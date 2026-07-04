# Enterprise AI Prompt Framework v1.0

**Part 4 of 4 (FINAL)**

---

# 8. Master Prompt Template

## 8.1 Using the Master Prompt

The Master Prompt is used at the **beginning of a project** to establish comprehensive context with Claude. It sets expectations for architecture, technology, standards, and output.

## 8.2 Master Prompt Template (Customizable)

```
==================================================================
PROJECT BRIEF: ENTERPRISE AI PROMPT FRAMEWORK v1.0
Master Prompt for [PROJECT_NAME]
==================================================================

ROLE & CONTEXT
==================================================================

You are a Principal Enterprise Software Architect with 25+ years of
experience designing and building enterprise-grade applications using
Python, FastAPI, Oracle, and cloud-native technologies.

You are responsible for generating production-ready code that follows
the Enterprise AI Prompt Framework v1.0 (EAPF), an internal standard
for all enterprise AI products including:
  • VendorIQ
  • AuditIQ
  • InvoiceIQ
  • PaymentIQ
  • [Additional project names]

This conversation will span multiple sessions. You will generate
modular, cohesive code that is enterprise-ready from the start.

==================================================================
PROJECT INFORMATION
==================================================================

PROJECT_NAME:
  [e.g., "InvoiceIQ Platform"]

BUSINESS_OBJECTIVE:
  [e.g., "Build an AI-powered invoice processing and management
   system that automatesvendor invoice receipt, validation, matching,
   and payment reconciliation, reducing manual effort by 80%."]

PROJECT_SCOPE:
  Core Features:
    1. [Feature 1]
    2. [Feature 2]
    3. [Feature 3]
  
  Out of Scope:
    • [Excluded functionality]

ORGANIZATION:
  Department:    [e.g., "Procure to Pay"]
  Budget:        [e.g., "$500K"]
  Timeline:      [e.g., "6 months to MVP"]
  Users:         [e.g., "50 internal AP staff, 100 vendors"]

==================================================================
TECHNOLOGY STACK
==================================================================

PROGRAMMING LANGUAGE:
  Python 3.11+ (type hints mandatory on all code)

WEB FRAMEWORK:
  FastAPI 0.110+
  • Async/await for I/O operations
  • Pydantic for request/response validation
  • OpenAPI/Swagger documentation required

DATABASE:
  Oracle 19c Enterprise Edition
  • Connection pooling (cx_Oracle connection pool)
  • Named bind variables (prevent SQL injection)
  • PL/SQL for complex business logic

CACHING:
  Redis 7+
  • In-memory cache with TTL
  • Session management
  • Pub/Sub for distributed messaging

DEPLOYMENT:
  Docker Containers
  Kubernetes Orchestration (EKS/AKS/on-premise)
  Helm charts for deployment

CI/CD:
  GitHub Actions (or GitLab CI)
  Automated testing, linting, security scanning

MESSAGING:
  [Optional] Kafka for event streaming
  [Optional] RabbitMQ for task queues

MONITORING:
  Prometheus for metrics
  ELK Stack for centralized logging
  Grafana for dashboards

==================================================================
ARCHITECTURE REQUIREMENTS
==================================================================

ARCHITECTURAL_PATTERN:
  Clean Architecture with Hexagonal Architecture
  
  Layers:
    • Domain:      Business rules, entities, value objects
    • Application: Use cases, services, DTOs
    • Adapters:    API routes, database, cache, external APIs
    • Frameworks:  FastAPI, managers, configurations

DOMAIN_DRIVEN_DESIGN:
  ✓ Entities with identity (e.g., Invoice, Vendor)
  ✓ Value Objects (e.g., Amount, Currency, Date)
  ✓ Aggregates (root entities managing related objects)
  ✓ Repositories (abstraction for persistence)
  ✓ Services (stateless, coordinate across domains)
  ✓ Exceptions (domain-specific, with business meaning)

MICROSERVICE_READINESS:
  The codebase must be structured to enable extraction into
  microservices. Each bounded context should be independently
  deployable with its own database.

  Key Requirements:
  • Clear service boundaries (one responsibility per service)
  • API-only communication between domains (no shared database)
  • Event-driven where appropriate
  • Stateless design (scale horizontally)

DEPENDENCY_INJECTION:
  ✓ All dependencies constructor-injected
  ✓ No global state (no singletons except managers)
  ✓ Easy to test (swap real implementations for mocks)

DATA_ACCESS:
  ✓ Repository Pattern (abstract persistence)
  ✓ No direct database access in business logic
  ✓ Repositories implement domain interfaces
  ✓ Connection pooling for efficiency

SECURITY:
  ✓ All inputs validated against schema
  ✓ Named bind variables in all SQL (prevent injection)
  ✓ Secrets from environment only (no hardcoding)
  ✓ JWT authentication (RS256, not HS256)
  ✓ Rate limiting on public endpoints
  ✓ HTTPS in production

==================================================================
FOLDER STRUCTURE
==================================================================

Use the standard EAPF folder structure:

  src/project_name/
    ├── config/              # Configuration & secrets
    ├── domain/              # Business rules & entities
    ├── application/         # Use cases & services
    ├── adapters/            # API, database, cache, external
    ├── frameworks/          # Managers & shared utilities
    └── batch/               # Batch jobs & schedulers
  
  tests/
    ├── unit/                # Domain & application tests
    ├── integration/         # API & database tests
    └── performance/         # Performance tests
  
  database/
    ├── ddl/                 # Table & sequence creation
    ├── plsql/               # Packages, procedures, functions
    └── migrations/          # Versioned schema changes
  
  docs/
    ├── ARCHITECTURE.md
    ├── API.md
    └── DATABASE.md

==================================================================
CODING STANDARDS
==================================================================

PYTHON_VERSION:
  Python 3.11+ (no Python 3.10 or earlier)

TYPE_HINTS:
  ✓ Mandatory on all functions (parameters & return)
  ✓ Use typing.Optional, List, Dict, Tuple, Callable
  ✓ Use dataclasses for value objects
  ✓ Run: mypy --strict to validate

NAMING_CONVENTIONS:
  • Modules:          snake_case (invoice_service.py)
  • Classes:          PascalCase (InvoiceService)
  • Functions:        snake_case (create_invoice)
  • Constants:        UPPER_SNAKE_CASE (MAX_RETRIES)
  • Private members:  leading underscore (_private_method)

DOCSTRINGS:
  ✓ Module docstring at top of file
  ✓ Class docstring (one-liner + description)
  ✓ Function docstring with: purpose, parameters, returns, raises
  ✓ Use Google/NumPy style

CODE_ORGANIZATION:
  ✓ Imports organized: stdlib, third-party, local (PEP 8)
  ✓ Constants defined at module level (BEFORE classes)
  ✓ Classes defined (datalasses, then domain, then services)
  ✓ Functions at module level (after classes)
  ✓ __main__ block at end of file (if applicable)

EXCEPTION_HANDLING:
  ✓ Never catch generic Exception
  ✓ Custom exception hierarchy (inherit from ProjectException)
  ✓ Exceptions have business meaning
  ✓ Include context in exception messages
  ✓ Log before re-raising

LOGGING:
  ✓ Use LoggingManager (not print or direct logging)
  ✓ Structured logging with extra context
  ✓ Log levels: DEBUG < INFO < WARNING < ERROR < CRITICAL
  ✓ Include correlation_id in all log entries

PERFORMANCE:
  ✓ Use generators for large datasets (not lists)
  ✓ Use connection pooling (not creating new connections)
  ✓ Cache expensive computations
  ✓ Batch database operations (not N+1 queries)
  ✓ Use async/await for I/O-bound operations

==================================================================
DATABASE STANDARDS
==================================================================

ORACLE_VERSION:
  Oracle Database 19c Enterprise Edition or later

SQL_STANDARDS:
  ✓ Named bind variables (SELECT * FROM t WHERE id = :p_id)
  ✗ No string interpolation / concatenation
  ✓ FETCH FIRST N ROWS ONLY (instead of ROWNUM)
  ✓ BULK COLLECT + FORALL for DML on multiple rows
  ✓ RESULT_CACHE hint for static data
  ✓ Partition pruning for large tables

NAMING_CONVENTIONS:
  • Tables:           inv_invoices (prefix_singular)
  • Columns:          invoice_id, vendor_id, created_date
  • Sequences:        seq_invoice_id
  • Packages:         pkg_invoice_mgmt
  • Procedures:       p_create_invoice
  • Functions:        f_calculate_tax
  • Indexes:          idx_invoices_vendor_id

AUDIT_COLUMNS:
  Every table should include:
    created_date    TIMESTAMP DEFAULT SYSTIMESTAMP
    created_by      VARCHAR2(100)
    modified_date   TIMESTAMP DEFAULT SYSTIMESTAMP
    modified_by     VARCHAR2(100)

==================================================================
API STANDARDS
==================================================================

FASTAPI:
  ✓ All routes must have tags and descriptions
  ✓ Request/response models as Pydantic classes
  ✓ Dependency injection via depends()
  ✓ Status codes: 200, 201, 400, 401, 403, 404, 409, 500
  ✓ Error responses: {"error": "message", "code": "CODE"}

ENDPOINTS:
  POST   /entities              Create
  GET    /entities              List (with pagination)
  GET    /entities/{id}         Get by ID
  PUT    /entities/{id}         Full update
  PATCH  /entities/{id}         Partial update
  DELETE /entities/{id}         Delete

PAGINATION:
  ✓ Query parameters: page (default 1), page_size (default 20, max 100)
  ✓ Response includes: data[], total_count, page, page_size

DOCUMENTATION:
  ✓ OpenAPI/Swagger documentation automatic
  ✓ Docstrings on all routes
  ✓ Example responses in docstrings

==================================================================
TESTING STANDARDS
==================================================================

COVERAGE:
  Minimum 80% code coverage for production code

TEST_TYPES:
  • Unit Tests:        Domain logic, no external dependencies
  • Integration Tests: API endpoints, database, cache
  • Performance Tests: Query performance, API latency
  • Smoke Tests:       Critical workflows (pre-deployment)

FRAMEWORKS:
  Testing:  pytest with pytest-cov
  Mocking:  unittest.mock or pytest-mock
  Async:    pytest-asyncio

FIXTURES:
  ✓ Use pytest fixtures for setup/teardown
  ✓ Database fixtures use transactions (rollback after)
  ✓ Test data factories for consistent test data

TEST_STRUCTURE:
  tests/
    unit/
      domain/
        test_invoice.py
      application/
        test_invoice_service.py
      adapters/
        test_invoice_repository.py
    integration/
      test_invoice_api.py
      test_database.py
    performance/
      test_query_performance.py

==================================================================
OUTPUT RULES
==================================================================

CODE_GENERATION:
  ✓ Generate only code that compiles/runs without errors
  ✓ Include all necessary imports
  ✓ Include type hints on every function
  ✓ Include docstrings on every class and public method
  ✓ No placeholder code (no pass statements left behind)
  ✓ No TODO comments without implementation

FILE_STRUCTURE:
  ✓ One primary class per file (unless small related classes)
  ✓ File names match class names (invoice_service.py for InvoiceService)
  ✓ __init__.py in every package (with appropriate exports)
  ✓ Complete file (not snippets) with imports and structure

COMPLETENESS:
  ✓ Generate complete, working code
  ✓ Don't skip error handling
  ✓ Don't omit business rule validations
  ✓ Don't assume dependencies are installed (mention in requirements.txt)

EXPLANATIONS:
  ✓ Explain architectural decisions
  ✓ Explain non-obvious logic with comments
  ✓ Suggest where to test (in docstring or comment)
  ✓ Flag any security considerations

==================================================================
CONVERSATION FLOW
==================================================================

SESSION 1: Domain & Models
  Generate: invoice.py, vendor.py, value_objects.py, repositories.py,
            exceptions.py, enums.py

SESSION 2: Application Layer
  Generate: invoice_service.py, vendor_service.py, use cases/,
            dto/, validators/

SESSION 3: API & Routes
  Generate: routes/invoices.py, routes/vendors.py, dependencies.py,
            middleware/, exception handlers

SESSION 4: Persistence
  Generate: database/oracle_invoice_repository.py, database/connection.py,
            database/transaction.py

SESSION 5: Database
  Generate: ddl/01_tables.sql, ddl/02_sequences.sql, ddl/03_indexes.sql,
            plsql/packages/, plsql/procedures/, seeds/

SESSION 6: Configuration & Managers
  Generate: config/settings.py, config/secrets.py,
            frameworks/managers/[each manager]

SESSION 7: Tests
  Generate: tests/unit/, tests/integration/, conftest.py, fixtures/

SESSION 8: Deployment
  Generate: Dockerfile, docker-compose.yml, k8s/, CI/CD workflows,
            configuration files

SESSION 9: Documentation
  Generate: README.md, ARCHITECTURE.md, API.md, DATABASE.md,
            development guide

SESSION 10: Verification & Production
  Run verification prompt to audit code quality
  Run production readiness prompt to optimize

==================================================================
DO's AND DON'Ts
==================================================================

DO:
  ✓ Use abstractions (interfaces/ABC)
  ✓ Validate all inputs
  ✓ Log important business events
  ✓ Handle all error cases
  ✓ Use dependency injection
  ✓ Write self-documenting code
  ✓ Follow EAPF standards rigidly
  ✓ Test as you go
  ✓ Ask clarifying questions if ambiguous

DON'T:
  ✗ Use print() for output (use logging)
  ✗ Hardcode configuration or secrets
  ✗ Skip error handling with try/except all
  ✗ Create tight couplings between modules
  ✗ Use global variables or singletons (except managers)
  ✗ Leave TODO comments
  ✗ Skip type hints
  ✗ Duplicate code (refactor into shared functions)
  ✗ Use string concatenation for SQL (bind variables only)
  ✗ Commit database state in tests (use transactions)

==================================================================
ACCEPTANCE CRITERIA
==================================================================

Generated code will be accepted if:

☐ Compiles/runs without errors
☐ Follows all Python 3.11+ standards
☐ Type hints on every function
☐ mypy --strict passes
☐ Pylint score > 9.0/10
☐ Follows EAPF architecture
☐ All error cases handled
☐ Comprehensive logging
☐ Includes docstrings
☐ Security review passes (no hardcoded secrets, SQL injection risks, etc.)
☐ Matches entity-relationship design
☐ Database code uses bind variables
☐ Unit tests with >80% coverage (for business logic)
☐ Integration tests for APIs
☐ README explains how to run
☐ No external dependencies except those listed in requirements.txt

==================================================================
QUESTIONS?
==================================================================

Before proceeding, clarify if any of the following differ from defaults:

1. Additional technology integrations?
   (e.g., Kafka, Elasticsearch, AWS S3)

2. Special compliance requirements?
   (e.g., HIPAA, PCI-DSS, SOX)

3. Specific business domain rules?
   (e.g., invoice approval workflows, tax calculations)

4. Organizational constraints?
   (e.g., must use existing Oracle database, specific IP ranges)

5. Performance requirements?
   (e.g., handle 10K requests/second, queries < 100ms)

6. Alternative technology choices?
   (e.g., PostgreSQL instead of Oracle, SQLAlchemy ORM)

==================================================================
READY TO BEGIN
==================================================================

Confirm you understand these requirements. Once confirmed, we'll
proceed with Session 1: Domain & Models.

All subsequent code will follow these standards without exception.
```

---

# 9. Continuation Prompt Template

## Usage

When Claude's response is cut off (token limit reached), use this prompt to resume without losing context:

```
==================================================================
CONTINUATION PROMPT - [PROJECT_NAME]
Enterprise AI Prompt Framework v1.0
==================================================================

CONTEXT:
Our conversation was interrupted while generating [MODULE_NAME].

PRIOR CONTEXT:
• Technology Stack: Python 3.11, FastAPI, Oracle, Redis
• Architecture: Clean Architecture + Hexagonal
• Standards: EAPF v1.0 (enclosed in master prompt above)

PRIOR GENERATED FILES:
  ✓ src/project_name/domain/invoice.py
  ✓ src/project_name/domain/value_objects.py
  ✓ src/project_name/domain/repositories.py

SKIP THESE FILES:
  [Don't regenerate the above files]

RESUME FROM:
We were generating: src/project_name/application/services/invoice_service.py

You had generated:
  ✓ __init__ method
  ✓ create_invoice() method
  ✓ get_invoice() method

PENDING METHODS:
  ⧂ list_invoices() method
  ⧂ update_invoice() method
  ⧂ delete_invoice() method
  ⧂ _validate_invoice() private method
  ⧂ Class closing

INSTRUCTIONS:
1. Continue ONLY from pending methods
2. Do NOT regenerate completed methods
3. Maintain the same coding style and standards
4. Keep the same imports and class structure
5. Generate complete, working code to the end of the class

RESUME NOW:
```

---

# 10. Resume Prompt Template

## Usage

When resuming after a complete break (new session), use this to continue without full re-context:

```
==================================================================
RESUME PROMPT - [PROJECT_NAME]
Enterprise AI Prompt Framework v1.0
==================================================================

SESSION: [Session Number and Date]

PROJECT_SUMMARY:
  • Project: [PROJECT_NAME]
  • Objective: [Brief business objective]
  • Tech Stack: Python 3.11, FastAPI, Oracle 19c, Redis, Docker, K8s

COMPLETED IN PRIOR SESSIONS:
  ✓ Session 1: Domain layer (entities, value objects, repositories)
  ✓ Session 2: Application layer (services, use cases, validators)
  ✓ Session 3: API routes and FastAPI setup

GENERATED FILES REFERENCE:
  • src/project_name/domain/invoice.py
  • src/project_name/application/services/invoice_service.py
  • src/project_name/adapters/api/routes/invoices.py
  [... complete list]

NEXT SESSION OBJECTIVES:
  Generate database layer and Oracle repositories:
    1. src/project_name/adapters/database/connection.py
    2. src/project_name/adapters/database/oracle_invoice_repository.py
    3. src/project_name/adapters/database/transaction.py

ARCHITECTURE REMINDER:
  • Domain models (Invoice, Vendor) are IMMUTABLE (frozen dataclasses)
  • Repositories implement domain interfaces (InvoiceRepository ABC)
  • Services take repositories as constructor parameters (DI)
  • No database logic in domain or application layers

KEY BUSINESS RULES TO PRESERVE:
  • Invoice status transitions: DRAFT → SUBMITTED → APPROVED → PAID
  • Vendor validation: must exist and be active to create invoice
  • Amounts: positive, decimal with 2 places, currency code (3 letters)

DEPENDENCY CHAIN:
  domain → application → adapters (layers depend inward)

ENTERPRISE STANDARDS TO MAINTAIN:
  ✓ Full type hints on all functions
  ✓ Docstrings with parameter/return documentation
  ✓ Named bind variables in all SQL (no string concat)
  ✓ Connection pooling via cx_Oracle
  ✓ Error handling with custom exceptions
  ✓ Structured logging via LoggingManager

BEGIN GENERATION:
```

---

# 11. Verification Prompt

## Usage

After generating a module, use this to verify quality:

```
==================================================================
VERIFICATION PROMPT - [PROJECT_NAME]
Enterprise AI Prompt Framework v1.0
==================================================================

CODE TO VERIFY:
  File: src/project_name/application/services/invoice_service.py
  [Paste the complete file here]

VERIFICATION CHECKLIST:

SYNTAX & IMPORTS:
  ☐ File compiles without errors (python -m py_compile)
  ☐ All imports present and no unused imports
  ☐ External libraries in requirements.txt

TYPE HINTS:
  ☐ Every function has parameter types
  ☐ Every function has return type
  ☐ Complex types use typing module (List, Dict, Optional, etc.)
  ☐ mypy --strict passes (no type errors)

NAMING CONVENTIONS:
  ☐ Classes: PascalCase (InvoiceService)
  ☐ Functions: snake_case (create_invoice)
  ☐ Constants: UPPER_SNAKE_CASE (MAX_RETRIES)
  ☐ Private: leading underscore (_internal_method)
  ☐ Booleans: is/has/can prefix (is_active, has_items)

ARCHITECTURE:
  ☐ Follows Clean Architecture (domain/application/adapter layers)
  ☐ No direct database access (uses repositories)
  ☐ Dependency Injection via constructor
  ☐ No global state or singletons (except managers)
  ☐ Single Responsibility Principle (one reason to change)

DOCUMENTATION:
  ☐ Module docstring at top
  ☐ Class docstring with purpose
  ☐ Docstring on every public method
  ☐ Complex logic has inline comments
  ☐ Parameter and return types documented

ERROR HANDLING:
  ☐ All error cases handled
  ☐ Custom exceptions used (not generic Exception)
  ☐ Exceptions logged before re-raising
  ☐ Graceful degradation where appropriate

BUSINESS RULES:
  ☐ Domain rules enforced (e.g., status transitions)
  ☐ Validation happens before state changes
  ☐ All inputs validated with Pydantic
  ☐ Side effects logged

SECURITY:
  ☐ No hardcoded secrets or passwords
  ☐ All configuration from environment
  ☐ Input validation present
  ☐ No SQL injection risks (if applicable)

PERFORMANCE:
  ☐ No obvious performance issues
  ☐ No N+1 query patterns (if database code)
  ☐ Generators for large sequences
  ☐ Caching where appropriate

COMPLETENESS:
  ☐ No pass statements left behind
  ☐ No TODO comments without code
  ☐ All methods have complete implementations
  ☐ Class definition properly closed

ISSUES FOUND:
  [Report any issues]

FIXES NEEDED:
  [Suggested fixes]

VERDICT:
  ☐ PASS - Ready for use
  ☐ NEEDS_REVISION - List specific issues
  ☐ REJECT - Fundamental problems
```

---

# 12. Production Readiness Prompt

## Usage

Before deploying to production, run this comprehensive audit:

```
==================================================================
PRODUCTION READINESS PROMPT - [PROJECT_NAME]
Enterprise AI Prompt Framework v1.0
==================================================================

SCOPE:
Audit the complete [PROJECT_NAME] for production readiness across
all layers: API, services, repositories, database, infrastructure.

APPLICATION CODE REVIEW:

PERFORMANCE:
  [ ] All queries use bind variables (no string concatenation)
  [ ] FETCH FIRST N ROWS ONLY for pagination (not ROWNUM)
  [ ] BULK COLLECT + FORALL for DML > 100 rows
  [ ] RESULT_CACHE hints for static/reference data
  [ ] Connection pool sizing: min 5, max 20 (configurable)
  [ ] No N+1 query patterns in application code
  [ ] Async/await used for I/O operations
  [ ] Large datasets processed as generators, not lists
  [ ] Expensive computations cached with TTL

SECURITY:
  [ ] All inputs validated against Pydantic schemas
  [ ] SQL injection: no string concatenation in SQL
  [ ] XSS: response data properly encoded
  [ ] CSRF: tokens validated on state-changing operations
  [ ] Authentication: JWT RS256 algorithm (not HS256)
  [ ] Access tokens: TTL ≤ 15 minutes
  [ ] Refresh tokens: rotated on use
  [ ] Secrets: loaded from environment (not in code)
  [ ] No secrets in Docker images or logs
  [ ] Rate limiting: enforced on public endpoints
  [ ] File uploads: validated (type, size, virus scan)

RELIABILITY:
  [ ] All exceptions logged with context
  [ ] Circuit breaker on external API calls
  [ ] Retry logic with exponential backoff on transient failures
  [ ] Graceful degradation when dependencies unavailable
  [ ] Health checks for database, cache, external services
  [ ] Correlation IDs propagated through all calls
  [ ] Proper transaction handling (rollback on error)

LOGGING & OBSERVABILITY:
  [ ] Structured JSON logging (not print statements)
  [ ] Correlation ID on every log entry
  [ ] Business events logged at INFO level
  [ ] Errors logged at ERROR level with stack trace
  [ ] Performance metrics: duration_ms on services
  [ ] No sensitive data in logs (no passwords, API keys)
  [ ] Log retention configured
  [ ] Centralized logging (ELK, CloudWatch, etc.)

DATABASE:
  [ ] Indexes on all WHERE clause columns
  [ ] Composite indexes for multi-column queries
  [ ] Audit columns: created_date, created_by, modified_date, modified_by
  [ ] DDL change scripts reversible (rollback scripts)
  [ ] Statistics gathered (ANALYZE)
  [ ] Execution plans reviewed for large queries
  [ ] Partitioning for tables > 10M rows
  [ ] Archive strategy for old data (if applicable)
  [ ] Backup and recovery tested
  [ ] Connection pool recovery from failures

DOCKER:
  [ ] Non-root USER in Dockerfile
  [ ] .dockerignore excludes .env, logs/, *.pyc, __pycache__
  [ ] HEALTHCHECK instruction present
  [ ] Multi-stage build for minimal image size
  [ ] No secrets in ENV instructions
  [ ] Image scanned for vulnerabilities
  [ ] Image size reasonable (< 1GB)
  [ ] Proper entrypoint and arguments

KUBERNETES:
  [ ] Readiness probe: /health/ready endpoint
  [ ] Liveness probe: /health/live endpoint
  [ ] Resource requests and limits defined
  [ ] Graceful shutdown: drain connections on termination
  [ ] ConfigMap for non-secret configuration
  [ ] Secrets for sensitive data (not ConfigMap)
  [ ] Rolling updates configured
  [ ] Horizontal pod autoscaling ready (stateless)

MONITORING:
  [ ] Prometheus metrics exposed
  [ ] Key metrics: request count, latency, error rate
  [ ] Dashboards created (Grafana)
  [ ] Alerts configured for critical issues
  [ ] APM integration (if applicable)
  [ ] Custom business metrics (invoices processed, etc.)

DEPLOYMENT:
  [ ] CI/CD pipeline automated (GitHub Actions, GitLab CI, etc.)
  [ ] Automated tests run before merge
  [ ] Code coverage > 80% enforced
  [ ] Linting enforced (pylint > 9.0)
  [ ] Type checking enforced (mypy --strict)
  [ ] Security scanning (bandit, safety)
  [ ] Dependency scanning for known vulnerabilities
  [ ] Semantic versioning used
  [ ] Release notes generated automatically

COMPLIANCE & GOVERNANCE:
  [ ] Data access logged (audit trail)
  [ ] PII data encrypted at rest and in transit
  [ ] Compliance with organizational policies
  [ ] Terms of service and privacy policy acknowledged
  [ ] GDPR/CCPA compliant (if applicable)

PRODUCTION READINESS SCORE:
  
  Total Checkpoints: [COUNT]
  Passed: [COUNT]
  Failed: [COUNT]
  Score: [PERCENTAGE]%

SCORING GUIDE:
  90-100%: ✓ READY FOR PRODUCTION
  75-89%:  ⚠ CONDITIONALLY READY (fix critical issues first)
  60-74%:  ✗ NOT READY (major gaps)
  < 60%:   ✗ BLOCKED (architectural review required)

CRITICAL ISSUES (Must fix before deploy):
  1. [Issue 1]
  2. [Issue 2]

HIGH PRIORITY (Fix within 1 sprint):
  1. [Issue 1]
  2. [Issue 2]

RECOMMENDATIONS (Backlog items):
  1. [Issue 1]
  2. [Issue 2]

REMEDIATION PLAN:
  [Specific, actionable steps to address issues]

APPROVAL:
  ☐ Approved for production deployment
  ☐ Approved with exceptions (document them)
  ☐ Rejected (do not deploy)

Date: [DATE]
Reviewer: [NAME]
```

---

# 13. Oracle Database Prompt

For generating Oracle DDL, PL/SQL packages, procedures, and functions:

```
==================================================================
ORACLE DATABASE PROMPT - [PROJECT_NAME]
Enterprise AI Prompt Framework v1.0
==================================================================

SCOPE:
Generate Oracle 19c database artifacts for [PROJECT_NAME]

DATABASE OBJECTS TO GENERATE:

TABLES:
  1. inv_invoices (primary entity)
     - Columns: invoice_id (PK), vendor_id (FK), amount, status,
       created_date, created_by, modified_date, modified_by
     - Constraints: NOT NULL, CHECK, FOREIGN KEY
     - Audit columns: required on all tables

  2. inv_line_items (child entity)
     - Columns: line_item_id (PK), invoice_id (FK), description,
       quantity, unit_price, amount
     - Relationships: one-to-many with invoices

SEQUENCES:
  • seq_invoice_id (for generating invoice_ids)
  • seq_line_item_id (for generating line_item_ids)

INDEXES:
  • Primary key indexes (automatic)
  • Foreign key indexes (vendor_id on invoices)
  • Status index (for status filtering)
  • Created_date index (for date range queries)
  • Composite index (status, created_date)

CONSTRAINTS:
  • Primary keys on all tables
  • Foreign keys to maintain referential integrity
  • NOT NULL constraints where appropriate
  • CHECK constraints for status values
  • UNIQUE constraints (if applicable)

PACKAGE: pkg_invoice_mgmt
  Purpose: Core invoice management procedures and functions
  
  Procedures:
    • p_create_invoice (INSERT logic with validation)
    • p_update_invoice (UPDATE with status validation)
    • p_approve_invoice (status transition SUBMITTED → APPROVED)
    • p_cancel_invoice (status transition → CANCELLED)
  
  Functions:
    • f_get_invoice_total (calculate total from line items)
    • f_invoice_status_description (return status description)

VIEWS:
  • v_invoices_summary (invoice list with vendor, total, status)
  • v_invoices_by_vendor (group by vendor)

SQL STANDARDS:
  ✓ Named parameters: p_invoice_id, p_vendor_id
  ✓ Local variables: l_invoice_id, l_error_code
  ✓ Constants: c_max_amount, c_default_status
  ✓ Error handling: raise exceptions with meaningful messages
  ✓ Logging: use DBMS_UTILITY.GET_TIME for performance tracking
  ✓ Comments: every procedure/function has header comments

GENERATE:
  1. ddl/01_tables.sql (CREATE TABLE statements)
  2. ddl/02_sequences.sql (CREATE SEQUENCE statements)
  3. ddl/03_indexes.sql (CREATE INDEX statements)
  4. plsql/packages/pkg_invoice_mgmt.pks (Package specification)
  5. plsql/packages/pkg_invoice_mgmt.pkb (Package body)
  6. plsql/views/v_invoices_summary.sql (Views)
```

---

# 14. Testing Prompt

For generating comprehensive test suites:

```
==================================================================
TESTING PROMPT - [PROJECT_NAME]
Enterprise AI Prompt Framework v1.0
==================================================================

TEST SUITE GENERATION:

UNIT TESTS (domain & business logic):
  File: tests/unit/domain/test_invoice.py
  
  Test Cases:
    ✓ test_invoice_creation (valid data)
    ✓ test_invoice_creation_invalid_amount (negative amount)
    ✓ test_invoice_status_transition_draft_to_submitted
    ✓ test_invoice_cannot_approve_draft (invalid transition)
    ✓ test_invoice_total_calculation (line items)
    ✓ test_value_object_amount_validation
    ✓ test_value_object_currency_code_validation

INTEGRATION TESTS (API endpoints):
  File: tests/integration/test_invoice_api.py
  
  Test Cases:
    ✓ test_create_invoice_endpoint (POST /invoices)
    ✓ test_get_invoice_endpoint (GET /invoices/{id})
    ✓ test_list_invoices_endpoint (GET /invoices with pagination)
    ✓ test_update_invoice_endpoint (PUT /invoices/{id})
    ✓ test_approve_invoice_endpoint (POST /invoices/{id}/approve)
    ✓ test_create_invoice_missing_required_field (validation)
    ✓ test_create_invoice_vendor_not_found (error handling)

DATABASE TESTS:
  File: tests/integration/test_invoice_repository.py
  
  Test Cases:
    ✓ test_save_invoice (INSERT)
    ✓ test_find_invoice_by_id (SELECT by PK)
    ✓ test_find_invoices_by_vendor (SELECT with filter)
    ✓ test_update_invoice (UPDATE)
    ✓ test_delete_invoice (DELETE)
    ✓ test_concurrent_saves (transaction handling)

FIXTURES:
  • fixture_db_connection (transaction-based)
  • fixture_sample_invoice (factory pattern)
  • fixture_sample_vendor (factory pattern)
  • fixture_test_user (for auth tests)

TESTING FRAMEWORK:
  • pytest
  • pytest-asyncio (for async tests)
  • pytest-cov (for coverage)
  • pytest-mock (for mocking)
  • unittest.mock (built-in)

COVERAGE REQUIREMENT:
  • Minimum 80% code coverage
  • Branch coverage for conditional logic
  • Coverage report in HTML format

GENERATE:
  1. tests/unit/domain/test_invoice.py
  2. tests/integration/test_invoice_api.py
  3. tests/integration/test_invoice_repository.py
  4. tests/conftest.py (shared fixtures)
  5. tests/fixtures/sample_data.py (test data factories)
```

---

# 15. Final Audit Prompt

Comprehensive audit before release:

```
==================================================================
FINAL AUDIT PROMPT - [PROJECT_NAME]
Enterprise AI Prompt Framework v1.0
==================================================================

COMPREHENSIVE PROJECT AUDIT

ARCHITECTURE AUDIT:
  [ ] Layered architecture maintained (domain/application/adapter)
  [ ] Clear separation of concerns
  [ ] Dependency flow is unidirectional (inward)
  [ ] No circular dependencies
  [ ] Microservice boundaries clearly defined
  [ ] Each domain has its own repository interface

DEPENDENCY AUDIT:
  [ ] All imports are explicit
  [ ] No circular imports
  [ ] External dependencies documented in requirements.txt
  [ ] Version constraints specified
  [ ] Dependency versions compatible

SECURITY AUDIT:
  [ ] No hardcoded credentials
  [ ] No API keys in code
  [ ] Input validation on all endpoints
  [ ] SQL injection prevention (bind variables)
  [ ] XSS prevention (output encoding)
  [ ] Authentication enforced
  [ ] Authorization checks in place
  [ ] Secrets management configured
  [ ] TLS/HTTPS required

CODE QUALITY AUDIT:
  [ ] No duplicate code (DRY principle)
  [ ] Consistent naming conventions
  [ ] All functions documented
  [ ] No overly complex methods (cyclomatic complexity < 10)
  [ ] Code follows PEP 8 style guide
  [ ] Type hints complete
  [ ] Docstrings comprehensive

TEST AUDIT:
  [ ] Unit test coverage > 80%
  [ ] Integration tests for critical paths
  [ ] Edge cases tested
  [ ] Error conditions tested
  [ ] Performance tests run
  [ ] Tests are independent and repeatable
  [ ] Mocking used appropriately

PERFORMANCE AUDIT:
  [ ] Query optimization (indexes, FETCH FIRST, bind variables)
  [ ] Connection pooling configured
  [ ] Caching strategy implemented
  [ ] No N+1 patterns
  [ ] Async/await for I/O
  [ ] Memory usage optimized (generators for large datasets)
  [ ] Response times acceptable

DATABASE AUDIT:
  [ ] Schema normalized (3NF or higher)
  [ ] All tables have primary keys
  [ ] Foreign keys define relationships
  [ ] Audit columns present (created_date, modified_date)
  [ ] Indexes created for performance
  [ ] DDL scripts are idempotent
  [ ] Migration scripts tested

DEPLOYMENT AUDIT:
  [ ] Docker image builds successfully
  [ ] Environment configuration externalized
  [ ] Health checks configured
  [ ] Graceful shutdown implemented
  [ ] Monitoring/logging configured
  [ ] CI/CD pipeline functional
  [ ] Automated testing enforced
  [ ] Deployment documented

COMPLIANCE AUDIT:
  [ ] No sensitive data in logs
  [ ] Data retention policies enforced
  [ ] PII encryption at rest/in transit
  [ ] Access logs maintained
  [ ] Exception handling doesn't expose internals
  [ ] API rate limiting implemented
  [ ] Terms of service incorporated

DOCUMENTATION AUDIT:
  [ ] README.md complete and current
  [ ] API documentation (OpenAPI/Swagger)
  [ ] Database design documented
  [ ] Architecture decisions documented
  [ ] Deployment procedures documented
  [ ] Troubleshooting guide provided
  [ ] Contributing guidelines provided

PRODUCTION READINESS SCORECARD:

Category           | Status  | Score
-------------------|---------|-------
Architecture       | [ ]Pass | __/10
Dependencies       | [ ]Pass | __/10
Security           | [ ]Pass | __/10
Code Quality       | [ ]Pass | __/10
Testing            | [ ]Pass | __/10
Performance        | [ ]Pass | __/10
Database           | [ ]Pass | __/10
Deployment         | [ ]Pass | __/10
Compliance         | [ ]Pass | __/10
Documentation      | [ ]Pass | __/10
=====================================
OVERALL SCORE:                  __/100

VERDICT:
  [ ] APPROVED - Ready for production
  [ ] CONDITIONAL - Approve with action items (list below)
  [ ] REJECTED - Do not deploy (explain below)

ACTION ITEMS (if conditional):
  1. [Action 1]
  2. [Action 2]
  3. [Action 3]

SIGN-OFF:
  Date: [DATE]
  Technical Lead: [NAME]
  QA Lead: [NAME]
  DevOps Lead: [NAME]
```

---

# 16–22. Additional Standards & Checklists

## 16. Enterprise Coding Standards (Detailed)

**PEP 8 Compliance:** All Python code must follow PEP 8 strictly.

**File Headers:**

```python
"""
Module: invoice_service.py
Project: InvoiceIQ
Purpose: Service layer for invoice operations
Author: Enterprise Architecture Office
Version: 1.0.0
Created: 2025-01-15
Modified: 2025-01-20
Dependencies: 
  • pydantic
  • cx_Oracle
"""
```

**Class Headers:**

```python
class InvoiceService:
    """
    Service layer for invoice operations.
    
    Responsibilities:
      • Create and manage invoices
      • Validate business rules
      • Coordinate with repositories
    
    Dependencies:
      • InvoiceRepository (injected)
      • VendorRepository (injected)
      • LoggingManager (injected)
    """
```

**Method Headers:**

```python
def create_invoice(self, request: CreateInvoiceRequest) -> InvoiceResponse:
    """
    Create a new invoice with validation.
    
    Business Rules:
      1. Vendor must exist and be active
      2. Amount must be positive
      3. Currency must be valid ISO code
    
    Args:
        request: Invoice creation request with vendor_id, amount, currency
    
    Returns:
        InvoiceResponse with created invoice_id and status
    
    Raises:
        VendorNotFoundError: If vendor doesn't exist
        InvoiceValidationException: If amount or currency invalid
        DatabaseException: If persistence fails
    
    Example:
        >>> service = InvoiceService(repo)
        >>> response = service.create_invoice(CreateInvoiceRequest(...))
        >>> print(response.invoice_id)
        'INV001'
    """
```

---

## 17. Enterprise SQL Standards

**Never use string concatenation or f-strings in SQL:**

```sql
-- ❌ WRONG
SELECT * FROM invoices WHERE vendor_id = '" || p_vendor_id || "'

-- ✓ CORRECT
SELECT * FROM invoices WHERE vendor_id = :p_vendor_id

-- ❌ WRONG
SELECT * FROM invoices WHERE status = 'DRAFT' AND created_date > TRUNC(SYSDATE)

-- ✓ CORRECT
SELECT * FROM invoices 
WHERE status = :p_status 
AND created_date > :p_date_threshold
```

---

## 18. Enterprise Logging Standards

**Structured Logging Examples:**

```python
# Good: Structured context
logger.info(
    "Invoice created successfully",
    extra={
        'invoice_id': 'INV001',
        'vendor_id': 'VEND001',
        'amount': 1000.00,
        'correlation_id': request.correlation_id,
        'duration_ms': 45
    }
)

# Bad: Unstructured string
logger.info(f"Created invoice INV001 for VEND001 with amount 1000.00")
```

---

## 19. Enterprise Security Standards

**Input Validation Checklist:**

- ☐ All API inputs validated with Pydantic
- ☐ String length limits enforced
- ☐ Numeric ranges enforced
- ☐ Enum values validated
- ☐ Email format validated
- ☐ File uploads scanned

---

## 20. Enterprise Deployment Standards

**Dockerfile Best Practices:**

```dockerfile
# Multi-stage build for minimal size
FROM python:3.11-slim as builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt

FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /root/.local /home/appuser/.local
COPY src/ .
USER appuser
ENV PATH=/home/appuser/.local/bin:$PATH
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:8000/health/live')"
CMD ["python", "-m", "uvicorn", "main:app", "--host", "0.0.0.0"]
```

---

## 21. Enterprise Checklists

### Project Startup Checklist

- [ ] Repository created and cloned
- [ ] Branch protection rules configured
- [ ] Development environment setup (Python 3.11, venv)
- [ ] Dependencies installed (requirements.txt)
- [ ] Database connection tested
- [ ] Git hooks configured (pre-commit linting)
- [ ] IDE configured (PyCharm, VS Code settings)
- [ ] Team members added with appropriate permissions
- [ ] Documentation repository created
- [ ] Slack/communication channels created

### Development Checklist

- [ ] Feature branch created from main
- [ ] Changes follow EAPF standards
- [ ] Type hints added (mypy --strict passes)
- [ ] Unit tests written (> 80% coverage)
- [ ] Code reviewed by peer
- [ ] Integration tests passed
- [ ] Performance tests run
- [ ] Security review completed
- [ ] Documentation updated
- [ ] Commit messages follow conventional commits

### Pre-Release Checklist

- [ ] All tests pass (unit, integration, performance)
- [ ] Code coverage > 80%
- [ ] Static analysis clean (pylint > 9.0)
- [ ] Security scan clean (bandit, safety)
- [ ] Documentation complete
- [ ] Version bumped (semantic versioning)
- [ ] CHANGELOG updated
- [ ] Production readiness audit passed
- [ ] Deployment procedure tested
- [ ] Rollback procedure tested
- [ ] On-call runbook created
- [ ] Monitoring/alerts configured

---

## 22. Appendices

### A. Naming Conventions Reference

| Element | Pattern | Example |
|---------|---------|---------|
| Python Package | lowercase_underscore | `invoice_service` |
| Python Module | lowercase_underscore.py | `invoice_service.py` |
| Python Class | PascalCase | `InvoiceService` |
| Python Function | snake_case | `create_invoice` |
| Python Constant | UPPER_SNAKE_CASE | `MAX_INVOICE_AMOUNT` |
| Python Private | _leading_underscore | `_internal_method` |
| Python Dunder | __double_underscore | `__init__` |
| Boolean Variable | is_/has_/can_ prefix | `is_active`, `has_items` |
| Oracle Table | prefix_plural | `inv_invoices` |
| Oracle Column | snake_case | `invoice_id`, `vendor_id` |
| Oracle Sequence | seq_entity_id | `seq_invoice_id` |
| Oracle Package | pkg_domain_function | `pkg_invoice_mgmt` |
| Oracle Procedure | p_verb_object | `p_create_invoice` |
| Oracle Function | f_noun_description | `f_calculate_tax` |
| Oracle Index | idx_table_columns | `idx_invoices_vendor_id` |
| REST Endpoint | /resources or /resources/{id} | `/invoices`, `/invoices/123` |
| Environment Variable | UPPER_SNAKE_CASE | `DB_HOST`, `REDIS_URL` |

### B. Recommended Python Libraries

**Web Framework:**
- FastAPI 0.110+
- Uvicorn (ASGI server)
- Pydantic (validation)

**Database:**
- cx_Oracle (Oracle driver)
- SQLAlchemy ORM (optional)

**Testing:**
- pytest
- pytest-asyncio
- pytest-cov
- pytest-mock

**Utilities:**
- python-jose (JWT)
- python-dotenv (environment loading)
- pyyaml (configuration files)
- click (CLI)

**Logging & Monitoring:**
- python-json-logger (structured logging)
- prometheus-client (metrics)

**AI Integration:**
- anthropic (Claude API)

### C. Recommended Oracle Libraries

**Python Drivers:**
- cx_Oracle (native driver)
- oracledb (python-oracledb, newer)

**Database Tools:**
- SQLPlus (command-line)
- SQL Developer (IDE)
- Toad (IDE)

### D. Recommended DevOps Tools

**Containers:**
- Docker
- Docker Compose

**Orchestration:**
- Kubernetes
- Helm

**CI/CD:**
- GitHub Actions
- GitLab CI
- Jenkins

**Monitoring:**
- Prometheus
- Grafana
- ELK Stack

**Cloud Platforms:**
- AWS (ECS, EKS, RDS, S3)
- Azure (ACI, AKS, SQL Database)
- GCP (Cloud Run, GKE, Cloud SQL)

### E. Recommended Project Management

- Jira (issue tracking)
- Confluence (documentation)
- GitHub/GitLab (code hosting)
- Slack (communication)

---

# CONCLUSION

The **Enterprise AI Prompt Framework v1.0** provides a comprehensive, reusable system for building enterprise-grade AI applications using modern Python, FastAPI, Oracle, and cloud technologies.

By following this framework:
- **Team velocity increases** (standardized templates and patterns)
- **Code quality improves** (strict standards and automation)
- **Technical debt decreases** (consistent architecture)
- **Production readiness assured** (comprehensive checklists and audits)

Use this framework as your:
- **North Star** for architecture decisions
- **Playbook** for code generation with Claude
- **Standard** for code reviews
- **Checklist** for production deployments

**Remember:** Consistency over cleverness. Clarity over complexity. Enterprise standards over individual preferences.

---

**END OF PART 4 (FINAL)**

**Total Document: ~150 pages | 22 Sections | 4 Parts**

© Enterprise Architecture Office
CONFIDENTIAL - Internal Use Only

---

