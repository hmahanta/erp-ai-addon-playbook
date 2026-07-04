# Enterprise AI Prompt Framework v1.0

**Part 3 of 4**

---

# 5. AI Framework Integration (Continued)

## 5.5 CacheManager

**Purpose:** Provide Redis caching with TTL, serialization, and cache stampede protection.

```python
# frameworks/managers/cache_manager.py
import redis
import json
import pickle
from typing import Optional, Any, Callable, TypeVar
from datetime import timedelta
import hashlib
import logging
import time

logger = logging.getLogger(__name__)
T = TypeVar('T')

class CacheManager:
    """Redis caching with TTL and serialization"""
    
    _instance: Optional['CacheManager'] = None
    _redis: Optional[redis.Redis] = None
    
    def __new__(cls) -> 'CacheManager':
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def initialize(self, redis_url: str) -> None:
        """Initialize Redis connection"""
        self._redis = redis.from_url(redis_url, decode_responses=True)
        try:
            self._redis.ping()
            logger.info("Redis cache initialized")
        except Exception as e:
            logger.error(f"Failed to initialize Redis: {e}")
            raise
    
    def get(self, key: str, deserializer: Callable = json.loads) -> Optional[Any]:
        """Retrieve cached value"""
        try:
            value = self._redis.get(key)
            if value is None:
                return None
            return deserializer(value)
        except Exception as e:
            logger.error(f"Cache get failed for key {key}: {e}")
            return None
    
    def set(
        self,
        key: str,
        value: Any,
        ttl: Optional[timedelta] = None,
        serializer: Callable = json.dumps
    ) -> bool:
        """Set cached value with optional TTL"""
        try:
            serialized = serializer(value)
            if ttl:
                self._redis.setex(key, ttl, serialized)
            else:
                self._redis.set(key, serialized)
            logger.debug(f"Cache set for key {key}")
            return True
        except Exception as e:
            logger.error(f"Cache set failed for key {key}: {e}")
            return False
    
    def get_or_fetch(
        self,
        key: str,
        fetch_func: Callable[[], T],
        ttl: Optional[timedelta] = None
    ) -> T:
        """Get from cache or fetch with cache stampede protection"""
        # Try to get from cache
        cached = self.get(key)
        if cached is not None:
            return cached
        
        # Lock key to prevent thundering herd
        lock_key = f"{key}:lock"
        lock_acquired = self._redis.set(lock_key, "1", nx=True, ex=5)
        
        if lock_acquired:
            try:
                # Fetch data
                value = fetch_func()
                # Store in cache
                self.set(key, value, ttl)
                return value
            finally:
                self._redis.delete(lock_key)
        else:
            # Wait for other request to populate cache
            for _ in range(50):  # 5 second timeout
                cached = self.get(key)
                if cached is not None:
                    return cached
                time.sleep(0.1)
            # Timeout; fetch anyway
            return fetch_func()
    
    def delete(self, key: str) -> bool:
        """Delete cache entry"""
        return self._redis.delete(key) > 0
    
    def clear_prefix(self, prefix: str) -> int:
        """Delete all keys with given prefix"""
        keys = self._redis.keys(f"{prefix}*")
        if not keys:
            return 0
        return self._redis.delete(*keys)
    
    def health_check(self) -> bool:
        """Check if Redis is healthy"""
        try:
            self._redis.ping()
            return True
        except Exception as e:
            logger.error(f"Cache health check failed: {e}")
            return False

# Usage
cache_mgr = CacheManager()
cache_mgr.initialize("redis://localhost:6379/0")

# Simple get/set
cache_mgr.set("vendor:ACME", {"name": "ACME Corp"}, ttl=timedelta(hours=1))
vendor = cache_mgr.get("vendor:ACME")

# Get or fetch with cache stampede protection
def fetch_vendor(vendor_id: str):
    return repository.find_by_id(vendor_id)

vendor = cache_mgr.get_or_fetch(
    f"vendor:{vendor_id}",
    lambda: fetch_vendor(vendor_id),
    ttl=timedelta(hours=1)
)
```

## 5.6 HealthCheckManager

**Purpose:** Provide readiness and liveness probes for Kubernetes and cloud platforms.

```python
# frameworks/managers/health_check_manager.py
from typing import Dict, Callable, Optional
from enum import Enum
import logging

logger = logging.getLogger(__name__)

class HealthStatus(Enum):
    HEALTHY = "healthy"
    UNHEALTHY = "unhealthy"
    DEGRADED = "degraded"

class HealthCheckManager:
    """Manage health checks for Kubernetes probes"""
    
    _instance: Optional['HealthCheckManager'] = None
    _checks: Dict[str, Callable[[], bool]] = {}
    
    def __new__(cls) -> 'HealthCheckManager':
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def register_check(self, name: str, check_func: Callable[[], bool]) -> None:
        """Register a health check"""
        self._checks[name] = check_func
        logger.info(f"Registered health check: {name}")
    
    def readiness_check(self) -> Dict:
        """Check if service is ready to accept traffic"""
        results = {}
        all_ready = True
        
        for name, check_func in self._checks.items():
            try:
                results[name] = check_func()
                if not results[name]:
                    all_ready = False
            except Exception as e:
                logger.error(f"Health check {name} failed: {e}")
                results[name] = False
                all_ready = False
        
        return {
            'status': HealthStatus.HEALTHY.value if all_ready else HealthStatus.UNHEALTHY.value,
            'checks': results
        }
    
    def liveness_check(self) -> Dict:
        """Check if service is alive (minimal checks)"""
        # Usually just check if process is running
        return {'status': HealthStatus.HEALTHY.value}

# Usage
health_mgr = HealthCheckManager()
health_mgr.register_check("database", lambda: db_mgr.health_check())
health_mgr.register_check("cache", lambda: cache_mgr.health_check())

# In FastAPI routes
from fastapi import APIRouter

router = APIRouter(tags=["health"])

@router.get("/health/ready")
async def readiness_probe():
    return health_mgr.readiness_check()

@router.get("/health/live")
async def liveness_probe():
    return health_mgr.liveness_check()
```

## 5.7 ExceptionManager

**Purpose:** Centralized exception handling and recovery.

```python
# frameworks/managers/exception_manager.py
import logging
from typing import Type, Callable, Optional, Any
from functools import wraps

logger = logging.getLogger(__name__)

class ExceptionManager:
    """Centralized exception handling"""
    
    _instance: Optional['ExceptionManager'] = None
    _handlers: Dict[Type[Exception], Callable] = {}
    
    def __new__(cls) -> 'ExceptionManager':
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def register_handler(
        self,
        exception_class: Type[Exception],
        handler: Callable
    ) -> None:
        """Register handler for specific exception type"""
        self._handlers[exception_class] = handler
        logger.info(f"Registered handler for {exception_class.__name__}")
    
    def handle(self, exception: Exception) -> Any:
        """Handle exception using registered handler"""
        handler = self._handlers.get(type(exception))
        if handler:
            return handler(exception)
        # Default handling
        logger.error(f"Unhandled exception: {exception}", exc_info=True)
        raise
    
    def wrap(self, func: Callable) -> Callable:
        """Decorator to wrap function with exception handling"""
        @wraps(func)
        def wrapper(*args, **kwargs):
            try:
                return func(*args, **kwargs)
            except Exception as e:
                return self.handle(e)
        return wrapper

# Usage
exc_mgr = ExceptionManager()

def handle_validation_error(exc: ValidationError):
    logger.error(f"Validation error: {exc}")
    return {"error": str(exc), "status": 400}

exc_mgr.register_handler(ValidationError, handle_validation_error)

@exc_mgr.wrap
def process_invoice(invoice_id: str):
    # Will be wrapped with exception handling
    pass
```

## 5.8 AuthenticationManager

**Purpose:** JWT token validation and authentication.

```python
# frameworks/managers/authentication_manager.py
from jose import JWTError, jwt
from datetime import datetime, timedelta
from typing import Optional, Dict, Any
import logging

logger = logging.getLogger(__name__)

class AuthenticationManager:
    """JWT token management"""
    
    def __init__(self, secret_key: str, algorithm: str = "RS256"):
        self.secret_key = secret_key
        self.algorithm = algorithm
        self.access_token_expire_minutes = 15
    
    def create_access_token(self, data: Dict[str, Any]) -> str:
        """Create JWT access token"""
        to_encode = data.copy()
        expire = datetime.utcnow() + timedelta(
            minutes=self.access_token_expire_minutes
        )
        to_encode.update({"exp": expire})
        
        encoded_jwt = jwt.encode(
            to_encode,
            self.secret_key,
            algorithm=self.algorithm
        )
        return encoded_jwt
    
    def verify_token(self, token: str) -> Optional[Dict[str, Any]]:
        """Verify and decode JWT token"""
        try:
            payload = jwt.decode(
                token,
                self.secret_key,
                algorithms=[self.algorithm]
            )
            return payload
        except JWTError as e:
            logger.error(f"Token verification failed: {e}")
            return None
    
    def validate_request(self, token: str) -> bool:
        """Validate token from request"""
        if not token:
            return False
        return self.verify_token(token) is not None

# Usage in FastAPI
from fastapi import Depends, HTTPException
from fastapi.security import HTTPBearer

auth_mgr = AuthenticationManager(secret_key="your-secret-key")
security = HTTPBearer()

async def get_current_user(credentials = Depends(security)):
    token = credentials.credentials
    payload = auth_mgr.verify_token(token)
    if not payload:
        raise HTTPException(status_code=401, detail="Invalid token")
    return payload
```

## 5.9 EmailManager

**Purpose:** Send emails with templates and formatting.

```python
# frameworks/managers/email_manager.py
from typing import List, Optional
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
import smtplib
import logging

logger = logging.getLogger(__name__)

class EmailManager:
    """Email sending service"""
    
    def __init__(
        self,
        smtp_host: str,
        smtp_port: int,
        username: str,
        password: str
    ):
        self.smtp_host = smtp_host
        self.smtp_port = smtp_port
        self.username = username
        self.password = password
    
    def send(
        self,
        to: List[str],
        subject: str,
        body: str,
        html: Optional[str] = None
    ) -> bool:
        """Send email"""
        try:
            msg = MIMEMultipart("alternative")
            msg["Subject"] = subject
            msg["From"] = self.username
            msg["To"] = ", ".join(to)
            
            msg.attach(MIMEText(body, "plain"))
            if html:
                msg.attach(MIMEText(html, "html"))
            
            with smtplib.SMTP(self.smtp_host, self.smtp_port) as server:
                server.starttls()
                server.login(self.username, self.password)
                server.send_message(msg)
            
            logger.info(f"Email sent to {to}: {subject}")
            return True
        except Exception as e:
            logger.error(f"Failed to send email: {e}")
            return False
    
    def send_with_template(
        self,
        to: List[str],
        template_name: str,
        context: Dict[str, Any]
    ) -> bool:
        """Send email using template"""
        # Load template
        html = self._render_template(template_name, context)
        subject = context.get("subject", "")
        
        return self.send(to, subject, html, html)
    
    def _render_template(self, name: str, context: Dict) -> str:
        """Render email template (use Jinja2 in production)"""
        # Simplified; use Jinja2 in production
        pass

# Usage
email_mgr = EmailManager(
    smtp_host="smtp.example.com",
    smtp_port=587,
    username="noreply@example.com",
    password="password"
)

email_mgr.send(
    to=["user@example.com"],
    subject="Invoice Created",
    body="Your invoice has been created"
)
```

---

# 6. Enterprise Project Templates

## 6.1 FastAPI REST API Template

**Complete structure for a REST API microservice.**

```python
# main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from fastapi.responses import JSONResponse
from contextlib import asynccontextmanager
import logging

from config.settings import AppSettings
from frameworks.managers.configuration_manager import ConfigurationManager
from frameworks.managers.database_manager import DatabaseManager
from frameworks.managers.logging_manager import LoggingManager
from frameworks.managers.cache_manager import CacheManager
from frameworks.managers.health_check_manager import HealthCheckManager
from adapters.api.middleware.exception_handler import ExceptionHandlerMiddleware
from adapters.api.middleware.correlation_id import CorrelationIdMiddleware
from adapters.api.routes import invoices, vendors, health

logger = logging.getLogger(__name__)

# Initialize managers
config_mgr = ConfigurationManager.load()
db_mgr = DatabaseManager()
cache_mgr = CacheManager()
logging_mgr = LoggingManager()
health_mgr = HealthCheckManager()

@asynccontextmanager
async def lifespan(app: FastAPI):
    """Application lifecycle: startup and shutdown"""
    # Startup
    logger.info("Starting application...")
    db_mgr.initialize(config_mgr.get_database_config())
    cache_mgr.initialize(config_mgr.get_redis_config().url)
    health_mgr.register_check("database", db_mgr.health_check)
    health_mgr.register_check("cache", cache_mgr.health_check)
    logger.info("Application started")
    
    yield  # Application runs here
    
    # Shutdown
    logger.info("Shutting down application...")
    db_mgr.close()
    logger.info("Application shutdown complete")

# Create FastAPI app
app = FastAPI(
    title="Invoice Service",
    description="Enterprise invoice management API",
    version="1.0.0",
    lifespan=lifespan
)

# Add middleware
app.add_middleware(CORSMiddleware, allow_origins=["*"])
app.add_middleware(CorrelationIdMiddleware)
app.add_middleware(ExceptionHandlerMiddleware)

# Include routes
app.include_router(invoices.router)
app.include_router(vendors.router)
app.include_router(health.router)

@app.get("/", tags=["root"])
async def root():
    """API root endpoint"""
    return {
        "name": "Invoice Service",
        "version": "1.0.0",
        "status": "operational"
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(
        "main:app",
        host="0.0.0.0",
        port=8000,
        reload=config_mgr.config.debug
    )
```

## 6.2 Batch Job Template

**Template for long-running batch processing jobs.**

```python
# batch/jobs/invoice_reconciliation_job.py
from typing import List
import logging
from datetime import datetime
import time

logger = logging.getLogger(__name__)

class InvoiceReconciliationJob:
    """Batch job to reconcile invoices against payments"""
    
    def __init__(
        self,
        invoice_repo,
        payment_repo,
        logger_mgr,
        cache_mgr,
        batch_size: int = 1000
    ):
        self.invoice_repo = invoice_repo
        self.payment_repo = payment_repo
        self.logger_mgr = logger_mgr
        self.cache_mgr = cache_mgr
        self.batch_size = batch_size
    
    def execute(self) -> Dict[str, Any]:
        """Execute the reconciliation job"""
        start_time = datetime.utcnow()
        job_id = self._generate_job_id()
        
        try:
            logger.info(f"Starting reconciliation job {job_id}")
            
            # Get unreconciled invoices
            invoices = self.invoice_repo.find_unreconciled()
            
            # Process in batches
            reconciled_count = 0
            error_count = 0
            
            for batch in self._batch(invoices, self.batch_size):
                try:
                    for invoice in batch:
                        if self._reconcile_invoice(invoice):
                            reconciled_count += 1
                        else:
                            error_count += 1
                except Exception as e:
                    logger.error(f"Batch processing failed: {e}", exc_info=True)
                    error_count += len(batch)
            
            duration = (datetime.utcnow() - start_time).total_seconds()
            
            result = {
                'job_id': job_id,
                'status': 'success',
                'reconciled': reconciled_count,
                'errors': error_count,
                'duration_seconds': duration
            }
            
            logger.info(f"Job {job_id} completed: {result}")
            return result
            
        except Exception as e:
            logger.error(f"Job {job_id} failed: {e}", exc_info=True)
            return {
                'job_id': job_id,
                'status': 'failed',
                'error': str(e)
            }
    
    def _reconcile_invoice(self, invoice) -> bool:
        """Reconcile a single invoice"""
        # Get corresponding payment
        payment = self.payment_repo.find_by_invoice_id(invoice.id)
        
        if not payment:
            return False
        
        # Compare amounts
        if invoice.total_amount == payment.amount:
            invoice.mark_reconciled(payment.id)
            self.invoice_repo.save(invoice)
            return True
        
        return False
    
    def _batch(self, items: List, size: int):
        """Yield successive batches from items"""
        for i in range(0, len(items), size):
            yield items[i:i + size]
    
    def _generate_job_id(self) -> str:
        return f"JOB_{datetime.utcnow().strftime('%Y%m%d_%H%M%S')}"

# Scheduler
# batch/scheduler.py
from apscheduler.schedulers.background import BackgroundScheduler
import logging

logger = logging.getLogger(__name__)

class BatchScheduler:
    """Manage scheduled batch jobs"""
    
    def __init__(self):
        self.scheduler = BackgroundScheduler()
    
    def schedule_reconciliation_job(self, cron_expression: str):
        """Schedule reconciliation job with cron expression"""
        self.scheduler.add_job(
            self._run_reconciliation,
            'cron',
            hour=2,  # 2 AM daily
            minute=0
        )
    
    def _run_reconciliation(self):
        """Run reconciliation job"""
        job = InvoiceReconciliationJob(
            invoice_repo,
            payment_repo,
            logger_mgr,
            cache_mgr
        )
        result = job.execute()
        logger.info(f"Scheduled job result: {result}")
    
    def start(self):
        """Start scheduler"""
        self.scheduler.start()
        logger.info("Batch scheduler started")
    
    def shutdown(self):
        """Shutdown scheduler"""
        self.scheduler.shutdown()
        logger.info("Batch scheduler shutdown")
```

## 6.3 CLI Application Template

**Template for command-line tools.**

```python
# main.py (CLI)
import click
from typing import Optional
import logging

logger = logging.getLogger(__name__)

@click.group()
@click.option("--verbose", is_flag=True, help="Enable verbose logging")
def cli(verbose: bool):
    """Invoice Management CLI"""
    if verbose:
        logging.basicConfig(level=logging.DEBUG)
    else:
        logging.basicConfig(level=logging.INFO)

@cli.command()
@click.option("--vendor-id", required=True, help="Vendor ID")
@click.option("--amount", required=True, type=float, help="Invoice amount")
@click.option("--currency", default="USD", help="Currency code")
def create_invoice(vendor_id: str, amount: float, currency: str):
    """Create a new invoice"""
    try:
        # Initialize services
        service = InvoiceService(invoice_repo)
        
        # Create invoice
        invoice = service.create_invoice({
            'vendor_id': vendor_id,
            'amount': amount,
            'currency': currency
        })
        
        click.echo(f"Invoice created: {invoice.id}", fg='green'))
    except Exception as e:
        click.echo(f"Error: {e}", err=True, fg='red')

@cli.command()
@click.option("--invoice-id", required=True, help="Invoice ID")
def get_invoice(invoice_id: str):
    """Get invoice details"""
    try:
        service = InvoiceService(invoice_repo)
        invoice = service.get_invoice(invoice_id)
        
        click.echo(f"Invoice: {invoice.id}")
        click.echo(f"Vendor: {invoice.vendor_id}")
        click.echo(f"Amount: {invoice.total_amount}")
    except Exception as e:
        click.echo(f"Error: {e}", err=True, fg='red')

@cli.command()
def list_invoices():
    """List all invoices"""
    try:
        service = InvoiceService(invoice_repo)
        invoices = service.list_invoices()
        
        for invoice in invoices:
            click.echo(f"{invoice.id}: {invoice.vendor_id} - {invoice.total_amount}")
    except Exception as e:
        click.echo(f"Error: {e}", err=True, fg='red')

if __name__ == "__main__":
    cli()

# Usage
# $ python main.py --verbose create-invoice --vendor-id ACME --amount 1000
# $ python main.py get-invoice --invoice-id INV001
# $ python main.py list-invoices
```

---

# 7. Prompt Engineering Standards

## 7.1 How to Write Prompts for Claude

### 7.1.1 Structure for Code Generation Prompts

**Every code generation prompt should follow this structure:**

```
1. ROLE/CONTEXT
   - Who is Claude acting as?
   - What's the project context?

2. TASK
   - What specifically should Claude generate?
   - What's the scope?

3. CONSTRAINTS
   - What technologies are being used?
   - What patterns should be followed?
   - What must Claude NOT do?

4. OUTPUT FORMAT
   - How should the code be structured?
   - What file format(s)?
   - Level of detail needed?

5. EXAMPLES
   - Show good examples
   - Show bad examples (what to avoid)
```

### 7.1.2 Example: Well-Structured Prompt

```
ROLE:
You are a Senior Python Enterprise Architect with expertise in FastAPI,
Oracle databases, and clean architecture patterns. You are familiar with
the Enterprise AI Prompt Framework v1.0 (EAPF).

TASK:
Generate a complete InvoiceService class that:
1. Implements invoice creation with validation
2. Uses the repository pattern for data access
3. Includes proper exception handling
4. Includes comprehensive logging
5. Follows the EAPF standards for service layer classes

TECHNOLOGY STACK:
- Python 3.11
- FastAPI 0.110
- Oracle 19c database
- Pydantic for validation
- Python logging for structured logs

ARCHITECTURE CONSTRAINTS:
- Follow Clean Architecture principles
- Implement Dependency Injection (constructor-based)
- Use domain models (Invoice entity), not DTOs
- Use value objects for Amount, Currency
- No direct database access (use repositories)
- All business rules encoded in domain models

CODING STANDARDS:
- Full type hints on all methods
- Docstrings on all public methods
- Use dataclasses for value objects
- Use Enums for bounded values (InvoiceStatus)
- Exception handling: create custom exceptions, don't catch generic Exception

OUTPUT FORMAT:
Generate Python code with:
- Class definition
- __init__ with DI
- Each method on separate section
- Type hints and docstrings
- Inline code comments for complex logic
```

### 7.1.3 How to Continue Interrupted Sessions

**When Claude reaches token limit:**

```
[Previous conversation cut off. You were generating InvoiceService methods]

RESUMING FROM:
You had completed:
1. __init__ method
2. create_invoice() method
3. get_invoice() method

NEXT, GENERATE:
4. list_invoices() method
5. update_invoice() method
6. validate_invoice() private method
7. Complete with the _map_to_domain() method

Continue from where you left off without regenerating completed methods.
Include the closing of the class definition.
```

### 7.1.4 How to Split Large Projects

**Don't ask Claude to generate 10,000 lines of code in one session.**

**Instead, break into logical units:**

```
SESSION 1: Domain Models & Entities
- Generate invoice.py (domain entity)
- Generate value_objects.py (Amount, Currency)
- Generate repositories.py (abstract interfaces)

SESSION 2: Application Services
- Generate invoice_service.py
- Generate vendor_service.py
- Generate exception_manager.py

SESSION 3: API Routes & Adapters
- Generate routes/invoices.py (FastAPI endpoints)
- Generate database/oracle_invoice_repository.py
- Generate middleware/auth.py

SESSION 4: Tests
- Generate unit tests for domain
- Generate integration tests for API
- Generate fixtures and test data

SESSION 5: Database & Configuration
- Generate DDL (table definitions)
- Generate PL/SQL procedures
- Generate config files
```

### 7.1.5 How to Verify Generated Code

**Always verify before using:**

```
1. Check for imports:
   - Are all imports present?
   - Are there unused imports?
   - Are external libraries compatible?

2. Check syntax:
   - Run: python -m py_compile filename.py
   - No syntax errors?

3. Check type hints:
   - Run: mypy --strict filename.py
   - All types properly annotated?

4. Check for obvious bugs:
   - Unfinished methods? (pass statement?)
   - Missing return statements?
   - Logic errors?

5. Check patterns:
   - Does it follow EAPF standards?
   - Does it use the correct architectural patterns?
   - Are error cases handled?

6. Check security:
   - Are inputs validated?
   - Are there SQL injection risks?
   - Are secrets hardcoded?
```

### 7.1.6 How to Review Generated Code

**Code review checklist:**

```
ARCHITECTURE:
☐ Follows Clean Architecture principles?
☐ Uses correct layer (domain/application/adapter)?
☐ Respects dependency direction (inward)?
☐ Is properly separated from other concerns?

DESIGN:
☐ Single Responsibility Principle?
☐ No code duplication (DRY)?
☐ Appropriate use of design patterns?
☐ Clear responsibility boundaries?

READABILITY:
☐ Meaningful variable/function names?
☐ Adequate comments for complex logic?
☐ Appropriate docstrings?
☐ Consistent with team standards?

ROBUSTNESS:
☐ All error cases handled?
☐ Proper exception types?
☐ Input validation?
☐ Graceful degradation?

PERFORMANCE:
☐ No N+1 queries?
☐ Efficient algorithms?
☐ Proper use of caching?
☐ Memory efficient for large datasets?

SECURITY:
☐ No SQL injection risks?
☐ No hardcoded secrets?
☐ Proper input validation?
☐ Appropriate authorization checks?

TESTABILITY:
☐ Mockable dependencies?
☐ Isolated business logic?
☐ Clear test cases?

COMPLIANCE:
☐ Follows EAPF standards?
☐ Uses enterprise managers?
☐ Proper logging?
☐ Configuration from environment?
```

## 7.2 Prompt Engineering Best Practices

| Practice | Reason |
|----------|--------|
| **Be specific** | "Generate InvoiceService" vs "Generate a service" |
| **Include constraints** | Tell Claude what NOT to do (no hardcoding, no print statements) |
| **Show examples** | Include example code, error handling patterns, naming conventions |
| **Use roles** | "Act as a Senior Python Architect" sets expectations |
| **Specify technology versions** | "Python 3.11, FastAPI 0.110, Oracle 19c" |
| **Request full context** | Don't ask for snippets; request complete, runnable code |
| **Verify incrementally** | Generate one file/class at a time, verify, then continue |
| **Maintain state** | Keep the prompt window history for consistency |

---

**End of Part 3**

---

**To Continue:** Proceed to **Part 4**, which covers:
- Section 8: Master Prompt Template
- Section 9: Continuation Prompt Template
- Section 10: Resume Prompt Template
- Section 11: Verification Prompt
- Section 12: Production Readiness Prompt
- Sections 13-22: Oracle, Testing, Audits, and Checklists
