# Enterprise AI Prompt Framework v1.0

**Part 2 of 4**

---

# 3. Standard Enterprise Folder Structure

## 3.1 Recommended Project Layout

Every enterprise AI project follows this standardized folder structure:

```
project-root/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                    # GitHub Actions CI/CD pipeline
│   │   ├── tests.yml                 # Automated test execution
│   │   └── deploy.yml                # Deployment automation
│   └── ISSUE_TEMPLATE/
├── .docker/
│   ├── Dockerfile                    # Production Docker image
│   ├── Dockerfile.dev                # Development Docker image
│   └── .dockerignore                 # Exclude from image
├── .devcontainer/
│   └── devcontainer.json             # VS Code dev container config
├── src/
│   └── project_name/
│       ├── __init__.py               # Package marker
│       ├── main.py                   # Application entry point
│       ├── config/
│       │   ├── __init__.py
│       │   ├── settings.py           # Configuration classes
│       │   ├── secrets.py            # Secrets management
│       │   └── constants.py          # Application constants
│       ├── domain/
│       │   ├── __init__.py
│       │   ├── models/
│       │   │   ├── __init__.py
│       │   │   ├── invoice.py        # Domain entities
│       │   │   ├── vendor.py
│       │   │   └── value_objects.py  # Value objects
│       │   ├── exceptions.py         # Domain-specific exceptions
│       │   ├── enums.py              # Business enumerations
│       │   └── repositories.py       # Repository abstractions
│       ├── application/
│       │   ├── __init__.py
│       │   ├── use_cases/
│       │   │   ├── __init__.py
│       │   │   ├── create_invoice.py # Individual use case
│       │   │   ├── approve_invoice.py
│       │   │   └── search_invoices.py
│       │   ├── services/
│       │   │   ├── __init__.py
│       │   │   ├── invoice_service.py    # Service layer
│       │   │   ├── vendor_service.py
│       │   │   └── notification_service.py
│       │   ├── dto/
│       │   │   ├── __init__.py
│       │   │   ├── requests.py       # Request DTOs
│       │   │   └── responses.py      # Response DTOs
│       │   └── validators/
│       │       ├── __init__.py
│       │       ├── invoice_validator.py
│       │       └── common_validators.py
│       ├── adapters/
│       │   ├── __init__.py
│       │   ├── api/
│       │   │   ├── __init__.py
│       │   │   ├── routes/
│       │   │   │   ├── __init__.py
│       │   │   │   ├── invoices.py   # FastAPI route handlers
│       │   │   │   ├── vendors.py
│       │   │   │   └── health.py
│       │   │   ├── middleware/
│       │   │   │   ├── __init__.py
│       │   │   │   ├── correlation_id.py
│       │   │   │   ├── exception_handler.py
│       │   │   │   └── auth.py
│       │   │   ├── dependencies.py   # FastAPI dependencies
│       │   │   └── app.py            # FastAPI app creation
│       │   ├── database/
│       │   │   ├── __init__.py
│       │   │   ├── oracle_invoice_repository.py
│       │   │   ├── oracle_vendor_repository.py
│       │   │   ├── connection.py     # Connection management
│       │   │   └── transaction.py    # Transaction handling
│       │   ├── cache/
│       │   │   ├── __init__.py
│       │   │   └── redis_cache.py    # Redis implementation
│       │   └── external/
│       │       ├── __init__.py
│       │       ├── email_adapter.py  # Email service client
│       │       └── notification_adapter.py
│       ├── frameworks/
│       │   ├── __init__.py
│       │   ├── managers/
│       │   │   ├── __init__.py
│       │   │   ├── configuration_manager.py
│       │   │   ├── database_manager.py
│       │   │   ├── logging_manager.py
│       │   │   ├── exception_manager.py
│       │   │   ├── cache_manager.py
│       │   │   ├── health_check_manager.py
│       │   │   ├── scheduler_manager.py
│       │   │   ├── metrics_manager.py
│       │   │   └── monitoring_manager.py
│       │   ├── engines/
│       │   │   ├── __init__.py
│       │   │   ├── graph_engine.py   # Graph processing
│       │   │   ├── ml_engine.py      # Machine learning
│       │   │   ├── nlp_engine.py     # NLP processing
│       │   │   ├── ocr_engine.py     # OCR/document processing
│       │   │   ├── rule_engine.py    # Business rules
│       │   │   └── integration_engine.py
│       │   └── utilities/
│       │       ├── __init__.py
│       │       ├── decorators.py     # Reusable decorators
│       │       ├── context.py        # Context managers
│       │       └── helpers.py        # Utility functions
│       └── batch/
│           ├── __init__.py
│           ├── jobs/
│           │   ├── __init__.py
│           │   ├── invoice_reconciliation_job.py
│           │   └── audit_job.py
│           └── scheduler.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py                   # Pytest fixtures
│   ├── unit/
│   │   ├── __init__.py
│   │   ├── domain/
│   │   │   ├── __init__.py
│   │   │   ├── test_invoice.py
│   │   │   └── test_value_objects.py
│   │   ├── application/
│   │   │   ├── __init__.py
│   │   │   ├── test_invoice_service.py
│   │   │   └── test_validators.py
│   │   └── adapters/
│   │       ├── __init__.py
│   │       └── test_invoice_repository.py
│   ├── integration/
│   │   ├── __init__.py
│   │   ├── test_invoice_api.py       # API tests
│   │   ├── test_database.py          # DB tests
│   │   └── test_cache.py             # Cache tests
│   ├── performance/
│   │   ├── __init__.py
│   │   ├── test_query_performance.py
│   │   └── test_api_latency.py
│   └── fixtures/
│       ├── __init__.py
│       ├── sample_data.py
│       └── mocks.py
├── database/
│   ├── ddl/
│   │   ├── 01_tables.sql             # Table creation
│   │   ├── 02_sequences.sql          # Sequence creation
│   │   ├── 03_indexes.sql            # Index creation
│   │   └── 04_constraints.sql        # Constraint creation
│   ├── plsql/
│   │   ├── packages/
│   │   │   ├── pkg_invoice_mgmt.pks  # Package spec
│   │   │   └── pkg_invoice_mgmt.pkb  # Package body
│   │   ├── procedures/
│   │   │   └── p_reconcile_invoices.sql
│   │   ├── functions/
│   │   │   └── f_calculate_tax.sql
│   │   └── views/
│   │       └── v_invoice_summary.sql
│   ├── seeds/
│   │   ├── 01_lookup_data.sql        # Reference data
│   │   ├── 02_test_data.sql          # Sample data
│   │   └── data_setup.sql
│   └── migrations/
│       ├── V001__Initial_Schema.sql
│       ├── V002__Add_Audit_Columns.sql
│       └── V003__Create_Indexes.sql
├── docs/
│   ├── README.md                     # Project overview
│   ├── ARCHITECTURE.md               # Architecture decision records
│   ├── API.md                        # API documentation
│   ├── DATABASE.md                   # Database design
│   ├── DEPLOYMENT.md                 # Deployment guide
│   ├── DEVELOPMENT.md                # Development setup
│   └── DIAGRAMS/
│       ├── architecture.mmd          # Mermaid diagrams
│       ├── data_model.mmd
│       └── api_flow.mmd
├── scripts/
│   ├── setup_db.sh                   # Database setup
│   ├── run_tests.sh                  # Test automation
│   ├── run_linter.sh                 # Code linting
│   └── docker_build.sh               # Docker build
├── docker-compose.yml                # Local development environment
├── Dockerfile                        # Production Docker image
├── pyproject.toml                    # Python project configuration
├── setup.py                          # Package setup (legacy)
├── requirements.txt                  # Dependencies (pinned versions)
├── requirements-dev.txt              # Dev dependencies
├── .env.example                      # Environment variable template
├── .gitignore                        # Git ignore rules
├── .pylintrc                         # Pylint configuration
├── pytest.ini                        # Pytest configuration
├── mypy.ini                          # Type checking configuration
├── tox.ini                           # Testing automation
├── MANIFEST.in                       # Package manifest
└── LICENSE                           # Software license
```

## 3.2 Folder Responsibilities and Guidelines

### `/src/project_name/config/`

**Purpose:** Centralized configuration and secrets management

**Responsibilities:**
- Load environment variables
- Manage secrets (database credentials, API keys)
- Define configuration classes
- Validate configuration at startup

**Key Files:**

```python
# config/settings.py
from pydantic import BaseSettings

class DatabaseConfig(BaseSettings):
    """Oracle database configuration"""
    host: str = "localhost"
    port: int = 1521
    service: str = "XE"
    user: str
    password: str
    pool_min: int = 5
    pool_max: int = 20
    
    class Config:
        env_prefix = "DB_"

class AppSettings(BaseSettings):
    """Application settings"""
    debug: bool = False
    log_level: str = "INFO"
    version: str = "1.0.0"
    database: DatabaseConfig
    redis_url: str
    
    class Config:
        env_file = ".env"

# config/constants.py
class InvoiceConstants:
    DEFAULT_PAGE_SIZE = 20
    MAX_PAGE_SIZE = 100
    MAX_INVOICE_AMOUNT = Decimal("999999.99")
    ALLOWED_CURRENCIES = ["USD", "EUR", "GBP"]
```

**Ownership:** DevOps / Platform team defines; application team uses

### `/src/project_name/domain/`

**Purpose:** Business domain models and rules

**Responsibilities:**
- Define domain entities and value objects
- Encode business rules in code
- Define repository interfaces (not implementations)
- Domain-specific exceptions

**Key Files:**

```python
# domain/models/invoice.py
from dataclasses import dataclass
from enum import Enum

class InvoiceStatus(Enum):
    DRAFT = "DRAFT"
    SUBMITTED = "SUBMITTED"
    APPROVED = "APPROVED"
    PAID = "PAID"

@dataclass
class Invoice:
    """Domain entity: represents a business invoice"""
    invoice_id: str
    vendor_id: str
    total_amount: Amount
    status: InvoiceStatus
    
    def approve(self) -> None:
        """Enforce business rule: can only approve SUBMITTED invoices"""
        if self.status != InvoiceStatus.SUBMITTED:
            raise InvalidStatusTransitionError(
                f"Cannot approve invoice in {self.status} status"
            )
        self.status = InvoiceStatus.APPROVED

# domain/repositories.py (Abstract - implementation in adapters/)
from abc import ABC, abstractmethod

class InvoiceRepository(ABC):
    @abstractmethod
    def save(self, invoice: Invoice) -> str: ...
    @abstractmethod
    def find_by_id(self, invoice_id: str) -> Invoice: ...
```

**Ownership:** Business Analysts, Domain Experts, Architects

**Best Practices:**
- No external dependencies (no imports from adapters or frameworks)
- Business rules are explicit in methods
- Use value objects for data validation
- Use enums for bounded domains (statuses, types)

### `/src/project_name/application/`

**Purpose:** Use cases and business logic coordination

**Responsibilities:**
- Implement use cases (e.g., "Create Invoice")
- Coordinate repositories and services
- Implement business workflows
- Validation and error handling
- DTOs (Data Transfer Objects) for API communication

**Key Files:**

```python
# application/use_cases/create_invoice.py
class CreateInvoiceUseCase:
    def __init__(
        self,
        invoice_repo: InvoiceRepository,
        vendor_repo: VendorRepository,
        logger: LoggingManager
    ):
        self.invoice_repo = invoice_repo
        self.vendor_repo = vendor_repo
        self.logger = logger
    
    def execute(self, request: CreateInvoiceRequest) -> CreateInvoiceResponse:
        # Validate vendor exists
        vendor = self.vendor_repo.find_by_id(request.vendor_id)
        if not vendor:
            raise VendorNotFound(f"Vendor {request.vendor_id} not found")
        
        # Create domain model
        invoice = Invoice.create(request.vendor_id, request.amount)
        
        # Persist
        invoice_id = self.invoice_repo.save(invoice)
        
        # Log
        self.logger.info("Invoice created", extra={'invoice_id': invoice_id})
        
        return CreateInvoiceResponse(invoice_id=invoice_id)
```

**Ownership:** Feature teams (develop specific use cases)

### `/src/project_name/adapters/api/`

**Purpose:** HTTP API layer (FastAPI routes and middleware)

**Responsibilities:**
- FastAPI route handlers
- Request/response conversion
- Authentication and authorization
- Exception handling and error responses
- API documentation

**Key Files:**

```python
# adapters/api/routes/invoices.py
from fastapi import APIRouter, Depends, HTTPException
from fastapi.responses import JSONResponse

router = APIRouter(prefix="/invoices", tags=["invoices"])

@router.post("", response_model=InvoiceResponse)
async def create_invoice(
    request: CreateInvoiceRequest,
    service: InvoiceService = Depends(get_invoice_service)
) -> InvoiceResponse:
    """Create a new invoice"""
    try:
        response = service.create_invoice(request)
        return response
    except VendorNotFound as e:
        raise HTTPException(status_code=404, detail=str(e))
    except ValidationError as e:
        raise HTTPException(status_code=400, detail=str(e))

@router.get("/{invoice_id}", response_model=InvoiceResponse)
async def get_invoice(
    invoice_id: str,
    service: InvoiceService = Depends(get_invoice_service)
) -> InvoiceResponse:
    """Retrieve an invoice by ID"""
    invoice = service.get_invoice(invoice_id)
    if not invoice:
        raise HTTPException(status_code=404, detail="Invoice not found")
    return InvoiceResponse.from_domain(invoice)
```

**Ownership:** API team

**Best Practices:**
- Route handlers are thin (delegate to services)
- Validate input with Pydantic
- Convert domain models to DTOs for responses
- Clear error messages and HTTP status codes

### `/src/project_name/adapters/database/`

**Purpose:** Database persistence implementation

**Responsibilities:**
- Implement repository interfaces
- Execute SQL queries
- Connection management
- Transaction handling

**Key Files:**

```python
# adapters/database/oracle_invoice_repository.py
class OracleInvoiceRepository(InvoiceRepository):
    def __init__(self, connection_pool: OracleConnectionPool):
        self.connection_pool = connection_pool
    
    def save(self, invoice: Invoice) -> str:
        with self.connection_pool.get_connection() as conn:
            cursor = conn.cursor()
            cursor.execute(
                """
                INSERT INTO inv_invoices (invoice_id, vendor_id, amount, status)
                VALUES (:1, :2, :3, :4)
                """,
                [invoice.invoice_id, invoice.vendor_id, 
                 invoice.total_amount.value, invoice.status.value]
            )
            conn.commit()
            return invoice.invoice_id
    
    def find_by_id(self, invoice_id: str) -> Optional[Invoice]:
        with self.connection_pool.get_connection() as conn:
            cursor = conn.cursor()
            cursor.execute(
                "SELECT invoice_id, vendor_id, amount, status FROM inv_invoices WHERE invoice_id = :1",
                [invoice_id]
            )
            row = cursor.fetchone()
            if not row:
                return None
            return self._map_to_domain(row)
```

**Ownership:** Data team, DBAs

**Best Practices:**
- Use bind variables (prevent SQL injection)
- Map database rows to domain models
- Handle connection pool properly
- Log all queries in debug mode

### `/src/project_name/frameworks/managers/`

**Purpose:** Reusable enterprise components (shared across projects)

**Responsibilities:**
- Configuration management
- Database connection management
- Logging and monitoring
- Health checks
- Caching
- Exception management
- Scheduling

**Examples:** See Section 5 (AI Framework Integration)

**Ownership:** Platform/Infrastructure team

### `/tests/`

**Purpose:** Automated tests

**Responsibilities:**
- Unit tests (isolated domain and application logic)
- Integration tests (API endpoints, database)
- Performance tests (query optimization, API latency)
- Fixtures and test data

**Key Files:**

```python
# tests/unit/domain/test_invoice.py
import pytest
from src.project_name.domain.models import Invoice, InvoiceStatus

def test_invoice_approval():
    invoice = Invoice(
        invoice_id="INV001",
        vendor_id="VEND001",
        status=InvoiceStatus.SUBMITTED
    )
    invoice.approve()
    assert invoice.status == InvoiceStatus.APPROVED

def test_invoice_cannot_approve_draft():
    invoice = Invoice(
        invoice_id="INV001",
        vendor_id="VEND001",
        status=InvoiceStatus.DRAFT
    )
    with pytest.raises(InvalidStatusTransitionError):
        invoice.approve()
```

**Ownership:** QA, developers

### `/database/`

**Purpose:** Oracle database artifacts

**Responsibilities:**
- DDL (table, sequence, index definitions)
- PL/SQL packages, procedures, functions
- Views and materialized views
- Migration scripts
- Seed data

**Key Files:**

```sql
-- database/ddl/01_tables.sql
CREATE TABLE inv_invoices (
    invoice_id VARCHAR2(50) PRIMARY KEY,
    vendor_id VARCHAR2(50) NOT NULL,
    invoice_number VARCHAR2(50) NOT NULL,
    total_amount NUMBER(12, 2) NOT NULL,
    currency VARCHAR2(3) DEFAULT 'USD',
    status VARCHAR2(20) DEFAULT 'DRAFT',
    created_date TIMESTAMP DEFAULT SYSTIMESTAMP,
    CONSTRAINT fk_vendor FOREIGN KEY (vendor_id) REFERENCES vendors(vendor_id)
);

-- database/plsql/packages/pkg_invoice_mgmt.pks
CREATE OR REPLACE PACKAGE pkg_invoice_mgmt IS
    PROCEDURE p_create_invoice(
        p_vendor_id IN VARCHAR2,
        p_amount IN NUMBER,
        p_invoice_id OUT VARCHAR2
    );
END pkg_invoice_mgmt;
```

**Ownership:** DBA, Data team

---

# 4. Python Enterprise Standards

## 4.1 Python Version and Environment

**Minimum Python Version:** 3.11

**Reasoning:**
- Pattern matching (structural recursion)
- Type hints improvements
- Performance enhancements
- Long-term support until October 2027

**Environment Management:**

```bash
# Use pyenv to manage Python versions
pyenv install 3.11.0
pyenv local 3.11.0

# Use venv for project isolation
python -m venv venv
source venv/bin/activate  # macOS/Linux
# or
venv\Scripts\activate  # Windows

# Pin dependencies
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
```

## 4.2 Type Hints

**Requirement:** All functions must have type hints for parameters and return values.

```python
# ❌ WRONG: No type hints
def calculate_invoice_total(line_items):
    return sum(item['amount'] for item in line_items)

# ✅ CORRECT: Full type hints
from typing import List, Dict
from decimal import Decimal

def calculate_invoice_total(line_items: List[Dict[str, Decimal]]) -> Decimal:
    """Calculate total amount from line items"""
    return sum(item['amount'] for item in line_items)

# ✅ EVEN BETTER: Use dataclasses for clarity
from dataclasses import dataclass

@dataclass
class LineItem:
    description: str
    quantity: int
    unit_price: Decimal

def calculate_invoice_total(line_items: List[LineItem]) -> Decimal:
    """Calculate total amount from line items"""
    return sum(item.unit_price * item.quantity for item in line_items)
```

**Generic Types:**

```python
from typing import List, Dict, Optional, Union, Tuple, Callable

# List of strings
vendors: List[str] = ["ACME", "Widget Corp"]

# Dictionary mapping string to int
invoice_counts: Dict[str, int] = {"ACME": 5, "Widget": 3}

# Optional value (can be None)
vendor_name: Optional[str] = None

# Union of types
identifier: Union[str, int] = "INV001"  # Can be either

# Tuple with specific types
coordinates: Tuple[float, float] = (37.7749, -122.4194)

# Callable function type
processor: Callable[[str], bool] = lambda x: x.startswith("INV")
```

**Run Type Checking:**

```bash
# Install mypy
pip install mypy

# Check types
mypy src/

# With strict mode
mypy --strict src/
```

## 4.3 Dataclasses

**Use dataclasses for value objects and DTOs.** They're cleaner than classes and provide equality, hashing, and representation automatically.

```python
from dataclasses import dataclass, field
from typing import List
from decimal import Decimal
from datetime import datetime

@dataclass
class Amount:
    """Value object: immutable money amount"""
    value: Decimal
    currency: str = "USD"
    
    def __post_init__(self):
        if self.value < 0:
            raise ValueError("Amount cannot be negative")
        if len(self.currency) != 3:
            raise ValueError("Currency must be 3-letter code")

@dataclass(frozen=True)  # Immutable
class LineItem:
    """Line item on an invoice"""
    description: str
    quantity: int
    unit_price: Amount

@dataclass
class Invoice:
    """Invoice domain entity"""
    invoice_id: str
    vendor_id: str
    line_items: List[LineItem]
    created_date: datetime = field(default_factory=datetime.now)
    
    def total(self) -> Amount:
        return sum(
            (item.unit_price.value * item.quantity for item in self.line_items),
            Amount(Decimal(0))
        )

# Usage
invoice = Invoice(
    invoice_id="INV001",
    vendor_id="VEND001",
    line_items=[
        LineItem("Widget", 10, Amount(Decimal("99.99")))
    ]
)

print(invoice)  # Automatic __repr__
assert invoice == Invoice(...)  # Automatic __eq__
```

## 4.4 Enums

**Use enums for bounded values** (statuses, types, categories). Never use magic strings.

```python
from enum import Enum, auto

# Simple enum
class InvoiceStatus(Enum):
    DRAFT = "DRAFT"
    SUBMITTED = "SUBMITTED"
    APPROVED = "APPROVED"
    PAID = "PAID"
    CANCELLED = "CANCELLED"

# Auto-generated values
class Priority(Enum):
    LOW = auto()        # 1
    MEDIUM = auto()     # 2
    HIGH = auto()       # 3
    CRITICAL = auto()   # 4

# Usage
status = InvoiceStatus.APPROVED
assert status.value == "APPROVED"
assert status.name == "APPROVED"

# Iteration
for status in InvoiceStatus:
    print(status.name, status.value)

# Convert string to enum
status = InvoiceStatus["DRAFT"]  # by name
status = InvoiceStatus("DRAFT")   # by value
```

## 4.5 Context Managers

**Use context managers for resource management** (database connections, file handles, transactions).

```python
from contextlib import contextmanager
from typing import Iterator

# Define a context manager
@contextmanager
def database_transaction() -> Iterator:
    """Provide a database transaction context"""
    conn = get_connection()
    try:
        yield conn
        conn.commit()
    except Exception:
        conn.rollback()
        raise
    finally:
        conn.close()

# Usage
with database_transaction() as conn:
    conn.execute("INSERT INTO invoices ...")
    conn.execute("UPDATE vendors SET ...")
    # Auto-commits on success, auto-rollbacks on exception

# Class-based context manager
class InvoiceRepository:
    def __enter__(self):
        self.conn = get_connection()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.conn.close()
        return False

# Usage
with InvoiceRepository() as repo:
    invoices = repo.find_all()
```

## 4.6 Exception Hierarchy

**Define a clear exception hierarchy.** Base all custom exceptions on a project-specific root.

```python
# exceptions.py
class ProjectException(Exception):
    """Base exception for all project-specific errors"""
    pass

class ValidationException(ProjectException):
    """Raised when input validation fails"""
    pass

class InvoiceValidationException(ValidationException):
    """Raised when invoice validation fails"""
    pass

class VendorNotFoundError(ProjectException):
    """Raised when vendor is not found"""
    pass

class DatabaseException(ProjectException):
    """Raised when database operation fails"""
    pass

class TransactionFailedError(DatabaseException):
    """Raised when transaction cannot commit"""
    pass

# Usage
try:
    invoice = Invoice.create(request.data)
except InvoiceValidationException as e:
    logger.error(f"Validation failed: {e}")
    raise HTTPException(status_code=400, detail=str(e))
except VendorNotFoundError as e:
    logger.error(f"Vendor not found: {e}")
    raise HTTPException(status_code=404, detail=str(e))
except ProjectException as e:
    logger.error(f"Unexpected error: {e}")
    raise HTTPException(status_code=500, detail="Internal server error")
```

## 4.7 Decorators

**Use decorators for cross-cutting concerns** (logging, caching, retry logic, performance metrics).

```python
from functools import wraps
from typing import Any, Callable
import time
import logging

logger = logging.getLogger(__name__)

# Timing decorator
def measure_duration(func: Callable) -> Callable:
    """Log function execution duration"""
    @wraps(func)
    def wrapper(*args, **kwargs) -> Any:
        start = time.time()
        try:
            return func(*args, **kwargs)
        finally:
            duration_ms = (time.time() - start) * 1000
            logger.info(f"{func.__name__} took {duration_ms:.2f}ms")
    return wrapper

# Retry decorator with exponential backoff
def retry(max_attempts: int = 3, backoff: float = 2.0):
    """Retry on exception with exponential backoff"""
    def decorator(func: Callable) -> Callable:
        @wraps(func)
        def wrapper(*args, **kwargs) -> Any:
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts:
                        raise
                    wait_time = backoff ** (attempt - 1)
                    logger.warning(
                        f"{func.__name__} failed (attempt {attempt}/{max_attempts}), "
                        f"retrying in {wait_time}s: {e}"
                    )
                    time.sleep(wait_time)
        return wrapper
    return decorator

# Usage
@measure_duration
@retry(max_attempts=3, backoff=2.0)
def call_external_api() -> dict:
    """Call external API with retry and duration logging"""
    # Implementation
    pass
```

## 4.8 Generators

**Use generators for large datasets** to avoid loading everything into memory.

```python
from typing import Generator, Iterator

# ❌ WRONG: Loads all invoices into memory
def get_all_invoices() -> List[Invoice]:
    invoices = []
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM invoices")
    for row in cursor.fetchall():  # Loads all rows
        invoices.append(Invoice.from_row(row))
    return invoices

# ✅ CORRECT: Yields one at a time
def get_all_invoices() -> Generator[Invoice, None, None]:
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM invoices")
    for row in cursor.fetchall():  # Memory-efficient
        yield Invoice.from_row(row)

# Usage
for invoice in get_all_invoices():
    process_invoice(invoice)  # One at a time
```

## 4.9 Memory Optimization

**Key Rules:**

| Rule | Reason |
|------|--------|
| Use generators for large sequences | Avoid loading all data into memory |
| Use `__slots__` for classes created in bulk | Reduce memory overhead |
| Cache expensive computations | Don't repeat work |
| Delete large objects when done | Trigger garbage collection |
| Use `array.array` for homogenous numeric data | More efficient than lists |

```python
# Use __slots__ for memory efficiency
class LineItem:
    __slots__ = ('description', 'quantity', 'unit_price')
    
    def __init__(self, description, quantity, unit_price):
        self.description = description
        self.quantity = quantity
        self.unit_price = unit_price

# Batch processing with generator
def process_invoices_batch(batch_size: int = 1000) -> None:
    invoices = get_all_invoices()  # Generator, not list
    batch = []
    for invoice in invoices:
        batch.append(invoice)
        if len(batch) >= batch_size:
            save_batch(batch)
            batch = []  # Clear for next batch
    if batch:
        save_batch(batch)  # Process remaining
```

## 4.10 Logging Standards

**All applications must use structured logging.** Never use `print()`.

```python
import logging
from logging.config import dictConfig

# Configure structured logging
LOGGING_CONFIG = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "default": {
            "format": "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
        },
        "json": {
            "()": "pythonjsonlogger.jsonlogger.JsonFormatter",
            "format": "%(asctime)s %(name)s %(levelname)s %(message)s"
        }
    },
    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "formatter": "json",
            "level": "INFO"
        },
        "file": {
            "class": "logging.handlers.RotatingFileHandler",
            "filename": "app.log",
            "maxBytes": 10485760,  # 10MB
            "backupCount": 5,
            "formatter": "json"
        }
    },
    "root": {
        "level": "INFO",
        "handlers": ["console", "file"]
    }
}

dictConfig(LOGGING_CONFIG)
logger = logging.getLogger(__name__)

# Usage with structured context
logger.info(
    "Invoice created",
    extra={
        'invoice_id': 'INV001',
        'vendor_id': 'VEND001',
        'amount': 1000.00,
        'correlation_id': request.correlation_id
    }
)

logger.error(
    "Invoice processing failed",
    exc_info=True,  # Include stack trace
    extra={
        'invoice_id': 'INV001',
        'error_code': 'VALIDATION_FAILED'
    }
)
```

## 4.11 Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Packages | lowercase, underscores | `payment_service` |
| Modules | lowercase, underscores | `invoice_repository.py` |
| Classes | PascalCase | `InvoiceService` |
| Functions | snake_case | `calculate_invoice_total` |
| Constants | UPPER_SNAKE_CASE | `MAX_INVOICE_AMOUNT` |
| Private/Internal | leading underscore | `_internal_method` |
| Boolean | is/has/can prefix | `is_active`, `has_items` |
| Attributes | snake_case | `vendor_id` |

```python
# Good naming
class InvoiceService:
    MAX_RETRIES = 3
    
    def __init__(self, repository: InvoiceRepository):
        self._repository = repository
    
    def create_invoice(self, request: CreateInvoiceRequest) -> Invoice:
        # Implementation
        pass
    
    def _validate_amount(self, amount: Decimal) -> bool:
        return amount > Decimal(0)
    
    @property
    def is_healthy(self) -> bool:
        return self._repository.is_connected
```

---

# 5. AI Framework Integration

## 5.1 Overview of Enterprise Managers

Enterprise AI applications require common functionality that every project needs:

| Manager | Purpose | When to Use |
|---------|---------|------------|
| **ConfigurationManager** | Load and validate environment configuration | Application startup |
| **DatabaseManager** | Connection pooling, lifecycle management | Every database operation |
| **LoggingManager** | Structured logging with correlation IDs | Every business operation |
| **HealthCheckManager** | Readiness/liveness probes for Kubernetes | Health endpoints |
| **ExceptionManager** | Centralized exception handling and recovery | Error handling |
| **CacheManager** | Redis caching with TTL and serialization | Frequently accessed data |
| **SchedulerManager** | Distributed job scheduling | Batch operations |
| **MetricsManager** | Prometheus metrics collection | Performance monitoring |
| **MonitoringManager** | APM integration, alerts | Production monitoring |
| **AuthenticationManager** | JWT validation, token management | API security |
| **EmailManager** | Email sending with templates | Notifications |
| **NotificationManager** | Multi-channel notifications (email, SMS, etc.) | User communications |

## 5.2 ConfigurationManager

**Purpose:** Load, validate, and provide centralized access to application configuration.

```python
# frameworks/managers/configuration_manager.py
from pydantic import BaseSettings, validator
from typing import Optional
from enum import Enum

class Environment(str, Enum):
    DEVELOPMENT = "development"
    STAGING = "staging"
    PRODUCTION = "production"

class DatabaseConfig(BaseSettings):
    """Oracle database configuration"""
    host: str
    port: int = 1521
    service: str
    user: str
    password: str
    pool_min: int = 5
    pool_max: int = 20
    
    @validator('service')
    def validate_service(cls, v):
        if not v:
            raise ValueError("Service name required")
        return v
    
    class Config:
        env_prefix = "DB_"

class RedisConfig(BaseSettings):
    """Redis cache configuration"""
    host: str = "localhost"
    port: int = 6379
    db: int = 0
    password: Optional[str] = None
    ssl: bool = False
    
    class Config:
        env_prefix = "REDIS_"

class ApplicationConfig(BaseSettings):
    """Complete application configuration"""
    environment: Environment = Environment.DEVELOPMENT
    debug: bool = False
    version: str = "1.0.0"
    log_level: str = "INFO"
    database: DatabaseConfig
    redis: RedisConfig
    
    class Config:
        env_file = ".env"
        case_sensitive = False

class ConfigurationManager:
    """Singleton configuration manager"""
    _instance: Optional['ConfigurationManager'] = None
    _config: Optional[ApplicationConfig] = None
    
    def __new__(cls) -> 'ConfigurationManager':
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    @classmethod
    def load(cls, env_file: Optional[str] = None) -> 'ConfigurationManager':
        """Load configuration from environment"""
        instance = cls()
        instance._config = ApplicationConfig(_env_file=env_file)
        return instance
    
    @property
    def config(self) -> ApplicationConfig:
        if self._config is None:
            raise RuntimeError("Configuration not loaded. Call load() first.")
        return self._config
    
    def get_database_config(self) -> DatabaseConfig:
        return self.config.database
    
    def get_redis_config(self) -> RedisConfig:
        return self.config.redis
    
    def is_production(self) -> bool:
        return self.config.environment == Environment.PRODUCTION

# Usage
config_mgr = ConfigurationManager.load()
db_config = config_mgr.get_database_config()
is_prod = config_mgr.is_production()
```

## 5.3 DatabaseManager

**Purpose:** Manage database connection pools and provide connections on-demand.

```python
# frameworks/managers/database_manager.py
import cx_Oracle
from typing import Optional, Iterator
from contextlib import contextmanager
import logging

logger = logging.getLogger(__name__)

class DatabaseManager:
    """Manages Oracle database connections"""
    
    _instance: Optional['DatabaseManager'] = None
    
    def __new__(cls) -> 'DatabaseManager':
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance._pool = None
        return cls._instance
    
    def initialize(self, config: DatabaseConfig) -> None:
        """Initialize connection pool"""
        try:
            self._pool = cx_Oracle.create_pool(
                user=config.user,
                password=config.password,
                dsn=f"{config.host}:{config.port}/{config.service}",
                min=config.pool_min,
                max=config.pool_max,
                threaded=True
            )
            logger.info(f"Database pool initialized: {config.pool_min}-{config.pool_max}")
        except Exception as e:
            logger.error(f"Failed to initialize database pool: {e}")
            raise
    
    @contextmanager
    def get_connection(self) -> Iterator:
        """Get a connection from the pool"""
        if not self._pool:
            raise RuntimeError("Database not initialized. Call initialize() first.")
        
        conn = self._pool.acquire()
        try:
            yield conn
        except Exception as e:
            logger.error(f"Database operation failed: {e}")
            conn.rollback()
            raise
        finally:
            conn.close()
    
    def execute(self, sql: str, params: list = None) -> list:
        """Execute a query and return results"""
        with self.get_connection() as conn:
            cursor = conn.cursor()
            cursor.execute(sql, params or [])
            return cursor.fetchall()
    
    def health_check(self) -> bool:
        """Check if database is healthy"""
        try:
            with self.get_connection() as conn:
                cursor = conn.cursor()
                cursor.execute("SELECT 1 FROM dual")
                return True
        except Exception as e:
            logger.error(f"Database health check failed: {e}")
            return False
    
    def close(self) -> None:
        """Close the connection pool"""
        if self._pool:
            self._pool.close()
            logger.info("Database pool closed")

# Usage
db_mgr = DatabaseManager()
db_mgr.initialize(config_mgr.get_database_config())

with db_mgr.get_connection() as conn:
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM invoices WHERE vendor_id = :1", [vendor_id])
    invoices = cursor.fetchall()
```

## 5.4 LoggingManager

**Purpose:** Provide structured, centralized logging with correlation IDs.

```python
# frameworks/managers/logging_manager.py
import logging
import json
from typing import Optional, Any, Dict
from datetime import datetime
from pythonjsonlogger import jsonlogger

class LoggingManager:
    """Structured logging with correlation IDs"""
    
    _instance: Optional['LoggingManager'] = None
    _correlation_id: Optional[str] = None
    
    def __new__(cls) -> 'LoggingManager':
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance._setup_logging()
        return cls._instance
    
    def _setup_logging(self) -> None:
        """Configure structured JSON logging"""
        self.logger = logging.getLogger("app")
        handler = logging.StreamHandler()
        formatter = jsonlogger.JsonFormatter(
            '%(timestamp)s %(level)s %(message)s %(correlation_id)s'
        )
        handler.setFormatter(formatter)
        self.logger.addHandler(handler)
        self.logger.setLevel(logging.INFO)
    
    def set_correlation_id(self, correlation_id: str) -> None:
        """Set correlation ID for request tracing"""
        self._correlation_id = correlation_id
    
    def info(
        self,
        message: str,
        extra: Optional[Dict[str, Any]] = None
    ) -> None:
        """Log info level message"""
        self._log(logging.INFO, message, extra)
    
    def error(
        self,
        message: str,
        exc_info: bool = False,
        extra: Optional[Dict[str, Any]] = None
    ) -> None:
        """Log error level message"""
        self._log(logging.ERROR, message, extra, exc_info=exc_info)
    
    def _log(
        self,
        level: int,
        message: str,
        extra: Optional[Dict[str, Any]] = None,
        exc_info: bool = False
    ) -> None:
        """Internal logging method"""
        log_extra = {
            'timestamp': datetime.utcnow().isoformat(),
            'correlation_id': self._correlation_id or 'N/A'
        }
        if extra:
            log_extra.update(extra)
        
        self.logger.log(level, message, extra=log_extra, exc_info=exc_info)

# Usage
logger = LoggingManager()
logger.set_correlation_id(request.headers.get("X-Correlation-ID"))
logger.info(
    "Invoice created",
    extra={'invoice_id': 'INV001', 'vendor_id': 'VEND001'}
)
```

---

**End of Part 2**

---

**To Continue:** Proceed to **Part 3**, which covers:
- Section 5 (continued): Cache, Health Check, Exception, and other managers
- Section 6: Enterprise Project Templates
- Section 7: Prompt Engineering Standards
- Section 8: Master Prompt Template
