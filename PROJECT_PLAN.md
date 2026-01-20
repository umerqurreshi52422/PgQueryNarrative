---
name: PgQueryNarrative Project Plan
overview: Complete implementation plan for PgQueryNarrative - a Data Storyteller AI that converts SQL query results into business-ready narratives using Go, PostgreSQL, and free LLM models. The project follows Goa API design patterns and enterprise-grade structure inspired by pgEdge/control-plane.
todos:
  - id: setup-project-structure
    content: Create complete project directory structure following Goa and pgEdge patterns
    status: pending
  - id: setup-goa-design
    content: Define Goa API design files (design.go, queries.go, reports.go, types.go)
    status: pending
  - id: database-schema
    content: Create database migrations for app schema (saved_queries, reports, audit_logs) and demo schema
    status: pending
  - id: query-runner
    content: Implement query runner with validation, execution, and result profiling
    status: pending
  - id: metrics-calculator
    content: Build metrics calculation engine (totals, averages, time-series, top categories)
    status: pending
  - id: llm-integration
    content: Implement LLM client interface with Ollama, Groq, and Gemini providers
    status: pending
  - id: narrative-generator
    content: Create narrative generation service with prompt engineering and JSON parsing
    status: pending
  - id: web-ui-templ
    content: Build web UI using Templ templates and HTMX for interactivity
    status: pending
  - id: documentation
    content: Write comprehensive documentation (README, API docs, architecture, deployment)
    status: pending
  - id: docs-generation
    content: Set up automatic documentation generation (GoDoc, OpenAPI, README validation)
    status: pending
  - id: testing
    content: Implement unit and integration tests with fixtures and test utilities
    status: pending
  - id: e2e-testing
    content: Implement E2E tests for complete user workflows (query to report generation)
    status: pending
  - id: github-workflows
    content: Create GitHub Actions workflows for CI/CD, E2E tests, releases, and documentation
    status: pending
  - id: git-conventions
    content: Set up Git conventions (branching strategy, commit messages, pre-commit hooks)
    status: pending
  - id: docker-setup
    content: Create Docker configuration and docker-compose for local development
    status: pending
  - id: license-readme
    content: Add MIT license and comprehensive README following open source standards
    status: pending
---

# PgQueryNarrative: Data Storyteller AI - Complete Project Plan

## 1. Problem Statement

### The Challenge

Organizations generate vast amounts of data through SQL queries, but transforming raw query results into actionable business insights remains a manual, time-consuming process. Data analysts spend hours creating reports, executives struggle to interpret tabular data, and technical teams are bottlenecked producing readable summaries for stakeholders.

Current solutions focus on **query generation** (Text-to-SQL), but there's a critical gap in **query interpretation**: converting results into clear, evidence-based narratives that non-technical stakeholders can understand and act upon.

### Why Existing Solutions Fall Short

- **BI Tools**: Require complex setup, expensive licenses, and don't generate natural language narratives
- **AI Query Tools**: Focus on generating SQL, not interpreting results
- **Manual Reporting**: Time-consuming, inconsistent, error-prone
- **Dashboard Tools**: Require constant maintenance and don't produce narrative summaries

### The Solution: PgQueryNarrative

PgQueryNarrative automatically converts SQL query results into structured business narratives with:

- Evidence-based insights derived only from query results
- Computed metrics (trends, deltas, top contributors)
- Structured outputs (headlines, takeaways, drivers, limitations)
- Guardrails to prevent hallucinations and ensure accuracy
- Export capabilities for reports and presentations

---

## 2. Project Naming

**Top Recommended Name: `QueryNarrative`**

**Rationale:**

- **Clear Purpose**: "Query" + "Narrative" immediately communicates SQL query → story conversion
- **Professional**: Sounds enterprise-ready and technical
- **Memorable**: Easy to remember and pronounce
- **Available**: No major conflicts found for this exact use case
- **SEO-Friendly**: Good keyword combination for discoverability

**Secondary Recommendation: `SQLStory`**

**Rationale:**

- **Concise**: Short, memorable name
- **Direct**: Clearly indicates SQL input and story output
- **Brandable**: Easy to create domain and branding
- **Note**: SQLStory.ai exists but is in stealth mode (different focus)

**Alternative Names Considered:**

| Name | Pros | Cons | Status |
|------|------|------|--------|
| **QueryNarrative** ⭐ | Clear, professional, available | Slightly longer | **RECOMMENDED** |
| **SQLStory** | Short, memorable, direct | SQLStory.ai exists (stealth) | Good alternative |
| **NarrativeSQL** | Clear flow, professional | Less intuitive word order | Available |
| **NarrateDB** | Concise, database-focused | Less memorable | Available |
| **StoryForge** | Memorable, creative | Too generic (fiction writing) | Available |
| **QueryVox** | Unique, modern | Less obvious purpose | Available |
| **InsightForge** | Emphasizes insights | InsightForge.com exists (commercial) | Conflict |
| **DataForge** | Adaptable, broad | Too generic | Available |

**Final Decision: `PgQueryNarrative`** ⭐

**Why PgQueryNarrative:**
- **PostgreSQL-Specific**: "Pg" prefix clearly indicates PostgreSQL focus
- **Clear Purpose**: "Query" + "Narrative" communicates SQL → story conversion
- **Professional**: Sounds enterprise-ready and technical
- **Memorable**: Easy to remember and pronounce
- **Available**: No major conflicts found for this exact use case
- **Brandable**: Works well for GitHub repository naming (`pgquerynarrative` or `pg-query-narrative`)
- **Aligns with pgEdge**: "Pg" prefix aligns with PostgreSQL ecosystem naming conventions

---

## 3. Architecture Overview

### High-Level Architecture

```
┌─────────────┐
│  Web UI     │ (Templ + HTMX)
│  (Frontend) │
└──────┬──────┘
       │ HTTP/REST
       ▼
┌──────────────────────────────────────┐
│  Goa API Server (Go)                 │
│  ├── Query Runner                    │
│  ├── Result Profiler                 │
│  ├── Metrics Calculator              │
│  ├── LLM Client Interface            │
│  ├── MCP Client (Optional)           │
│  ├── Embedding Generator (Optional)  │
│  └── Report Generator                │
└──────┬───────────────────┬───────────┘
       │                   │
       ▼                   ▼
┌─────────────┐    ┌──────────────────┐
│ PostgreSQL  │    │ Free LLM + MCP   │
│  Database   │    │ (Ollama/         │
│ + pgvector  │    │  Groq/Gemini)    │
│ (optional)  │    │                  │
└─────────────┘    └──────────────────┘
```

### Component Responsibilities

**Web UI (Templ + HTMX)**

- SQL query editor with syntax highlighting
- Saved queries management
- Results preview table
- Narrative report display
- Export functionality (Markdown/JSON)

**Goa API Server**

- Request validation via Goa DSL
- SQL execution (read-only)
- Result profiling and metric computation
- LLM integration with structured prompts
- Report generation and storage
- Audit logging

**PostgreSQL Database**

- Business data tables (read-only access)
- Application tables (saved_queries, reports, audit_logs)
- Role-based access control

**LLM Providers (Free)**

- Ollama (local, fully free)
- Groq (free tier, fast)
- Google Gemini (free tier)

---

## 3.1. Architectural Decision: Standalone Service vs PostgreSQL Extension

### Decision: Standalone Service Architecture

PgQueryNarrative is designed as a **standalone service** rather than a PostgreSQL extension. This architectural decision is fundamental to the project's design and implementation approach.

### Rationale

**1. External LLM Integration Requirements**

- Requires HTTP calls to external services (Ollama, Groq, Gemini APIs)
- Needs robust retry logic, timeouts, and error handling for external API calls
- PostgreSQL extensions cannot easily make external HTTP requests
- LLM integration requires connection pooling, authentication, and rate limiting
- **Conclusion**: External API integration is best handled in a standalone service

**2. Web UI Requirements**

- Must serve HTML/HTMX interfaces for interactive user experience
- Requires static file serving, routing, and session management
- Needs to handle multiple concurrent user sessions
- PostgreSQL extensions cannot serve web interfaces
- **Conclusion**: Web UI requires a full HTTP server, which extensions cannot provide

**3. Complex Business Logic**

- Narrative generation involves multi-step workflows (query → profile → metrics → LLM → narrative)
- Metrics calculation requires complex algorithms and data transformations
- Result profiling needs sophisticated pattern recognition
- Error handling, validation, and data transformation are easier in Go
- **Conclusion**: Complex business logic benefits from general-purpose language (Go) and modern tooling

**4. Independent Scalability**

- Narrative generation can be CPU/memory intensive (LLM processing)
- Query execution and narrative generation should scale independently
- Can deploy multiple service instances behind load balancer
- Database load should not be affected by narrative generation workload
- **Conclusion**: Standalone service allows independent horizontal scaling

**5. API Versioning and Evolution**

- Need to support multiple API versions simultaneously
- API contracts evolve independently from database schema
- Goa framework provides excellent API versioning capabilities
- PostgreSQL extensions have limited API versioning support
- **Conclusion**: API versioning is critical for maintaining backward compatibility

**6. Development Ecosystem**

- Go ecosystem provides rich libraries, testing frameworks, and tooling
- Better CI/CD integration, monitoring, and observability
- Easier to test, debug, and maintain complex logic
- Modern development practices (unit tests, integration tests, E2E tests)
- **Conclusion**: Go ecosystem accelerates development and improves maintainability

**7. Operational Flexibility**

- Can deploy service separately from database
- Rolling deployments without database downtime
- Can update business logic without touching database
- Better fault isolation (service failures don't affect database)
- **Conclusion**: Operational flexibility improves reliability and deployment safety

### Optional Hybrid Approach (Future Enhancement)

While the core service remains standalone, certain performance-critical components could potentially be implemented as optional PostgreSQL extensions in the future:

**Potential Extension Components:**

1. **Query Validation Functions**
   - PostgreSQL-side SQL parsing and validation
   - Syntax checking before query execution
   - Could provide faster feedback for invalid queries

2. **Basic Metrics Aggregation**
   - Native SQL functions for common aggregations
   - Computed columns for frequently-used metrics
   - Could reduce data transfer for simple metrics

3. **Result Profiling Hints**
   - Database-side metadata collection
   - Column type detection helpers
   - Statistics and profiling metadata functions

**Hybrid Approach Requirements:**

- Extensions would be **optional** - core service works without them
- Extensions would provide **performance optimizations** only
- Core functionality must remain in standalone service
- Extensions must not create tight coupling
- Clear separation of concerns (extension = optimization, service = functionality)

**When to Consider Extensions:**

- Performance profiling identifies bottlenecks in database queries
- Specific use cases show significant latency from data transfer
- Community requests for in-database optimizations
- Clear performance gains justify additional complexity

### Comparison Table

| Aspect | Standalone Service | PostgreSQL Extension | Winner |
|--------|-------------------|---------------------|--------|
| External API Calls | ✅ Native support | ❌ Complex/impossible | **Standalone** |
| Web UI Serving | ✅ Full HTTP server | ❌ Cannot serve web | **Standalone** |
| Complex Business Logic | ✅ Go ecosystem | ⚠️ Limited (PL/pgSQL) | **Standalone** |
| Independent Scaling | ✅ Horizontal scaling | ❌ Scales with DB | **Standalone** |
| API Versioning | ✅ Goa versioning | ⚠️ Limited support | **Standalone** |
| Development Tooling | ✅ Rich ecosystem | ⚠️ Limited tooling | **Standalone** |
| Deployment Flexibility | ✅ Separate deployments | ❌ Coupled to DB | **Standalone** |
| Query Performance | ⚠️ Network latency | ✅ Zero latency | **Extension** (future) |
| Data Transfer | ⚠️ Network overhead | ✅ In-process | **Extension** (future) |

### Final Architecture Decision

**Core Service: Standalone**

- Primary architecture: Standalone Go service with Goa API
- Connects to PostgreSQL as a client (read-only for queries)
- Handles all business logic, LLM integration, and web UI
- Can scale horizontally independent of database

**Future Optimizations: Optional Extensions**

- Extensions would be optional performance optimizations
- Core functionality remains in standalone service
- Extensions enhance performance but are not required
- Clear architectural boundary between service and extensions

### Implementation Notes

1. **Database Connection**: Use read-only PostgreSQL connection for query execution
2. **Security**: Enforce read-only access at database role level
3. **Performance**: Optimize with connection pooling (pgxpool)
4. **Monitoring**: Service-level metrics independent of database
5. **Deployment**: Service can be deployed separately from database

This architectural decision ensures PgQueryNarrative is:
- **Flexible**: Can integrate with any PostgreSQL instance
- **Scalable**: Can scale narrative generation independently
- **Maintainable**: Modern Go development practices
- **Extensible**: Optional extensions can be added later if needed

---

## 4. Technology Stack

### Backend

- **Language**: Go 1.21+
- **API Framework**: Goa v3 (design-first API framework)
- **Router**: Generated from Goa (chi-based)
- **Database Driver**: pgx/v5
- **Query Builder**: sqlc (for app tables)
- **Template Engine**: Templ (type-safe HTML templates)
- **HTMX**: For interactive UI without heavy JavaScript

### Database

- **Primary DB**: PostgreSQL 14+
- **Migrations**: golang-migrate
- **Connection Pooling**: pgxpool

### LLM Integration

- **Primary**: Ollama (local, fully free)
- **Alternatives**: Groq API, Gemini API (both free tiers)
- **Client Pattern**: Interface-based for provider flexibility

### Development Tools

- **Testing**: testify, go-mock
- **Linting**: golangci-lint
- **Formatting**: gofmt, goimports
- **Documentation**: godoc

---

## 5. Project Structure

Following Goa and pgEdge/control-plane patterns:

```
pgquerynarrative/
├── README.md
├── LICENSE                    # MIT or Apache 2.0
├── CONTRIBUTING.md
├── CHANGELOG.md
├── go.mod
├── go.sum
├── Makefile
├── .golangci.yml
├── .gitignore
│
├── cmd/
│   └── server/
│       └── main.go            # Application entry point
│
├── design/                    # Goa API design definitions
│   ├── design.go             # Main service design
│   ├── queries.go            # Query operations
│   ├── reports.go            # Report operations
│   └── types.go              # Shared types
│
├── gen/                       # Generated code (gitignored, regenerated)
│   ├── http/                 # HTTP transport layer
│   ├── grpc/                 # gRPC transport (future)
│   └── pgquerynarrative/       # Service types
│
├── internal/                  # Private application code
│   ├── db/
│   │   ├── migrations/       # SQL migrations
│   │   ├── queries/          # sqlc queries
│   │   ├── connection.go     # DB connection pool
│   │   └── models.go         # Data models
│   │
│   ├── queryrunner/
│   │   ├── runner.go         # SQL execution
│   │   ├── profiler.go       # Result profiling
│   │   └── validator.go      # Query validation
│   │
│   ├── metrics/
│   │   ├── calculator.go     # Metric computation
│   │   ├── timeseries.go     # Time-series analysis
│   │   └── aggregator.go     # Aggregation helpers
│   │
│   ├── llm/
│   │   ├── client.go         # LLM client interface
│   │   ├── ollama.go         # Ollama implementation
│   │   ├── groq.go           # Groq implementation
│   │   ├── gemini.go         # Gemini implementation
│   │   └── prompt.go         # Prompt templates
│   │
│   ├── story/
│   │   ├── generator.go      # Narrative generation
│   │   ├── parser.go         # LLM response parsing
│   │   └── renderer.go       # Markdown rendering
│   │
│   ├── storage/
│   │   ├── queries.go        # Saved query storage
│   │   ├── reports.go        # Report storage
│   │   └── audit.go          # Audit logging
│   │
│   └── config/
│       └── config.go         # Configuration management
│
├── web/                       # Frontend resources
│   ├── static/
│   │   ├── css/
│   │   ├── js/
│   │   └── htmx.min.js
│   ├── templates/
│   │   ├── base.templ
│   │   ├── query.templ
│   │   ├── results.templ
│   │   └── narrative.templ
│   └── assets/               # Images, icons
│
├── api/                       # API documentation
│   ├── openapi.json          # Generated OpenAPI spec
│   └── examples/             # API usage examples
│
├── docs/                      # Documentation
│   ├── index.md
│   ├── getting-started.md
│   ├── architecture.md
│   ├── api-reference.md
│   ├── deployment.md
│   └── development.md
│
├── scripts/
│   ├── setup.sh              # Initial setup
│   ├── seed.sql              # Demo data seeding
│   └── migrate.sh            # Migration runner
│
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml    # Local development
│
├── test/
│   ├── integration/          # Integration tests
│   ├── fixtures/             # Test data
│   └── helpers.go            # Test utilities
│
└── hack/                      # Development utilities
    ├── generate.sh           # Code generation script
    └── tools.go              # Build tools
```

---

## 5.1. Datasets, Examples & Test Data

### Overview

PgQueryNarrative needs sample data for development, testing, and demonstrations. This section covers where to get datasets, how to generate test data, and example queries to use during development.

### Data Sources for Development

#### 1. Public Datasets (Free Sources)

**Kaggle Datasets:**
- **Superstore Sales Dataset**: Real-world retail sales data with categories, regions, dates, and amounts
- **E-commerce Sales Dataset**: Online sales transactions with customer and product data
- **Retail Sales Dataset**: Time-series retail data perfect for trend analysis

**How to Use:**
1. Download dataset from Kaggle (CSV format)
2. Import into PostgreSQL using `COPY` command
3. Use for testing queries and narrative generation

**GitHub Sample Datasets:**
- AdventureWorks for PostgreSQL: Complete sample database with sales, HR, and inventory data
- MySQL test_db converted to PostgreSQL: Employee and department sample data

**Benefits:**
- Real-world data patterns
- Multiple data types (dates, categories, numbers)
- Good for testing various query patterns
- Free and available immediately

#### 2. Synthetic Data Generation

**Why Generate Synthetic Data:**
- Full control over data characteristics
- Can create specific test scenarios
- No privacy concerns
- Customizable data volumes

**Tools for Data Generation:**
- PostgreSQL `generate_series()` function for time-series data
- Random functions for realistic variation
- SQL scripts for bulk data insertion

**Types of Synthetic Data to Generate:**

**Sales Data:**
- Date ranges: Last 12 months with realistic patterns
- Categories: 5-10 product categories with different price ranges
- Regions: 4-6 geographic regions
- Sales representatives: 5-20 active reps
- Amounts: Realistic price ranges per category
- Seasonal patterns: Higher sales in certain months

**User Activity Data:**
- User IDs: 100-1000 unique users
- Event types: Page views, clicks, purchases, signups
- Session data: Duration, page views per session
- Conversion events: Realistic conversion rates

**Orders Data:**
- Customer IDs: Links to user activity data
- Order dates: Time-series over months
- Status: Pending, shipped, delivered, cancelled
- Amounts: Order totals with realistic distributions

#### 3. Demo Data Schema Examples

**Basic Sales Schema:**
- Table: `demo.sales`
- Columns: date, product_category, product_name, quantity, unit_price, total_amount, region, sales_rep
- Volume: 5,000-50,000 rows for development
- Time span: 12-24 months

**User Analytics Schema:**
- Table: `demo.user_activity`
- Columns: user_id, event_date, event_type, page_views, session_duration, conversion
- Volume: 10,000-100,000 rows
- Event types: page_view, click, purchase, signup

**Orders Schema:**
- Table: `demo.orders`
- Columns: order_date, customer_id, total_amount, status, shipping_country
- Volume: 5,000-20,000 orders
- Links to other tables via customer_id

### Example Queries for Testing

**Simple Aggregation:**
- Query: Group sales by product category and calculate totals
- Use Case: Testing basic metrics calculation
- Expected Result: 5-10 rows with revenue totals per category

**Time-Series Analysis:**
- Query: Monthly revenue trends over last 6 months
- Use Case: Testing time-series metric detection
- Expected Result: 6 rows with month and revenue columns

**Top N Analysis:**
- Query: Top 10 products by total sales
- Use Case: Testing top categories metric calculation
- Expected Result: 10 rows ordered by revenue

**Comparative Analysis:**
- Query: Period-over-period revenue comparison (current vs previous month)
- Use Case: Testing trend detection and delta calculations
- Expected Result: Single row with current, previous, and growth percentage

**Complex Multi-Table:**
- Query: Join user activity with orders to calculate lifetime value
- Use Case: Testing query execution with joins
- Expected Result: Multiple rows with user metrics

### Test Data Seed Scripts

**Purpose:**
- Automatically populate database with consistent test data
- Ensure reproducible test environments
- Create specific scenarios for edge case testing

**Script Locations:**
- `scripts/seed.sql` - Main seed script for demo data
- `test/fixtures/` - Test-specific data fixtures
- `scripts/seed_minimal.sql` - Minimal dataset for quick setup

**Seed Script Contents:**
1. Clean existing demo data (if any)
2. Generate realistic sales data with variety
3. Create user activity data
4. Generate orders data
5. Create indexes for performance
6. Verify data was inserted correctly

**Development Data Volume Recommendations:**
- **Quick Testing**: 1,000-5,000 rows per table
- **Normal Development**: 10,000-50,000 rows per table
- **Performance Testing**: 100,000+ rows per table

### Edge Case Test Scenarios

**Empty Result Sets:**
- Query that returns no rows
- Test narrative generation for empty data
- Verify appropriate error handling

**Single Row Results:**
- Aggregated queries returning one row
- Test narrative format for single-value results
- Ensure proper metric handling

**Large Result Sets:**
- Queries returning 1,000+ rows
- Test result limiting functionality
- Verify performance and memory usage

**Null Values:**
- Data with NULL in various columns
- Test handling of missing data
- Verify metrics calculation with NULLs

**Date Edge Cases:**
- Queries with only one day of data
- Queries spanning multiple years
- Queries with irregular date gaps

### Using Real Business Data (Advanced)

**Considerations:**
- Data privacy and PII concerns
- Need for data masking/anonymization
- Compliance requirements (GDPR, HIPAA)
- Data volume and performance impact

**Recommendations:**
- Start with synthetic data for development
- Use anonymized data for testing
- Implement PII detection and masking
- Use read-only access for real data

---

## 6. Database Schema

### Application Schema (`app`)

```sql
-- Saved Queries
CREATE TABLE app.saved_queries (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    sql TEXT NOT NULL,
    description TEXT,
    tags TEXT[] DEFAULT '{}',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    created_by TEXT,
    CONSTRAINT saved_queries_name_check CHECK (char_length(name) > 0),
    CONSTRAINT saved_queries_sql_check CHECK (char_length(sql) > 0)
);

CREATE INDEX idx_saved_queries_tags ON app.saved_queries USING GIN(tags);
CREATE INDEX idx_saved_queries_created_at ON app.saved_queries(created_at DESC);

-- Reports
CREATE TABLE app.reports (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    saved_query_id UUID REFERENCES app.saved_queries(id) ON DELETE SET NULL,
    sql TEXT NOT NULL,
    result_hash TEXT, -- Hash of result sample + metrics for caching
    narrative_md TEXT NOT NULL,
    narrative_json JSONB NOT NULL, -- Structured narrative data
    metrics JSONB NOT NULL, -- Computed stats
    stats JSONB, -- Query execution stats
    llm_model TEXT NOT NULL,
    llm_provider TEXT NOT NULL,
    llm_tokens_used INT,
    llm_response_time_ms INT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    created_by TEXT,
    success BOOLEAN NOT NULL DEFAULT true,
    error TEXT,
    CONSTRAINT reports_sql_check CHECK (char_length(sql) > 0)
);

CREATE INDEX idx_reports_saved_query_id ON app.reports(saved_query_id);
CREATE INDEX idx_reports_result_hash ON app.reports(result_hash);
CREATE INDEX idx_reports_created_at ON app.reports(created_at DESC);
CREATE INDEX idx_reports_llm_model ON app.reports(llm_model);

-- Audit Logs
CREATE TABLE app.audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_type TEXT NOT NULL, -- RUN_QUERY, GENERATE_REPORT, EXPORT_REPORT, etc.
    entity_type TEXT, -- query, report, etc.
    entity_id UUID,
    details JSONB,
    user_id TEXT,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    CONSTRAINT audit_logs_event_type_check CHECK (event_type IN (
        'RUN_QUERY', 'GENERATE_REPORT', 'EXPORT_REPORT', 
        'SAVE_QUERY', 'DELETE_QUERY', 'UPDATE_QUERY',
        'AUTH_FAILURE', 'AUTH_SUCCESS', 'RATE_LIMIT_EXCEEDED',
        'INVALID_SQL_ATTEMPT', 'UNAUTHORIZED_ACCESS'
    ))
);

CREATE INDEX idx_audit_logs_event_type ON app.audit_logs(event_type);
CREATE INDEX idx_audit_logs_entity ON app.audit_logs(entity_type, entity_id);
CREATE INDEX idx_audit_logs_created_at ON app.audit_logs(created_at DESC);

-- Demo Business Schema (for examples)
CREATE SCHEMA IF NOT EXISTS demo;

CREATE TABLE demo.sales (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    date DATE NOT NULL,
    product_category TEXT NOT NULL,
    product_name TEXT NOT NULL,
    quantity INT NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL,
    region TEXT NOT NULL,
    sales_rep TEXT NOT NULL
);

CREATE INDEX idx_sales_date ON demo.sales(date);
CREATE INDEX idx_sales_category ON demo.sales(product_category);
CREATE INDEX idx_sales_region ON demo.sales(region);
```

### Security Setup

```sql
-- Read-only role for query execution
CREATE ROLE pgquerynarrative_readonly;
GRANT USAGE ON SCHEMA demo TO pgquerynarrative_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA demo TO pgquerynarrative_readonly;
ALTER DEFAULT PRIVILEGES IN SCHEMA demo 
    GRANT SELECT ON TABLES TO pgquerynarrative_readonly;

-- Application role with full access to app schema
CREATE ROLE pgquerynarrative_app;
GRANT USAGE ON SCHEMA app TO pgquerynarrative_app;
GRANT ALL ON ALL TABLES IN SCHEMA app TO pgquerynarrative_app;
ALTER DEFAULT PRIVILEGES IN SCHEMA app 
    GRANT ALL ON TABLES TO pgquerynarrative_app;
```

---

## 7. Goa API Design

### Service Definition (`design/design.go`)

```go
package design

import (
    . "goa.design/goa/v3/dsl"
)

var _ = API("pgquerynarrative", func() {
    Title("PgQueryNarrative API")
    Description("Data Storyteller AI - Convert PostgreSQL SQL queries to business narratives")
    Version("v1")
    Server("pgquerynarrative", func() {
        Host("localhost", func() {
            URI("http://localhost:8080")
        })
    })
})
```

### Query Service (`design/queries.go`)

```go
var _ = Service("queries", func() {
    Description("Query execution and management")

    Method("run", func() {
        Description("Execute a SQL query and return results with computed metrics")
        Payload(RunQueryPayload)
        Result(RunQueryResult)
        HTTP(func() {
            POST("/api/v1/queries/run")
            Response(StatusOK)
            Response(StatusBadRequest, ErrorResponse)
            Response(StatusInternalServerError, ErrorResponse)
        })
    })

    Method("list_saved", func() {
        Description("List all saved queries")
        Payload(func() {
            Attribute("tags", ArrayOf(String))
            Attribute("limit", Int32, func() {
                Default(50)
                Minimum(1)
                Maximum(100)
            })
            Attribute("offset", Int32, func() {
                Default(0)
                Minimum(0)
            })
        })
        Result(SavedQueryList)
        HTTP(func() {
            GET("/api/v1/queries/saved")
            Params(func() {
                Param("tags")
                Param("limit")
                Param("offset")
            })
        })
    })

    Method("save", func() {
        Description("Save a query for later use")
        Payload(SaveQueryPayload)
        Result(SavedQuery)
        HTTP(func() {
            POST("/api/v1/queries/saved")
        })
    })

    Method("get_saved", func() {
        Description("Get a saved query by ID")
        Payload(func() {
            Attribute("id", String, func() {
                Format(FormatUUID)
            })
            Required("id")
        })
        Result(SavedQuery)
        HTTP(func() {
            GET("/api/v1/queries/saved/{id}")
        })
    })

    Method("delete_saved", func() {
        Description("Delete a saved query")
        Payload(func() {
            Attribute("id", String, func() {
                Format(FormatUUID)
            })
            Required("id")
        })
        HTTP(func() {
            DELETE("/api/v1/queries/saved/{id}")
            Response(StatusNoContent)
        })
    })
})
```

### Report Service (`design/reports.go`)

```go
var _ = Service("reports", func() {
    Description("Narrative report generation")

    Method("generate", func() {
        Description("Generate a narrative report from query results")
        Payload(GenerateReportPayload)
        Result(Report)
        Error("validation_error", ErrorResponse)
        Error("llm_error", ErrorResponse)
        HTTP(func() {
            POST("/api/v1/reports/generate")
            Response(StatusOK)
            Response(StatusBadRequest, "validation_error")
            Response(StatusServiceUnavailable, "llm_error")
        })
    })

    Method("get", func() {
        Description("Get a generated report by ID")
        Payload(func() {
            Attribute("id", String, func() {
                Format(FormatUUID)
            })
            Required("id")
        })
        Result(Report)
        HTTP(func() {
            GET("/api/v1/reports/{id}")
        })
    })

    Method("list", func() {
        Description("List generated reports")
        Payload(func() {
            Attribute("saved_query_id", String, func() {
                Format(FormatUUID)
            })
            Attribute("limit", Int32, func() {
                Default(50)
            })
            Attribute("offset", Int32, func() {
                Default(0)
            })
        })
        Result(ReportList)
        HTTP(func() {
            GET("/api/v1/reports")
        })
    })
})
```

### Types (`design/types.go`)

```go
var RunQueryPayload = Type("RunQueryPayload", func() {
    Attribute("sql", String, "SQL query to execute", func() {
        MinLength(1)
        MaxLength(10000)
    })
    Attribute("limit", Int32, "Maximum number of rows to return", func() {
        Default(100)
        Minimum(1)
        Maximum(1000)
    })
    Required("sql")
})

var RunQueryResult = Type("RunQueryResult", func() {
    Attribute("columns", ArrayOf(ColumnInfo))
    Attribute("rows", ArrayOf(ArrayOf(Any)))
    Attribute("row_count", Int32)
    Attribute("metrics", Metrics)
    Attribute("execution_time_ms", Int32)
})

var ColumnInfo = Type("ColumnInfo", func() {
    Attribute("name", String)
    Attribute("type", String)
    Attribute("is_numeric", Boolean)
    Attribute("is_date", Boolean)
    Attribute("is_category", Boolean)
})

var Metrics = Type("Metrics", func() {
    Attribute("totals", MapOf(String, Float64))
    Attribute("averages", MapOf(String, Float64))
    Attribute("top_categories", ArrayOf(TopCategory))
    Attribute("time_series", TimeSeriesMetrics)
    Attribute("summary", String)
})

var GenerateReportPayload = Type("GenerateReportPayload", func() {
    Attribute("sql", String, func() {
        MinLength(1)
    })
    Attribute("context", ReportContext)
    Attribute("query_result", RunQueryResult) // Optional, will run query if not provided
    Required("sql", "context")
})

var ReportContext = Type("ReportContext", func() {
    Attribute("audience", String, func() {
        Enum("executive", "product", "ops", "technical")
        Default("executive")
    })
    Attribute("tone", String, func() {
        Enum("concise", "detailed")
        Default("concise")
    })
    Attribute("time_window", String) // e.g., "last 30 days"
})

var Report = Type("Report", func() {
    Attribute("id", String, func() {
        Format(FormatUUID)
    })
    Attribute("narrative", NarrativeContent)
    Attribute("metrics", Metrics)
    Attribute("chart_suggestions", ArrayOf(ChartSuggestion))
    Attribute("created_at", String, func() {
        Format(FormatDateTime)
    })
    Attribute("llm_model", String)
    Required("id", "narrative", "metrics", "created_at")
})

var NarrativeContent = Type("NarrativeContent", func() {
    Attribute("headline", String)
    Attribute("summary", String)
    Attribute("key_takeaways", ArrayOf(String))
    Attribute("notable_changes", ArrayOf(String))
    Attribute("top_drivers", ArrayOf(String))
    Attribute("risks_or_limits", ArrayOf(String))
    Attribute("next_questions", ArrayOf(String))
    Attribute("confidence", String, func() {
        Enum("low", "medium", "high")
    })
})

var ErrorResponse = Type("ErrorResponse", func() {
    Attribute("error", String)
    Attribute("message", String)
    Attribute("code", String)
    Required("error", "message")
})
```

---

## 8. LLM Integration

### Client Interface (`internal/llm/client.go`)

```go
type Client interface {
    GenerateNarrative(ctx context.Context, req GenerateRequest) (*GenerateResponse, error)
    Provider() string
    Model() string
}

type GenerateRequest struct {
    SQL           string
    SampleRows    [][]interface{}
    ColumnInfo    []ColumnInfo
    Metrics       Metrics
    Context       ReportContext
    MaxTokens     int
}

type GenerateResponse struct {
    NarrativeJSON []byte
    TokensUsed    int
    ResponseTime  time.Duration
}
```

### Free LLM Providers

**1. Ollama (Primary - Fully Free)**

- Local installation
- Models: llama3.2, mistral, phi3
- No API limits
- Privacy-first

**2. Groq (Fast Inference)**

- Free tier: 30 requests/minute
- Models: llama-3.1-70b, mixtral-8x7b
- Ultra-fast responses

**3. Google Gemini**

- Free tier: 60 requests/minute
- Models: gemini-1.5-flash, gemini-1.5-pro
- Good quality

### Prompt Engineering

Structured prompts with:

- System instructions (guardrails)
- Few-shot examples
- JSON schema enforcement
- Evidence citation requirements

---

## 8.1. RAG & Vectorization (Retrieval Augmented Generation)

### Overview

RAG (Retrieval Augmented Generation) and vectorization enhance PgQueryNarrative by enabling semantic search, query discovery, and context-aware narrative generation. This section explains how to implement these features as optional enhancements.

### What is RAG?

**RAG (Retrieval Augmented Generation)** combines:
- **Vector embeddings**: Converting text into numerical vectors that capture semantic meaning
- **Semantic search**: Finding similar content based on meaning, not exact text matching
- **Context retrieval**: Providing relevant historical context to LLM for better narratives

**How It Works:**
1. Generate embeddings for saved queries, reports, and schema metadata
2. Store embeddings in PostgreSQL using pgvector extension
3. When generating narratives, search for similar historical data
4. Retrieve relevant context and include in LLM prompt
5. LLM generates more informed, context-aware narratives

### Benefits for PgQueryNarrative

#### 1. Semantic Query Discovery

**Problem:** Users often don't know what queries exist or how to find similar ones.

**Solution with RAG:**
- Search saved queries by meaning, not just keywords
- "Find queries about sales trends" → Finds semantically similar queries
- Natural language query suggestions
- Better query discovery and reuse

**Use Cases:**
- User asks: "Show me revenue by month"
- System finds: Previously saved query "Monthly Sales Revenue Analysis"
- System suggests: "We found a similar query you might want to use"

#### 2. Intelligent Report Context Retrieval

**Problem:** Narratives lack historical context and comparisons.

**Solution with RAG:**
- Find similar historical reports automatically
- Include previous period narratives for comparison
- Cross-reference related metrics from other queries
- Pattern detection across multiple reports

**Use Cases:**
- Generate narrative for current month's sales
- System retrieves previous month's report automatically
- Narrative includes: "This month's revenue increased 15% compared to last month (previous report showed $2.1M vs current $2.4M)"

#### 3. Schema & Domain Knowledge

**Problem:** LLM may not understand what columns mean in business context.

**Solution with RAG:**
- Embed schema metadata with business descriptions
- LLM can query: "What does 'total_amount' column represent?"
- System retrieves: "Total sales amount including discounts, represents revenue"
- Narrative uses correct business terminology

#### 4. Query Result Caching with Semantic Matching

**Problem:** Exact hash matching misses semantically similar queries.

**Solution with RAG:**
- Cache results based on semantic similarity
- Two queries with same intent but different wording → cache hit
- Faster response times
- Reduced LLM costs

### Vector Embeddings Basics

#### What Are Embeddings?

**Embeddings** are numerical representations of text that capture semantic meaning:
- Similar concepts have similar vector values
- Mathematical distance between vectors represents semantic similarity
- Common dimensions: 384, 768, or 1536 values per text

**Example:**
- "Monthly sales revenue" → [0.23, -0.45, 0.67, ...] (384 numbers)
- "Revenue by month" → [0.24, -0.44, 0.68, ...] (very similar)
- "User login count" → [0.12, 0.89, -0.34, ...] (different meaning)

#### Embedding Models

**Recommended for Development (384 dimensions):**
- all-MiniLM-L6-v2: Fast, lightweight, good quality
- sentence-transformers: Easy to use, well-documented
- Via Ollama: Can run locally with Ollama models

**Production Options:**
- all-mpnet-base-v2 (768 dimensions): Better quality, slower
- text-embedding-3-small (1536 dimensions): Best quality, OpenAI compatible

**Why 384 Dimensions?**
- Good balance between quality and performance
- Faster embedding generation (~50ms)
- Lower storage requirements
- Sufficient accuracy for semantic search

### PostgreSQL pgvector Extension

#### What is pgvector?

**pgvector** is a PostgreSQL extension that adds vector data type and similarity search capabilities:
- Store vector embeddings directly in PostgreSQL
- Fast similarity search using specialized indexes
- No need for separate vector database

#### Installation Steps

**1. Install pgvector Extension:**
- Install PostgreSQL with pgvector support (PostgreSQL 11+)
- Enable extension in database: `CREATE EXTENSION vector;`
- Verify installation: `\dx vector` should show enabled

**2. Add Vector Columns:**
- Add `vector(384)` column type to tables
- Store embeddings in PostgreSQL alongside text data
- No external vector database needed

**3. Create Vector Indexes:**
- Use IVFFlat index for smaller datasets (< 100K vectors)
- Use HNSW index for larger datasets (> 100K vectors)
- Configure index parameters based on data volume

#### Schema Updates for Vectorization

**Enhanced Saved Queries Table:**
- Add `embedding vector(384)` column
- Stores semantic representation of query name + description
- Enables semantic search of saved queries

**Enhanced Reports Table:**
- Add `narrative_embedding vector(384)` column
- Stores embedding of narrative content
- Enables finding similar historical reports
- Add `result_embedding vector(384)` for semantic caching

**Indexes:**
- GIN index on vector columns for similarity search
- Supports cosine similarity, L2 distance, and inner product

### Implementation Strategy

#### Phase 1: Basic Vectorization (MVP Enhancement)

**Goal:** Enable semantic search of saved queries.

**Steps:**
1. Install pgvector extension in PostgreSQL
2. Add embedding columns to saved_queries table
3. Implement embedding generation for new saved queries
4. Add semantic search function for query discovery
5. Update query list endpoint to support semantic search

**Benefits:**
- Users can find queries by meaning
- Better query reuse
- Improved user experience

#### Phase 2: Report Context Retrieval

**Goal:** Include historical context in narratives.

**Steps:**
1. Add embedding columns to reports table
2. Generate embeddings when reports are created
3. Implement semantic search for similar reports
4. Modify narrative generation to include retrieved context
5. Update prompts to use historical context

**Benefits:**
- Narratives include comparisons to previous periods
- More informative and contextual insights
- Better understanding of trends

#### Phase 3: Schema Knowledge Base

**Goal:** LLM understands database schema semantics.

**Steps:**
1. Create schema_knowledge table with embeddings
2. Populate with table and column descriptions
3. Implement schema discovery via embeddings
4. Include schema context in narrative prompts
5. Generate domain-aware narratives

**Benefits:**
- LLM uses correct business terminology
- Better understanding of data meaning
- More accurate narratives

### Performance Considerations

#### Embedding Generation Overhead

**Challenge:** Generating embeddings takes time (50-200ms per item).

**Solution:**
- Generate embeddings asynchronously (background job)
- Don't block query save operations
- Cache embeddings to avoid regeneration

**Implementation Approach:**
1. Save query immediately (synchronous)
2. Generate embedding in background goroutine
3. Update query with embedding when ready
4. Search only considers queries with embeddings

#### Vector Index Performance

**Index Types:**
- **IVFFlat**: Fast to build, good for < 100K vectors, approximate results
- **HNSW**: Slower to build, better for large datasets, very fast queries, high accuracy

**Recommendation:**
- Start with IVFFlat for development
- Upgrade to HNSW if dataset grows beyond 100K vectors
- Monitor query performance and adjust index parameters

#### Storage Requirements

**Storage Calculation:**
- 384 dimensions × 4 bytes (float32) = 1,536 bytes per vector
- 10,000 saved queries × 1.5 KB = ~15 MB
- Index overhead: ~5-10 MB additional
- Total: ~25 MB for 10K queries (very manageable)

**Memory Usage:**
- Indexes loaded into memory for fast queries
- Plan for 2-3x index size in available RAM
- Monitor PostgreSQL memory usage

### Database Performance Impact

#### Positive Impacts

**Faster Semantic Search:**
- Vector similarity search is faster than full-text search on large datasets
- Index-optimized queries: < 10ms for 100K vectors
- Better user experience than keyword search

**Reduced LLM Token Usage:**
- Semantic caching reduces redundant LLM calls
- Retrieved context improves prompts without adding many tokens
- Cost savings on paid LLM APIs

#### Challenges & Mitigations

**Challenge: Initial Index Build Time**
- **IVFFlat**: Seconds to minutes for 100K vectors
- **HNSW**: Minutes for 100K vectors
- **Mitigation**: Build indexes during low-traffic periods, use async index creation

**Challenge: Memory Usage**
- Vector indexes consume RAM
- **Mitigation**: Monitor memory, adjust index parameters, use appropriate index type for dataset size

**Challenge: Embedding Generation Latency**
- Adding embeddings slows save operations
- **Mitigation**: Async embedding generation, don't block on embedding creation

### Security Considerations

**Data Privacy:**
- Embeddings don't expose raw data, but patterns can reveal information
- Embed business data carefully
- Consider PII detection before embedding

**Access Control:**
- Vector columns are in app schema (separate from read-only business data)
- Same security model applies to embeddings
- No additional security concerns beyond normal data access

---

## 8.2. MCP Servers (Model Context Protocol)

### Overview

MCP (Model Context Protocol) is an open protocol that allows LLMs to access external tools and data sources through a standardized interface. This section explains how MCP servers can enhance PgQueryNarrative, though they are **optional** and can be added as future enhancements.

### What is MCP?

**Model Context Protocol (MCP)** is a protocol developed by Anthropic that:
- Provides standardized way for LLMs to call external tools
- Enables LLMs to discover available capabilities automatically
- Allows secure, controlled access to database and system resources
- Supports multiple LLM providers through consistent interface

**Key Concepts:**
- **MCP Server**: Service that exposes tools and data sources via MCP protocol
- **MCP Client**: LLM that connects to MCP server and calls tools
- **Tools**: Capabilities exposed by MCP server (e.g., "get_table_schema", "search_queries")
- **Resources**: Data sources accessible through MCP (e.g., database schemas, saved queries)

### How MCP Can Help PgQueryNarrative

#### Use Case 1: Schema Discovery & Context

**Problem:** LLM may need to understand database schema to generate better narratives.

**Solution with MCP:**
- MCP server exposes "get_table_schema" tool
- LLM can call tool to understand column meanings
- Narrative generation becomes more accurate
- Better business terminology usage

**Example Flow:**
1. User runs query on sales table
2. LLM needs context: "What does 'total_amount' mean?"
3. LLM calls MCP tool: `get_table_schema("demo.sales")`
4. MCP returns: Column descriptions and business meanings
5. LLM uses context: "Revenue totaled $2.4M (total_amount represents sales revenue including discounts)"

#### Use Case 2: Query Discovery & Suggestions

**Problem:** Users don't know what queries exist or how to write them.

**Solution with MCP:**
- MCP server exposes "search_saved_queries" tool
- LLM can search queries by natural language
- Suggest queries based on user questions
- Natural language to query mapping

**Example Flow:**
1. User asks: "Show me sales trends for last month"
2. LLM calls MCP tool: `search_saved_queries("monthly sales trends")`
3. MCP returns: List of similar saved queries
4. LLM suggests: "I found a saved query 'Monthly Sales by Category' - would you like to use it?"

#### Use Case 3: Enhanced Narrative Generation

**Problem:** Narratives lack historical context and related insights.

**Solution with MCP:**
- MCP server exposes "get_historical_context" tool
- LLM can retrieve similar past reports
- Include period-over-period comparisons
- Cross-reference related metrics

**Example Flow:**
1. User generates report for current month
2. LLM calls MCP tool: `get_historical_context(query_hash)`
3. MCP returns: Similar reports from previous months
4. LLM includes: "Compared to last month's report, revenue increased 12%"

### MCP Tools for PostgreSQL

**Recommended Tools to Implement:**

**1. Schema Discovery Tools:**
- `get_table_schema`: Get column information for a table
- `list_available_tables`: List all tables in allowed schemas
- `get_table_sample`: Get sample rows to understand data structure

**2. Query Management Tools:**
- `search_saved_queries`: Search queries by keywords or semantic similarity
- `get_saved_query`: Retrieve a specific saved query
- `suggest_queries`: Suggest queries based on natural language question

**3. Context Retrieval Tools:**
- `get_historical_context`: Find similar historical reports
- `get_related_metrics`: Get related metrics from other tables
- `get_report_summary`: Get summary of previous reports

**4. Data Access Tools:**
- `get_table_sample`: Sample data from tables (limited, read-only)
- `execute_readonly_query`: Execute validated read-only queries (with restrictions)

### Implementation Approach

#### Phase 1: MVP Without MCP

**Focus:** Core functionality without MCP complexity.

**Benefits:**
- Faster initial development
- Less complexity
- Can add MCP later as enhancement

**Core Features:**
- Query execution
- Metrics calculation
- Narrative generation
- Basic saved queries

#### Phase 2: Add MCP as Optional Enhancement

**When to Add:** After MVP is stable and working.

**Approach:**
- Make MCP completely optional (feature flag)
- Core system works without MCP
- Gradual rollout for testing

**Implementation Steps:**
1. Create MCP server component (separate service or integrated)
2. Define tools to expose (start with schema discovery)
3. Integrate MCP client with LLM interface
4. Test with one LLM provider first (Groq or Gemini - better tool support)
5. Add more tools incrementally

#### Phase 3: Advanced MCP Features

**Future Enhancements:**
- Natural language to SQL query generation
- Intelligent query suggestions
- Advanced context retrieval
- Multi-database support

### Architecture with MCP

**MCP as Middleware Layer:**
- MCP server sits between LLM and PostgreSQL
- LLM makes tool calls through MCP protocol
- MCP server validates and executes requests
- Results returned to LLM for narrative generation

**Optional Integration:**
- MCP can be disabled via configuration
- System works with or without MCP
- No breaking changes to core functionality

### Benefits & Trade-offs

#### Benefits

**Better LLM Capabilities:**
- LLM can discover schema automatically
- Understand data context better
- Generate more accurate narratives

**Modular Design:**
- Tools can be added independently
- Clear separation of concerns
- Easier to test and maintain

**Future-Proof:**
- Standard protocol (not vendor-specific)
- Works with multiple LLM providers
- Extensible tool ecosystem

#### Trade-offs

**Complexity:**
- Additional component to maintain
- Learning curve for MCP protocol
- More moving parts

**Performance:**
- Additional network calls for tool execution
- Slight latency increase
- Requires LLM with function calling support

**LLM Compatibility:**
- Requires LLM with function calling/tool use support
- Ollama: Partial support (varies by model)
- Groq/Gemini: Good support
- May limit LLM provider options initially

### Recommended Approach

**For Beginners:**
1. **Start without MCP** - Focus on core functionality first
2. **Learn basics** - Get comfortable with query execution and narrative generation
3. **Add MCP later** - Once core system is stable, add MCP as enhancement
4. **Start simple** - Begin with schema discovery tools only

**Development Timeline:**
- **MVP**: Without MCP (simpler, faster)
- **Phase 2**: Add basic MCP with schema tools
- **Phase 3**: Expand MCP with query suggestions and context retrieval

**When MCP is Most Valuable:**
- Large number of saved queries (hard to discover)
- Complex schema (LLM needs context)
- Need for historical comparisons
- Natural language query interface requirements

### Learning Resources

**MCP Protocol:**
- Official MCP documentation: Model Context Protocol specification
- Example implementations in Go
- Community MCP servers for reference

**Function Calling with LLMs:**
- OpenAI function calling guide (concepts apply to other providers)
- Groq tools documentation
- Gemini function calling examples

---

## 9. Key Features Implementation

### 9.1 Result Profiling

Automatically detect:

- Column types (numeric, date, category)
- Time-series patterns
- Grouped data structures
- Measure and dimension candidates

### 9.2 Metrics Calculation

Compute:

- Aggregates (sum, avg, min, max)
- Top N categories by measure
- Period-over-period changes
- Trend direction (up/down/flat)
- Contribution percentages

### 9.3 Narrative Generation

Structured output with:

- Evidence-based claims only
- Citations to source metrics
- Confidence levels
- Limitations acknowledgment
- Suggested follow-up questions

### 9.4 Guardrails

- Only use data from query results
- No invented causes or explanations
- Clear limitations statements
- Confidence scoring
- Validation of all numeric claims

---

## 10. Error Handling

Following pgEdge patterns:

```go
// Error types
type ErrorCode string

const (
    ErrCodeValidation     ErrorCode = "VALIDATION_ERROR"
    ErrCodeSQLExecution   ErrorCode = "SQL_EXECUTION_ERROR"
    ErrCodeLLMGeneration  ErrorCode = "LLM_GENERATION_ERROR"
    ErrCodeNotFound       ErrorCode = "NOT_FOUND"
    ErrCodeInternal       ErrorCode = "INTERNAL_ERROR"
)

type APIError struct {
    Code    ErrorCode
    Message string
    Details map[string]interface{}
}

func (e *APIError) Error() string {
    return e.Message
}
```

Goa automatically handles validation errors. Service errors are converted to proper HTTP responses.

---

## 10.1. Security Architecture

PgQueryNarrative implements a defense-in-depth security strategy with multiple layers of protection across database access, API security, input validation, and data privacy.

### Security Principles

1. **Least Privilege**: Minimum required permissions at every layer
2. **Defense in Depth**: Multiple security controls at different layers
3. **Fail Secure**: System fails in a secure state
4. **Input Validation**: Validate and sanitize all inputs
5. **Audit Logging**: Comprehensive audit trail for security events
6. **Data Privacy**: Protect sensitive data at rest and in transit

---

## 11. Security Implementation

### 11.1. Database Security

#### Read-Only Database Access

**Query Execution Role** (`pgquerynarrative_readonly`):

```sql
-- Create read-only role
CREATE ROLE pgquerynarrative_readonly;

-- Grant schema access (demo schema for example data)
GRANT USAGE ON SCHEMA demo TO pgquerynarrative_readonly;

-- Grant SELECT only (no INSERT, UPDATE, DELETE, DROP, etc.)
GRANT SELECT ON ALL TABLES IN SCHEMA demo TO pgquerynarrative_readonly;

-- Set default privileges for future tables
ALTER DEFAULT PRIVILEGES IN SCHEMA demo 
    GRANT SELECT ON TABLES TO pgquerynarrative_readonly;

-- Deny all other operations explicitly
REVOKE ALL ON SCHEMA demo FROM pgquerynarrative_readonly;
REVOKE CREATE ON SCHEMA demo FROM pgquerynarrative_readonly;
```

**Security Benefits**:
- Prevents accidental data modification
- Limits attack surface (no write operations possible)
- Protects against SQL injection data exfiltration (still vulnerable to SELECT injection)
- Clear separation between query execution and application operations

#### Application Role (`pgquerynarrative_app`)

**Separate Role for Application Operations**:

```sql
-- Application role for saved queries, reports, audit logs
CREATE ROLE pgquerynarrative_app;

-- Full access to app schema only
GRANT USAGE ON SCHEMA app TO pgquerynarrative_app;
GRANT ALL ON ALL TABLES IN SCHEMA app TO pgquerynarrative_app;
ALTER DEFAULT PRIVILEGES IN SCHEMA app 
    GRANT ALL ON TABLES TO pgquerynarrative_app;

-- No access to business data schemas
REVOKE ALL ON SCHEMA demo FROM pgquerynarrative_app;
REVOKE ALL ON SCHEMA public FROM pgquerynarrative_app;
```

**Connection Pooling**:
- Separate connection pools for read-only and app operations
- pgxpool manages connection limits and timeouts
- Connection limits prevent resource exhaustion

#### Row-Level Security (Optional)

For organizations requiring additional data isolation:

```sql
-- Enable RLS on sensitive tables
ALTER TABLE demo.sales ENABLE ROW LEVEL SECURITY;

-- Create policy for read-only role
CREATE POLICY sales_readonly_policy ON demo.sales
    FOR SELECT
    TO pgquerynarrative_readonly
    USING (true);  -- Or add custom filtering logic
```

### 11.2. SQL Injection Prevention

#### Multi-Layer Protection

**1. Query Validation**

```go
// internal/queryrunner/validator.go
type Validator struct {
    allowedSchemas map[string]bool
    maxQueryLength int
    disallowedKeywords []string
}

func (v *Validator) ValidateQuery(sql string) error {
    // Check query length
    if len(sql) > v.maxQueryLength {
        return ErrQueryTooLong
    }
    
    // Parse SQL to validate syntax
    stmt, err := pg_query.Parse(sql)
    if err != nil {
        return ErrInvalidSQL
    }
    
    // Ensure only SELECT statements
    if stmt.GetStmt().GetSelectStmt() == nil {
        return ErrOnlySelectAllowed
    }
    
    // Check for disallowed keywords
    if containsDisallowedKeywords(sql, v.disallowedKeywords) {
        return ErrDisallowedKeyword
    }
    
    // Validate schema access
    schemas := extractReferencedSchemas(stmt)
    for _, schema := range schemas {
        if !v.allowedSchemas[schema] {
            return ErrSchemaNotAllowed
        }
    }
    
    return nil
}
```

**2. Parameter Binding** (For Dynamic Queries)

```go
// Use parameterized queries when building dynamic SQL
// Example: If adding WHERE clause dynamically
query := "SELECT * FROM demo.sales WHERE date >= $1"
params := []interface{}{startDate}
```

**3. Query Timeout**

```go
// Set query execution timeout
ctx, cancel := context.WithTimeout(ctx, 30*time.Second)
defer cancel()

// Execute query with timeout
rows, err := conn.Query(ctx, sql, params...)
```

**4. Result Limiting**

```go
// Always enforce result limits
const maxResults = 1000
if limit > maxResults {
    limit = maxResults
}

// Append LIMIT clause if not present
sql = ensureLimitClause(sql, limit)
```

#### Disallowed Operations

**Explicitly Prohibited**:
- `INSERT`, `UPDATE`, `DELETE`, `DROP`, `TRUNCATE`
- `CREATE`, `ALTER`, `GRANT`, `REVOKE`
- `COPY FROM`, `\COPY` (PostgreSQL commands)
- `EXECUTE` (for prepared statements)
- `DECLARE CURSOR` (potential DoS)

**Blocked Keywords**:
```go
disallowedKeywords := []string{
    "INSERT", "UPDATE", "DELETE", "DROP", "TRUNCATE",
    "CREATE", "ALTER", "GRANT", "REVOKE", "EXECUTE",
    "COPY FROM", "DECLARE", "CURSOR", "EXPLAIN ANALYZE",
    "VACUUM", "ANALYZE", "REINDEX", "CLUSTER",
}
```

### 11.3. Input Validation

#### API Input Validation (Goa)

**Automatic Validation via Goa DSL**:

```go
// design/types.go
var RunQueryPayload = Type("RunQueryPayload", func() {
    Attribute("sql", String, "SQL query to execute", func() {
        MinLength(1)
        MaxLength(10000)  // Prevent extremely long queries
        Pattern("^[^;]+$")  // No multiple statements
    })
    Attribute("limit", Int32, func() {
        Default(100)
        Minimum(1)
        Maximum(1000)  // Enforce reasonable limits
    })
    Required("sql")
})
```

**Custom Validation**:

```go
// Additional validation in service layer
func validateQueryInput(req *RunQueryPayload) error {
    // Check for SQL injection patterns
    if containsSQLInjectionPatterns(req.SQL) {
        return ErrPotentialInjection
    }
    
    // Validate query structure
    if err := queryValidator.ValidateQuery(req.SQL); err != nil {
        return err
    }
    
    return nil
}
```

**Sanitization**:

```go
// Sanitize user input before storing
func sanitizeQueryName(name string) string {
    // Remove special characters, limit length
    cleaned := regexp.MustCompile(`[^a-zA-Z0-9\s-_]`).ReplaceAllString(name, "")
    if len(cleaned) > 100 {
        cleaned = cleaned[:100]
    }
    return strings.TrimSpace(cleaned)
}
```

### 11.4. Authentication & Authorization

#### Authentication Options

**1. API Key Authentication** (MVP)

```go
// internal/auth/apikey.go
type APIKeyAuth struct {
    keys map[string]APIKey
}

type APIKey struct {
    Key       string
    UserID    string
    Permissions []string
    ExpiresAt time.Time
}

func (a *APIKeyAuth) Authenticate(ctx context.Context, key string) (*User, error) {
    apiKey, exists := a.keys[key]
    if !exists {
        return nil, ErrInvalidAPIKey
    }
    
    if time.Now().After(apiKey.ExpiresAt) {
        return nil, ErrAPIKeyExpired
    }
    
    return &User{
        ID: apiKey.UserID,
        Permissions: apiKey.Permissions,
    }, nil
}
```

**2. JWT Token Authentication** (Future)

```go
// internal/auth/jwt.go
type JWTAuth struct {
    secretKey []byte
    issuer    string
}

func (a *JWTAuth) GenerateToken(user *User) (string, error) {
    claims := jwt.MapClaims{
        "user_id": user.ID,
        "permissions": user.Permissions,
        "exp": time.Now().Add(24 * time.Hour).Unix(),
        "iat": time.Now().Unix(),
        "iss": a.issuer,
    }
    
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
    return token.SignedString(a.secretKey)
}
```

**3. OAuth 2.0 / OIDC** (Enterprise)

```go
// Support for OAuth 2.0 providers
// - Google OAuth
// - GitHub OAuth
// - Enterprise SSO (SAML/OIDC)
```

#### Authorization

**Role-Based Access Control (RBAC)**:

```go
// internal/auth/rbac.go
type Permission string

const (
    PermissionRunQuery      Permission = "query:run"
    PermissionGenerateReport Permission = "report:generate"
    PermissionSaveQuery     Permission = "query:save"
    PermissionViewReports   Permission = "report:view"
)

func (u *User) HasPermission(perm Permission) bool {
    for _, p := range u.Permissions {
        if p == string(perm) {
            return true
        }
    }
    return false
}
```

**Resource-Level Authorization**:

```go
// Check ownership before allowing operations
func (s *Service) DeleteSavedQuery(ctx context.Context, queryID string, user *User) error {
    query, err := s.storage.GetSavedQuery(ctx, queryID)
    if err != nil {
        return err
    }
    
    // Check ownership
    if query.CreatedBy != user.ID {
        return ErrUnauthorized
    }
    
    return s.storage.DeleteSavedQuery(ctx, queryID)
}
```

### 11.5. API Security

#### HTTPS/TLS

**Transport Security**:

```go
// cmd/server/main.go
func setupTLS(config *config.TLSConfig) (*tls.Config, error) {
    return &tls.Config{
        MinVersion: tls.VersionTLS12,  // Require TLS 1.2+
        CipherSuites: []uint16{
            tls.TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,
            tls.TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305,
            tls.TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,
        },
        PreferServerCipherSuites: true,
        Certificates: []tls.Certificate{cert},
    }, nil
}
```

**HTTP Security Headers**:

```go
// internal/middleware/security.go
func SecurityHeaders() middleware.Middleware {
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            w.Header().Set("X-Content-Type-Options", "nosniff")
            w.Header().Set("X-Frame-Options", "DENY")
            w.Header().Set("X-XSS-Protection", "1; mode=block")
            w.Header().Set("Strict-Transport-Security", "max-age=31536000; includeSubDomains")
            w.Header().Set("Content-Security-Policy", "default-src 'self'")
            w.Header().Set("Referrer-Policy", "strict-origin-when-cross-origin")
            next.ServeHTTP(w, r)
        })
    }
}
```

#### Rate Limiting

```go
// internal/middleware/ratelimit.go
type RateLimiter struct {
    limiter *rate.Limiter
}

func NewRateLimiter(rps int) *RateLimiter {
    return &RateLimiter{
        limiter: rate.NewLimiter(rate.Limit(rps), rps),
    }
}

func (rl *RateLimiter) Limit(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        if !rl.limiter.Allow() {
            http.Error(w, "Rate limit exceeded", http.StatusTooManyRequests)
            return
        }
        next.ServeHTTP(w, r)
    })
}
```

**Per-User Rate Limiting**:

```go
// More sophisticated: per-user rate limits
type UserRateLimiter struct {
    limiters map[string]*rate.Limiter
    mu       sync.RWMutex
}

func (url *UserRateLimiter) Allow(userID string) bool {
    url.mu.RLock()
    limiter, exists := url.limiters[userID]
    url.mu.RUnlock()
    
    if !exists {
        url.mu.Lock()
        limiter = rate.NewLimiter(10, 10)  // 10 requests/second
        url.limiters[userID] = limiter
        url.mu.Unlock()
    }
    
    return limiter.Allow()
}
```

#### CORS Configuration

```go
// internal/middleware/cors.go
func CORS(origins []string) middleware.Middleware {
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            origin := r.Header.Get("Origin")
            
            if contains(origins, origin) {
                w.Header().Set("Access-Control-Allow-Origin", origin)
                w.Header().Set("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS")
                w.Header().Set("Access-Control-Allow-Headers", "Content-Type, Authorization")
                w.Header().Set("Access-Control-Max-Age", "86400")
            }
            
            if r.Method == "OPTIONS" {
                w.WriteHeader(http.StatusNoContent)
                return
            }
            
            next.ServeHTTP(w, r)
        })
    }
}
```

### 11.6. LLM Security

#### Prompt Injection Prevention

**Input Sanitization**:

```go
// internal/llm/prompt.go
func sanitizePrompt(userInput string) string {
    // Remove prompt injection patterns
    patterns := []string{
        "ignore previous instructions",
        "system:",
        "user:",
        "assistant:",
        "```",
        "forget",
    }
    
    sanitized := userInput
    for _, pattern := range patterns {
        sanitized = strings.ReplaceAll(sanitized, pattern, "")
    }
    
    return sanitized
}
```

**Structured Prompts**:

```go
// Use structured prompts with clear boundaries
func buildPrompt(context ReportContext, metrics Metrics, sampleRows [][]interface{}) string {
    return fmt.Sprintf(`You are a data analyst assistant. Generate a business narrative.

SQL Context: %s
Metrics: %s
Sample Data: %s

Instructions:
1. Only use the provided metrics and data
2. Do not invent or assume facts not in the data
3. Cite specific metrics when making claims

Generate narrative:`, context.SQL, metrics.JSON(), formatRows(sampleRows))
}
```

#### Data Privacy with LLMs

**Data Minimization**:

```go
// Only send minimal data to LLM
func prepareLLMPayload(result *QueryResult, maxRows int) *LLMPayload {
    // Limit number of rows sent
    sampleRows := result.Rows
    if len(sampleRows) > maxRows {
        sampleRows = sampleRows[:maxRows]
    }
    
    // Remove sensitive columns if detected
    filteredColumns := filterSensitiveColumns(result.Columns)
    
    return &LLMPayload{
        Columns: filteredColumns,
        SampleRows: anonymizeRows(sampleRows, filteredColumns),
        Metrics: result.Metrics,
    }
}
```

**PII Detection & Masking**:

```go
// Detect and mask PII before sending to LLM
func maskPII(data []byte) []byte {
    // Email addresses
    data = emailRegex.ReplaceAll(data, []byte("[EMAIL]"))
    
    // Phone numbers
    data = phoneRegex.ReplaceAll(data, []byte("[PHONE]"))
    
    // Credit card numbers
    data = creditCardRegex.ReplaceAll(data, []byte("[CARD]"))
    
    return data
}
```

#### LLM Response Validation

```go
// Validate LLM responses before returning
func validateLLMResponse(resp *LLMResponse) error {
    // Check for prompt injection in response
    if containsPromptInjectionPatterns(resp.Narrative) {
        return ErrInvalidLLMResponse
    }
    
    // Validate JSON structure
    if err := json.Unmarshal(resp.NarrativeJSON, &NarrativeContent{}); err != nil {
        return ErrInvalidJSONStructure
    }
    
    return nil
}
```

### 11.7. Data Privacy & Protection

#### Data at Rest

**Encryption**:

- Database encryption at rest (PostgreSQL TDE or filesystem encryption)
- Encrypt sensitive columns if needed
- Secure credential storage (environment variables, secret managers)

**Access Control**:

```go
// Audit who accesses what data
func (s *Service) RunQuery(ctx context.Context, sql string, user *User) (*QueryResult, error) {
    // Log query execution
    s.audit.Log(ctx, &AuditEvent{
        EventType: "RUN_QUERY",
        UserID: user.ID,
        Details: map[string]interface{}{
            "sql_hash": hashSQL(sql),  // Don't log full SQL if sensitive
            "timestamp": time.Now(),
        },
    })
    
    // Execute query
    result, err := s.queryRunner.Run(ctx, sql)
    return result, err
}
```

#### Data in Transit

**TLS Encryption**:
- All API communications over HTTPS
- Database connections use SSL/TLS
- LLM API calls use HTTPS (Groq, Gemini)

**Certificate Validation**:

```go
// Strict certificate validation for LLM APIs
func setupLLMTLS() *tls.Config {
    return &tls.Config{
        InsecureSkipVerify: false,  // Always verify certificates
        MinVersion: tls.VersionTLS12,
    }
}
```

#### Data Minimization

**Only Collect What's Needed**:
- Don't store full query results unnecessarily
- Store hashes for cache lookups instead of full data
- Automatically expire old reports

```go
// Store minimal data for caching
type ReportCache struct {
    SQLHash      string  // Hash of SQL + parameters
    MetricsHash  string  // Hash of computed metrics
    NarrativeID  string  // Reference to generated narrative
    CreatedAt    time.Time
}
```

### 11.8. Audit Logging

#### Comprehensive Audit Trail

```sql
-- Audit log table
CREATE TABLE app.audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_type TEXT NOT NULL,
    entity_type TEXT,
    entity_id UUID,
    user_id TEXT,
    ip_address INET,
    user_agent TEXT,
    details JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Audit Events**:

```go
// internal/storage/audit.go
type AuditEvent struct {
    EventType string
    EntityType string
    EntityID   string
    UserID     string
    IPAddress  string
    UserAgent  string
    Details    map[string]interface{}
    Timestamp  time.Time
}

// Log all security-relevant events
func (s *Service) auditQueryExecution(ctx context.Context, user *User, sql string) {
    s.audit.Log(ctx, &AuditEvent{
        EventType: "RUN_QUERY",
        UserID: user.ID,
        IPAddress: getIPAddress(ctx),
        UserAgent: getUserAgent(ctx),
        Details: map[string]interface{}{
            "sql_length": len(sql),
            "sql_hash": hashSQL(sql),  // Don't log full SQL
        },
    })
}
```

**Security Event Types**:
- `RUN_QUERY` - Query execution
- `GENERATE_REPORT` - Report generation
- `SAVE_QUERY` - Query saved
- `DELETE_QUERY` - Query deleted
- `AUTH_FAILURE` - Authentication failure
- `AUTH_SUCCESS` - Authentication success
- `RATE_LIMIT_EXCEEDED` - Rate limit hit
- `INVALID_SQL_ATTEMPT` - SQL injection attempt
- `UNAUTHORIZED_ACCESS` - Authorization failure

### 11.9. Secret Management

#### Environment Variables

```bash
# .env (never commit to git)
DATABASE_PASSWORD=secure_password_here
LLM_API_KEY=api_key_here
JWT_SECRET=secret_key_here
```

**Load Secrets Securely**:

```go
// internal/config/config.go
func LoadConfig() (*Config, error) {
    return &Config{
        Database: DatabaseConfig{
            Password: os.Getenv("DATABASE_PASSWORD"),
        },
        LLM: LLMConfig{
            APIKey: os.Getenv("LLM_API_KEY"),
        },
        Security: SecurityConfig{
            JWTSecret: os.Getenv("JWT_SECRET"),
        },
    }, nil
}
```

#### Secret Rotation

- Implement secret rotation mechanism
- Support multiple active secrets during rotation
- Update secrets without service restart (via config reload)

```go
// Support secret rotation
type SecretManager struct {
    secrets map[string][]string  // Multiple versions for rotation
    mu      sync.RWMutex
}

func (sm *SecretManager) Validate(secret string) bool {
    sm.mu.RLock()
    defer sm.mu.RUnlock()
    
    for _, versions := range sm.secrets {
        for _, v := range versions {
            if v == secret {
                return true
            }
        }
    }
    return false
}
```

### 11.10. Security Best Practices

#### Code Security

**Dependency Scanning**:

```bash
# Scan dependencies for vulnerabilities
go list -json -deps | nancy sleuth
```

**Security Linting**:

```yaml
# .golangci.yml
linters:
  enable:
    - gosec  # Security-focused linter
    - errcheck
    - staticcheck
```

#### Container Security

**Dockerfile Best Practices**:

```dockerfile
# Use non-root user
FROM golang:1.21-alpine AS builder
WORKDIR /build
COPY . .
RUN go build -o server ./cmd/server

# Final image with minimal attack surface
FROM alpine:latest
RUN addgroup -g 1000 appuser && adduser -u 1000 -G appuser -s /bin/sh -D appuser
COPY --from=builder /build/server /app/server
USER appuser
EXPOSE 8080
CMD ["/app/server"]
```

**Image Scanning**:

```bash
# Scan container images for vulnerabilities
trivy image pgquerynarrative:latest
```

#### Monitoring & Alerting

**Security Metrics**:

```go
// internal/monitoring/security.go
var (
    authFailuresTotal = prometheus.NewCounterVec(
        prometheus.CounterOpts{
            Name: "auth_failures_total",
            Help: "Total number of authentication failures",
        },
        []string{"reason"},
    )
    
    sqlInjectionAttempts = prometheus.NewCounter(
        prometheus.CounterOpts{
            Name: "sql_injection_attempts_total",
            Help: "Total number of SQL injection attempts detected",
        },
    )
)
```

**Alerting**:
- Alert on multiple auth failures from same IP
- Alert on SQL injection attempts
- Alert on rate limit violations
- Alert on unusual query patterns

### 11.11. Security Checklist

**Pre-Production Security Checklist**:

- [ ] Database uses read-only role for queries
- [ ] SQL injection prevention implemented
- [ ] Input validation on all endpoints
- [ ] Authentication required for all API endpoints
- [ ] HTTPS/TLS enabled for all connections
- [ ] Rate limiting configured
- [ ] Security headers set
- [ ] Audit logging enabled
- [ ] Secrets stored securely (not in code)
- [ ] Dependencies scanned for vulnerabilities
- [ ] Container runs as non-root user
- [ ] Error messages don't leak sensitive information
- [ ] CORS configured properly
- [ ] PII detection and masking implemented
- [ ] LLM prompts sanitized
- [ ] Monitoring and alerting configured

---

## 12. Configuration

Environment-based configuration:

```go
type Config struct {
    Server   ServerConfig
    Database DatabaseConfig
    LLM      LLMConfig
    Security SecurityConfig
}

type ServerConfig struct {
    Host         string
    Port         int
    ReadTimeout  time.Duration
    WriteTimeout time.Duration
}

type DatabaseConfig struct {
    Host            string
    Port            int
    Database        string
    User            string
    Password        string
    MaxConnections  int
    ReadOnlyUser    string
    ReadOnlyPassword string
}

type LLMConfig struct {
    Provider       string // ollama, groq, gemini
    Model          string
    BaseURL        string // For Ollama
    APIKey         string // For Groq/Gemini
    MaxTokens      int
    Temperature    float64
    Timeout        time.Duration
}

type SecurityConfig struct {
    // Authentication
    AuthEnabled     bool
    AuthMethod      string // "apikey", "jwt", "oauth"
    JWTSecret       string
    JWTIssuer       string
    APIKeyRequired  bool
    
    // TLS/HTTPS
    TLSEnabled      bool
    TLSCertFile     string
    TLSKeyFile      string
    
    // Rate Limiting
    RateLimitEnabled bool
    RateLimitRPS     int // Requests per second
    RateLimitBurst   int
    
    // CORS
    CORSEnabled     bool
    CORSOrigins     []string
    CORSMethods     []string
    CORSHeaders     []string
    
    // Security Headers
    SecurityHeadersEnabled bool
    
    // Audit Logging
    AuditLogEnabled bool
    AuditLogLevel   string // "minimal", "standard", "verbose"
}
```

---

## 12. Testing Strategy

### Overview

Testing is critical for ensuring PgQueryNarrative works correctly. This section provides beginner-friendly testing procedures and step-by-step instructions for implementing comprehensive tests.

### Testing Philosophy

**Why Test?**
- Catch bugs before they reach production
- Ensure features work as expected
- Build confidence in code changes
- Document expected behavior
- Enable safe refactoring

**Testing Pyramid:**
- **Unit Tests** (base, most tests): Fast, test individual functions
- **Integration Tests** (middle, fewer tests): Test component interactions
- **E2E Tests** (top, fewest tests): Test complete user workflows

### Unit Tests

**Purpose:** Test individual functions and components in isolation.

**What to Test:**

**Query Validation:**
- Valid SQL queries are accepted
- Invalid SQL queries are rejected
- Disallowed keywords are blocked
- Schema access is validated
- Query length limits are enforced

**Test Approach:**
1. Create test cases with various SQL queries (valid and invalid)
2. Call validation function with each query
3. Verify correct acceptance or rejection
4. Check error messages are clear and helpful

**Metrics Calculation:**
- Totals are calculated correctly
- Averages are computed accurately
- Top categories are identified properly
- Time-series trends are detected
- Period-over-period changes are calculated

**Test Approach:**
1. Create sample query results with known values
2. Run metrics calculation function
3. Verify calculated metrics match expected values
4. Test edge cases (empty results, single row, all nulls)

**Result Profiling:**
- Column types are detected correctly (numeric, date, category)
- Time-series patterns are identified
- Grouped data structures are recognized
- Measure and dimension candidates are found

**Test Approach:**
1. Create test data with various column types
2. Run profiling function
3. Verify correct column type detection
4. Check pattern recognition accuracy

**LLM Response Parsing:**
- Valid JSON responses are parsed correctly
- Invalid JSON is handled gracefully
- Missing fields are detected
- Malformed responses return errors
- Response structure is validated

**Test Approach:**
1. Create mock LLM responses (valid and invalid)
2. Run parsing function
3. Verify correct parsing or error handling
4. Check all required fields are present

**Storage Operations:**
- Queries are saved correctly
- Reports are stored properly
- Audit logs are created
- Data retrieval works
- Deletion functions correctly

**Test Approach:**
1. Use test database or in-memory storage
2. Perform save, retrieve, update, delete operations
3. Verify data integrity
4. Check constraints are enforced

### Integration Tests

**Purpose:** Test how components work together.

**What to Test:**

**Database Operations:**
- Connection to PostgreSQL works
- Queries execute correctly
- Results are returned properly
- Transaction handling works
- Connection pooling functions

**Test Approach:**
1. Set up test database (Docker container or test instance)
2. Run migrations to create schema
3. Seed test data
4. Execute database operations
5. Verify results and cleanup

**LLM Client Integration:**
- LLM client connects successfully
- Requests are formatted correctly
- Responses are received and parsed
- Error handling works (timeout, connection failure)
- Different providers work (Ollama, Groq, Gemini)

**Test Approach:**
1. Use mock LLM server or real provider (development)
2. Send test requests
3. Verify responses are handled correctly
4. Test error scenarios (timeout, invalid response)
5. Test with different LLM providers

**API Endpoint Integration:**
- HTTP endpoints respond correctly
- Request validation works
- Response format is correct
- Error responses are proper
- Authentication/authorization (if implemented)

**Test Approach:**
1. Start test server
2. Send HTTP requests to endpoints
3. Verify response status codes
4. Check response body structure
5. Test error cases (invalid input, missing fields)

**Report Generation Workflow:**
- Complete flow from query to narrative works
- Query execution → profiling → metrics → LLM → narrative → storage
- All steps execute successfully
- Data flows correctly between components
- Errors are handled at each step

**Test Approach:**
1. Execute complete workflow end-to-end
2. Verify each step completes successfully
3. Check data is passed correctly between steps
4. Test error scenarios at each step
5. Verify final output (narrative) is valid

### E2E Tests (End-to-End)

**Purpose**: Test complete user workflows from API request to report generation.

**Test Scenarios**:

1. **Query Execution Flow**

   - Execute SQL query via API
   - Validate query results
   - Verify metrics calculation
   - Check response structure

2. **Report Generation Flow**

   - Generate report from query
   - Validate narrative structure
   - Verify LLM integration
   - Check report persistence

3. **Saved Queries Flow**

   - Save query
   - List saved queries
   - Retrieve saved query
   - Generate report from saved query
   - Delete saved query

4. **Error Handling Flow**

   - Invalid SQL query
   - LLM service unavailable
   - Database connection failure
   - Rate limiting

**E2E Test Implementation Steps:**

**Test 1: Query to Report Flow**
1. Setup test environment (database, test data)
2. Send POST request to `/api/v1/queries/run` with test SQL query
3. Verify response status is 200 OK
4. Verify response contains columns, rows, and metrics
5. Verify row count matches expected value
6. Send POST request to `/api/v1/reports/generate` with query and context
7. Verify response status is 200 OK
8. Verify response contains narrative with headline and takeaways
9. Verify report ID is returned
10. Send GET request to `/api/v1/reports/{id}` to retrieve saved report
11. Verify stored report matches generated report
12. Cleanup test data

**Test 2: Saved Queries Flow**
1. Send POST request to save a query
2. Verify query is saved and ID is returned
3. Send GET request to list saved queries
4. Verify saved query appears in list
5. Send GET request to retrieve specific saved query
6. Verify query details match original
7. Generate report from saved query
8. Verify report is created successfully
9. Send DELETE request to remove saved query
10. Verify query is deleted (404 on retrieval)

**Test 3: Error Handling Flow**
1. Send invalid SQL query (syntax error)
2. Verify error response with clear message
3. Send query with disallowed keywords (INSERT, DELETE)
4. Verify error response indicating security violation
5. Test database connection failure scenario
6. Verify graceful error handling
7. Test LLM service unavailable scenario
8. Verify appropriate error response

**E2E Test Infrastructure:**

- **Test Containers**: Use testcontainers-go for PostgreSQL
- **Mock LLM**: Use in-memory mock for LLM responses
- **Test Data**: Seed scripts with consistent test data
- **Test Helpers**: Reusable setup/teardown utilities

**E2E Test Execution**:

- `make test-e2e` - Run all E2E tests
- `make test-e2e-slow` - Include slow LLM integration tests
- `make test-e2e-docker` - Run with Docker containers

### Test Data Setup

**Purpose:** Ensure tests have consistent, predictable data.

**Seed Scripts:**
1. Create seed script with realistic test data
2. Generate sales data for last 6 months
3. Include various product categories and regions
4. Ensure data is deterministic (same data every test run)
5. Use fixtures for specific test scenarios

**Test Fixtures:**
1. Create fixture files for common test scenarios
2. Edge case fixtures: empty results, single row, large datasets
3. Query fixtures: sample SQL queries for testing
4. Result fixtures: expected query results for validation

**Fixture Organization:**
- `test/fixtures/queries/` - Sample SQL queries
- `test/fixtures/results/` - Expected query results
- `test/fixtures/data/` - Seed data for specific scenarios
- `test/fixtures/narratives/` - Expected narrative outputs for validation

**Using Test Containers:**
1. Use testcontainers-go for PostgreSQL
2. Spin up fresh database for each test run
3. Run migrations automatically
4. Seed test data
5. Run tests
6. Tear down database after tests

**Benefits:**
- Isolated test environment
- No dependencies on external database
- Consistent test conditions
- Can run tests in parallel

### Testing Procedures by Phase

**Phase 1: Foundation Testing**
1. Test project structure setup
2. Test database connection
3. Test migration execution
4. Test basic query execution
5. Verify error handling for invalid queries

**Phase 2: Core Features Testing**
1. Test result profiling with various data types
2. Test metrics calculation with known data
3. Test Ollama LLM integration (or mock)
4. Test narrative generation end-to-end
5. Verify narrative structure and content

**Phase 3: UI & Polish Testing**
1. Test web UI loads correctly
2. Test query editor functionality
3. Test saved queries management
4. Test report display
5. Test export functionality

**Phase 4: Enhancements Testing**
1. Test time-series analysis
2. Test additional LLM providers
3. Test error handling improvements
4. Test performance optimizations

### Test Execution Workflow

**Daily Development Testing:**
1. Run unit tests before committing: `make test-unit`
2. Run integration tests before pushing: `make test-integration`
3. Run linter: `make lint`
4. Verify all tests pass locally

**Pre-commit Testing:**
1. Run full test suite: `make test`
2. Check test coverage: `make test-coverage`
3. Verify coverage meets goals (90%+ for unit tests)
4. Fix any failing tests before committing

**CI/CD Testing:**
1. Automated test runs on pull requests
2. Full test suite including E2E tests
3. Coverage reports generated automatically
4. Tests must pass before merge approval

**Release Testing:**
1. Full E2E test suite
2. Performance testing
3. Load testing (if applicable)
4. Security scanning
5. Manual smoke testing

### Test Coverage Goals

**Unit Tests:**
- Goal: 90%+ code coverage
- Focus: Core logic (query runner, metrics, profiler)
- Measure: Use `go test -cover` to see coverage
- Report: Generate coverage reports with `make test-coverage`

**Integration Tests:**
- Goal: 80%+ coverage of component interactions
- Focus: Database operations, LLM integration, API endpoints
- Measure: Test all major component interactions
- Report: Separate integration test reports

**E2E Tests:**
- Goal: All critical user flows covered
- Focus: Complete workflows from user request to response
- Measure: Count of test scenarios, not code coverage
- Report: E2E test results in CI/CD output

### Beginner-Friendly Testing Tips

**Start Simple:**
1. Write one test at a time
2. Test happy path first (normal operation)
3. Add edge cases gradually
4. Don't worry about 100% coverage initially

**Use Test Helpers:**
1. Create helper functions for common test setup
2. Reuse test fixtures across tests
3. Create test utilities for repeated patterns
4. Keep tests DRY (Don't Repeat Yourself)

**Readable Tests:**
1. Use descriptive test names
2. Include comments explaining test purpose
3. Keep tests focused (one thing per test)
4. Verify specific, clear assertions

**Common Pitfalls to Avoid:**
1. Don't test implementation details (test behavior instead)
2. Don't make tests dependent on execution order
3. Don't use real external services in unit tests (use mocks)
4. Don't ignore flaky tests (fix or remove them)
5. Don't write tests that are too complex (simplify if needed)

### Mock Services

**Why Mock?**
- Tests run faster (no real API calls)
- Tests are reliable (no network dependencies)
- Tests are isolated (don't affect external services)
- Can test error scenarios easily

**What to Mock:**
- LLM API calls (slow, requires API keys)
- External database connections (for unit tests)
- Time-dependent operations (for deterministic tests)
- Random number generation (for predictable tests)

**Mock Implementation Approach:**
1. Create interface for service (LLM client, database, etc.)
2. Create mock implementation of interface
3. Use mock in tests instead of real service
4. Control mock responses for different test scenarios

---

## 13. Documentation Generation

### Automatic Documentation

**1. Go Documentation (godoc)**

Generate Go API documentation from code comments:

```bash
# View locally
godoc -http=:6060

# Generate HTML
godoc -html ./internal/... > docs/go-api.html
```

**GoDoc Standards**:

- Package comments for all packages
- Exported functions have complete doc comments
- Examples for public APIs
- Follow godoc comment conventions

**Example**:

```go
// Package queryrunner provides SQL query execution and result profiling.
// It handles query validation, execution against read-only database connections,
// and automatic result structure analysis.
package queryrunner

// Run executes a SQL query and returns results with computed metrics.
// It validates the query, executes it against the read-only database connection,
// and profiles the result structure for narrative generation.
//
// Example:
//
//	ctx := context.Background()
//	result, err := runner.Run(ctx, "SELECT * FROM sales LIMIT 10")
//	if err != nil {
//	    return err
//	}
//	fmt.Printf("Rows: %d\n", result.RowCount)
func (r *Runner) Run(ctx context.Context, sql string) (*Result, error) {
    // ...
}
```

**2. OpenAPI/Swagger Documentation**

Goa automatically generates OpenAPI 3.0 specification:

```bash
# Generate OpenAPI spec
goa gen github.com/pgquerynarrative/design -o gen

# Convert to Swagger UI
swagger-codegen generate -i gen/openapi.json -l html -o docs/swagger-ui
```

**OpenAPI Generation**:

- Auto-generated from Goa design files
- Includes all endpoints, types, and validations
- Interactive API explorer
- Request/response schemas

**Location**: `api/openapi.json` (generated)

**3. API Documentation Site**

Using Goa's built-in docs generation:

```bash
# Generate API docs
goa gen github.com/pgquerynarrative/design -o gen

# Build docs site
make docs-site
```

**Documentation Site Structure**:

```
docs/
├── api/
│   ├── index.html          # API overview
│   ├── queries.html        # Query service docs
│   ├── reports.html        # Report service docs
│   └── types.html          # Type definitions
└── swagger-ui/             # Interactive API explorer
```

**4. README Generation**

Use tools to ensure README completeness:

```bash
# Check README badges (using shields.io)
# Validate links
# Ensure all sections present
make validate-readme
```

**5. Markdown Documentation**

Generate docs from code annotations:

```bash
# Extract examples from code
go run hack/extract-examples.go > docs/examples.md

# Generate architecture diagrams
# (using mermaid or graphviz)
make docs-diagrams
```

### Documentation Standards

**Go Code Comments**:

- All exported types, functions, and methods documented
- Package-level comments explain purpose
- Example functions for complex APIs
- Follow godoc formatting conventions

**API Documentation**:

- Complete endpoint descriptions
- Request/response examples
- Error code documentation
- Authentication requirements

**README Standards**:

- Project description
- Quick start guide
- Installation instructions
- Usage examples
- API reference links
- Contributing guidelines
- License information

### Documentation Tools

```makefile
# Makefile targets
.PHONY: docs docs-go docs-api docs-site validate-docs

docs: docs-go docs-api docs-site
	@echo "All documentation generated"

docs-go:
	@echo "Generating Go documentation..."
	@mkdir -p docs/go-api
	@godoc -html ./internal/... > docs/go-api/index.html

docs-api:
	@echo "Generating API documentation..."
	@goa gen ./design -o gen
	@cp gen/openapi.json api/openapi.json

docs-site:
	@echo "Building documentation site..."
	@mkdocs build

validate-docs:
	@echo "Validating documentation..."
	@markdownlint docs/**/*.md
	@golangci-lint run --enable=godot
```

---

## 14. README Standards

### README Structure

Following open source best practices and Go project conventions:

````markdown
# PgQueryNarrative

[![Go Reference](https://pkg.go.dev/badge/github.com/pgquerynarrative/pgquerynarrative.svg)](https://pkg.go.dev/github.com/pgquerynarrative/pgquerynarrative)
[![Go Report Card](https://goreportcard.com/badge/github.com/pgquerynarrative/pgquerynarrative)](https://goreportcard.com/report/github.com/pgquerynarrative/pgquerynarrative)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![CI](https://github.com/pgquerynarrative/pgquerynarrative/workflows/CI/badge.svg)](https://github.com/pgquerynarrative/pgquerynarrative/actions)

> Transform PostgreSQL SQL query results into actionable business narratives using AI.

PgQueryNarrative automatically converts PostgreSQL SQL query results into structured business narratives with evidence-based insights, computed metrics, and exportable reports.

## Features

- 🚀 **Execute SQL Queries** - Run queries against PostgreSQL databases
- 📊 **Automatic Metrics** - Compute trends, deltas, and top contributors
- 🤖 **AI-Powered Narratives** - Generate business-ready reports using free LLM models
- 💾 **Save Queries** - Store and manage frequently used queries
- 📤 **Export Reports** - Download reports as Markdown or JSON
- 🔒 **Secure** - Read-only database access with role-based permissions

## Quick Start

### Prerequisites

- Go 1.21 or later
- PostgreSQL 14 or later
- Docker (optional, for local development)
- Ollama (for local LLM, optional)

### Installation

```bash
# Clone the repository
git clone https://github.com/pgquerynarrative/pgquerynarrative.git
cd pgquerynarrative

# Install dependencies
make setup

# Run database migrations
make migrate

# Start the server
make run
````

The API will be available at `http://localhost:8080`.

### Docker Compose

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f server
```

## Usage

### Run a Query

```bash
curl -X POST http://localhost:8080/api/v1/queries/run \
  -H "Content-Type: application/json" \
  -d '{
    "sql": "SELECT category, SUM(amount) as total FROM sales GROUP BY category",
    "limit": 100
  }'
```

### Generate a Report

```bash
curl -X POST http://localhost:8080/api/v1/reports/generate \
  -H "Content-Type: application/json" \
  -d '{
    "sql": "SELECT category, SUM(amount) as total FROM sales GROUP BY category",
    "context": {
      "audience": "executive",
      "tone": "concise"
    }
  }'
```

See [Documentation](https://docs.pgquerynarrative.com) for more examples.

## Configuration

Configuration is managed through environment variables:

```bash
# Server
PGQUERYNARRATIVE_HOST=0.0.0.0
PGQUERYNARRATIVE_PORT=8080

# Database
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_NAME=pgquerynarrative
DATABASE_USER=pgquerynarrative_app
DATABASE_PASSWORD=your_password

# LLM (Ollama)
LLM_PROVIDER=ollama
LLM_MODEL=llama3.2
LLM_BASE_URL=http://localhost:11434

# LLM (Groq)
LLM_PROVIDER=groq
LLM_MODEL=llama-3.1-70b-versatile
LLM_API_KEY=your_api_key
```

See [Configuration](docs/configuration.md) for all options.

## Development

### Setup Development Environment

```bash
# Install tools
make install-tools

# Run tests
make test

# Run linters
make lint

# Generate code
make generate
```

### Project Structure

```
pgquerynarrative/
├── cmd/server/          # Application entry point
├── design/              # Goa API design definitions
├── gen/                 # Generated code (from Goa)
├── internal/            # Private application code
│   ├── db/             # Database layer
│   ├── queryrunner/    # Query execution
│   ├── metrics/        # Metrics calculation
│   ├── llm/            # LLM integration
│   └── story/          # Narrative generation
├── web/                 # Frontend resources
└── docs/                # Documentation
```

See [Architecture](docs/architecture.md) for details.

## Testing

```bash
# Run all tests
make test

# Run unit tests
make test-unit

# Run integration tests
make test-integration

# Run E2E tests
make test-e2e

# Generate coverage report
make test-coverage
```

## API Documentation

- **Interactive API Explorer**: http://localhost:8080/api/docs
- **OpenAPI Spec**: [api/openapi.json](api/openapi.json)
- **Go Package Docs**: https://pkg.go.dev/github.com/pgquerynarrative/pgquerynarrative

## Contributing

Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) first.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Built with [Goa](https://goa.design) for API design
- Inspired by [pgEdge Control Plane](https://github.com/pgEdge/control-plane)
- LLM support via Ollama, Groq, and Google Gemini
````

### README Checklist

- [ ] Project title and description
- [ ] Badges (build status, license, Go version)
- [ ] Features list
- [ ] Quick start guide
- [ ] Installation instructions
- [ ] Usage examples
- [ ] Configuration documentation
- [ ] Development setup
- [ ] Testing instructions
- [ ] API documentation links
- [ ] Contributing guidelines
- [ ] License information
- [ ] Acknowledgments

---

## 15. GitHub Actions Workflows

### CI/CD Pipeline

**Workflow Files Location**: `.github/workflows/`

### 1. CI Workflow (`.github/workflows/ci.yml`)

```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  lint:
    name: Lint
    runs-on: ubuntu-latest
    steps:
   - uses: actions/checkout@v4
   - uses: actions/setup-go@v5
        with:
          go-version: '1.21'
   - name: Run golangci-lint
        uses: golangci/golangci-lint-action@v3
        with:
          version: latest

  test:
    name: Test
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: pgquerynarrative_test
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
     - 5432:5432

    steps:
   - uses: actions/checkout@v4
   - uses: actions/setup-go@v5
        with:
          go-version: '1.21'
   - name: Cache Go modules
        uses: actions/cache@v3
        with:
          path: ~/go/pkg/mod
          key: ${{ runner.os }}-go-${{ hashFiles('**/go.sum') }}
          restore-keys: |
            ${{ runner.os }}-go-
   - name: Install dependencies
        run: go mod download
   - name: Run migrations
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/pgquerynarrative_test?sslmode=disable
        run: make migrate
   - name: Run tests
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/pgquerynarrative_test?sslmode=disable
        run: make test
   - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.out

  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
   - uses: actions/checkout@v4
   - uses: actions/setup-go@v5
        with:
          go-version: '1.21'
   - name: Generate code
        run: make generate
   - name: Build
        run: make build
   - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: binaries
          path: bin/
````


### 2. E2E Test Workflow (`.github/workflows/e2e.yml`)

```yaml
name: E2E Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
  - cron: '0 0 * * 0'  # Weekly

jobs:
  e2e:
    name: E2E Tests
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: pgquerynarrative
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
     - 5432:5432

    steps:
   - uses: actions/checkout@v4
   - uses: actions/setup-go@v5
        with:
          go-version: '1.21'
   - name: Setup Ollama
        run: |
          curl -fsSL https://ollama.com/install.sh | sh
          ollama pull llama3.2
   - name: Start server
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/pgquerynarrative?sslmode=disable
          LLM_PROVIDER: ollama
          LLM_MODEL: llama3.2
        run: |
          make migrate
          make run &
          sleep 5
   - name: Run E2E tests
        env:
          API_URL: http://localhost:8080
        run: make test-e2e
```

### 3. Release Workflow (`.github/workflows/release.yml`)

```yaml
name: Release

on:
  push:
    tags:
   - 'v*'

jobs:
  release:
    name: Release
    runs-on: ubuntu-latest
    steps:
   - uses: actions/checkout@v4
        with:
          fetch-depth: 0
   - uses: actions/setup-go@v5
        with:
          go-version: '1.21'
   - name: Generate changelog
        run: |
          git fetch --tags
          latest_tag=$(git describe --tags --abbrev=0 HEAD^ 2>/dev/null || echo "")
          if [ -z "$latest_tag" ]; then
            changelog=$(git log --pretty=format:"- %s" HEAD)
          else
            changelog=$(git log --pretty=format:"- %s" ${latest_tag}..HEAD)
          fi
          echo "CHANGELOG<<EOF" >> $GITHUB_ENV
          echo "$changelog" >> $GITHUB_ENV
          echo "EOF" >> $GITHUB_ENV
   - name: Build binaries
        run: make build-release
   - name: Create Release
        uses: softprops/action-gh-release@v1
        with:
          files: bin/*
          body: ${{ env.CHANGELOG }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### 4. Documentation Workflow (`.github/workflows/docs.yml`)

```yaml
name: Documentation

on:
  push:
    branches: [main]
    paths:
   - 'docs/**'
   - 'design/**'
  pull_request:
    branches: [main]
    paths:
   - 'docs/**'
   - 'design/**'

jobs:
  docs:
    name: Generate and Deploy Docs
    runs-on: ubuntu-latest
    steps:
   - uses: actions/checkout@v4
   - uses: actions/setup-go@v5
        with:
          go-version: '1.21'
   - name: Generate API docs
        run: |
          make generate
          make docs-api
   - name: Generate Go docs
        run: make docs-go
   - name: Build docs site
        run: make docs-site
   - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs-site
```

### Workflow Triggers

- **CI**: On push to main/develop and all pull requests
- **E2E**: On push to main, PRs, and weekly schedule
- **Release**: On version tags (v*)
- **Docs**: On changes to docs/ or design/ directories

---

## 16. Git Conventions

### Branch Strategy

**Main Branches**:

- `main` - Production-ready code
- `develop` - Integration branch for features

**Supporting Branches**:

- `feature/*` - New features (e.g., `feature/llm-integration`)
- `bugfix/*` - Bug fixes (e.g., `bugfix/query-validation`)
- `hotfix/*` - Critical production fixes (e.g., `hotfix/security-patch`)
- `docs/*` - Documentation updates (e.g., `docs/api-reference`)

### Commit Message Format

Following [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**:

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples**:

```
feat(queries): add saved queries support

Implement CRUD operations for saved queries with tag-based filtering.

Closes #123

fix(llm): handle Ollama connection timeout

Add retry logic for Ollama API calls with exponential backoff.

docs(api): update query endpoint documentation

Add examples for complex query scenarios.

test(e2e): add end-to-end test for report generation

Verify complete workflow from query to narrative report.
```

### Commit Message Validation

Use git hooks to enforce commit message format:

```bash
# .git/hooks/commit-msg
#!/bin/sh
commit_msg=$(cat "$1")

# Check conventional commit format
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+"; then
    echo "Error: Commit message does not follow Conventional Commits format"
    echo "Format: <type>(<scope>): <subject>"
    exit 1
fi
```

### Pre-commit Hooks

Use pre-commit framework:

```yaml
# .pre-commit-config.yaml
repos:
 - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
   - id: trailing-whitespace
   - id: end-of-file-fixer
   - id: check-yaml
   - id: check-added-large-files
   - id: check-json
   - id: check-toml

 - repo: https://github.com/dnephin/pre-commit-golang
    rev: v0.5.1
    hooks:
   - id: go-fmt
   - id: go-vet
   - id: go-lint
   - id: go-unit-tests
   - id: go-mod-tidy
```

### Pull Request Guidelines

**PR Requirements**:

- All tests pass
- Code coverage maintained or improved
- Documentation updated
- Commits follow conventional format
- PR description explains changes

**PR Template** (`.github/pull_request_template.md`):

```markdown
## Description

Brief description of changes

## Type of Change

- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing

- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] E2E tests added/updated

## Checklist

- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] No new warnings generated
- [ ] Tests pass locally
```

### Git Ignore

Standard Go `.gitignore`:

```gitignore
# Binaries
bin/
*.exe
*.exe~
*.dll
*.so
*.dylib

# Test binary
*.test

# Output
*.out

# Generated code
gen/
internal/db/queries/

# Dependency directories
vendor/

# Go workspace file
go.work

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Environment
.env
.env.local

# Documentation builds
docs-site/
site/

# Coverage
coverage.out
coverage.html
```

---

## 14. Open Source License

**Recommended: MIT License**

Allows maximum adoption while protecting contributors. Alternative: Apache 2.0 for more explicit patent grants.

**License File**: `LICENSE` (MIT License)

**License Headers**: Add license header to all source files:

```go
// Copyright (c) 2025 PgQueryNarrative Contributors
//
// Permission is hereby granted, free of charge, to any person obtaining a copy
// of this software and associated documentation files (the "Software"), to deal
// in the Software without restriction, including without limitation the rights
// to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
// copies of the Software, and to permit persons to whom the Software is
// furnished to do so, subject to the following conditions:
//
// The above copyright notice and this permission notice shall be included in
// all copies or substantial portions of the Software.
```

---

## 15. Development Workflow

### Setup

1. Install Go 1.21+, PostgreSQL 14+, Docker
2. Clone repository
3. Run `make setup` (installs dependencies, runs migrations)
4. Run `make dev` (starts server + database)

### Code Generation

- `make generate` - Regenerate Goa code from design
- `make sqlc` - Generate database query code
- `make docs` - Generate all documentation

### Running

- `make run` - Start server
- `make test` - Run all tests
- `make test-unit` - Run unit tests
- `make test-integration` - Run integration tests
- `make test-e2e` - Run E2E tests
- `make lint` - Run linters
- `make fmt` - Format code

### Git Workflow

1. Create feature branch: `git checkout -b feature/amazing-feature`
2. Make changes and commit: `git commit -m "feat(scope): description"`
3. Push to remote: `git push origin feature/amazing-feature`
4. Create Pull Request
5. Address review feedback
6. Merge after approval

### Pre-commit Checklist

- [ ] Code formatted (`make fmt`)
- [ ] Tests pass (`make test`)
- [ ] Linters pass (`make lint`)
- [ ] Documentation updated
- [ ] Commit message follows conventional format

---

## 16. Beginner-Friendly Implementation Plan

### Overview

This section provides step-by-step instructions for implementing PgQueryNarrative from scratch. Each phase includes detailed tasks, verification steps, and testing procedures. Follow phases in order - each builds on the previous phase.

### Prerequisites Checklist

Before starting, ensure you have:

- [ ] Go 1.21 or later installed (`go version`)
- [ ] PostgreSQL 14+ installed and running (`psql --version`)
- [ ] Git installed (`git --version`)
- [ ] Basic understanding of Go syntax
- [ ] Basic understanding of SQL and PostgreSQL
- [ ] Text editor or IDE (VS Code recommended)
- [ ] Terminal/command line access

**Optional but Recommended:**
- [ ] Docker installed (for easier database setup)
- [ ] Ollama installed (for local LLM testing)
- [ ] Make installed (for convenience commands)

---

## Phase 1: Foundation Setup (Week 1)

### Goal
Set up project structure, create database schema, and implement basic query execution.

### Day 1-2: Project Structure & Dependencies

#### Task 1.1: Initialize Go Module

**Steps:**
1. Create project directory: `mkdir pgquerynarrative && cd pgquerynarrative`
2. Initialize Go module: `go mod init github.com/yourusername/pgquerynarrative`
3. Verify `go.mod` file is created
4. Create directory structure following project structure from Section 5
5. Create main directories: `cmd/server`, `internal`, `design`, `web`, `scripts`, `test`

**Verification:**
- Run `go mod init` - should create go.mod file
- Check directory structure matches Section 5
- All required directories exist

#### Task 1.2: Install Core Dependencies

**Steps:**
1. Install Goa framework: `go get goa.design/goa/v3`
2. Install PostgreSQL driver: `go get github.com/jackc/pgx/v5`
3. Install connection pooling: `go get github.com/jackc/pgx/v5/pgxpool`
4. Install migration tool: `go get -tags 'postgres' github.com/golang-migrate/migrate/v4`
5. Run `go mod tidy` to organize dependencies

**Verification:**
- Check `go.mod` file contains required packages
- Run `go mod verify` to ensure dependencies are valid
- No errors when running `go mod tidy`

#### Task 1.3: Create Basic Project Files

**Steps:**
1. Create `.gitignore` file with Go and PostgreSQL patterns
2. Create `README.md` with project description (can expand later)
3. Create `LICENSE` file (MIT recommended)
4. Create `Makefile` with basic targets (run, test, lint)
5. Create `.golangci.yml` for linting configuration

**Verification:**
- All files are created in correct locations
- `.gitignore` excludes build artifacts and secrets
- `Makefile` has placeholder targets (will implement later)

### Day 3-4: Database Setup

#### Task 1.4: Set Up PostgreSQL Database

**Steps:**
1. Start PostgreSQL server (local or Docker)
2. Create database: `createdb pgquerynarrative` or `CREATE DATABASE pgquerynarrative;`
3. Verify database exists: `psql -l | grep pgquerynarrative`
4. Test connection: `psql -d pgquerynarrative -c "SELECT version();"`

**Verification:**
- Can connect to database
- Database exists and is accessible
- PostgreSQL version is 14 or later

#### Task 1.5: Create Database Schemas

**Steps:**
1. Create `internal/db/migrations/` directory
2. Create migration file: `000001_create_schemas.up.sql`
3. Create `app` schema for application tables
4. Create `demo` schema for demo/business data
5. Create migration file: `000001_create_schemas.down.sql` for rollback
6. Run migration using golang-migrate tool

**Verification:**
- Schemas are created in database: `psql -d pgquerynarrative -c "\dn"`
- Both `app` and `demo` schemas exist
- Migration can be rolled back and re-applied

#### Task 1.6: Create Application Tables

**Steps:**
1. Create migration: `000002_create_app_tables.up.sql`
2. Create `app.saved_queries` table with required columns
3. Create `app.reports` table with required columns
4. Create `app.audit_logs` table with required columns
5. Add appropriate indexes (GIN for arrays, B-tree for dates)
6. Create down migration for rollback
7. Run migrations

**Verification:**
- Tables exist: `psql -d pgquerynarrative -c "\dt app.*"`
- Indexes are created: `psql -d pgquerynarrative -c "\di app.*"`
- Constraints are in place (PRIMARY KEY, CHECK constraints)
- Can insert test row into each table

#### Task 1.7: Create Demo Data Schema

**Steps:**
1. Create migration: `000003_create_demo_schema.up.sql`
2. Create `demo.sales` table with required columns
3. Add indexes on date, category, and region columns
4. Create down migration
5. Run migrations

**Verification:**
- `demo.sales` table exists
- Indexes are created on demo tables
- Table structure matches schema from Section 6

#### Task 1.8: Set Up Database Roles & Permissions

**Steps:**
1. Create read-only role: `pgquerynarrative_readonly`
2. Grant USAGE on demo schema to read-only role
3. Grant SELECT on demo tables to read-only role
4. Set default privileges for future demo tables
5. Create application role: `pgquerynarrative_app`
6. Grant ALL privileges on app schema to app role
7. Set default privileges for future app tables
8. Verify roles have correct permissions

**Verification:**
- Read-only role can SELECT from demo tables but cannot INSERT/UPDATE/DELETE
- App role has full access to app schema
- Test connection with each role to verify permissions

#### Task 1.9: Seed Demo Data

**Steps:**
1. Create `scripts/seed.sql` file
2. Write SQL to generate realistic sales data (5,000-10,000 rows)
3. Include various product categories, regions, and dates
4. Generate data for last 12 months with realistic patterns
5. Run seed script: `psql -d pgquerynarrative -f scripts/seed.sql`
6. Verify data was inserted: `SELECT COUNT(*) FROM demo.sales;`

**Verification:**
- Data count matches expected number of rows
- Data has variety (multiple categories, regions, dates)
- Query returns results: `SELECT * FROM demo.sales LIMIT 10;`

### Day 5-6: Goa API Design

#### Task 1.10: Create Goa API Design Files

**Steps:**
1. Create `design/` directory
2. Create `design/design.go` with API definition
3. Define API title, description, and version
4. Define server configuration
5. Create `design/types.go` with shared types
6. Create `design/queries.go` with query service definition
7. Create `design/reports.go` with report service definition

**Verification:**
- All design files exist in `design/` directory
- Design files can be parsed (no syntax errors)
- API definition includes required services

#### Task 1.11: Define Query Service Operations

**Steps:**
1. Define `run` method for query execution
2. Define `list_saved` method for listing saved queries
3. Define `save` method for saving queries
4. Define `get_saved` method for retrieving saved query
5. Define `delete_saved` method for deleting saved query
6. Add HTTP endpoint definitions for each method
7. Define request and response types

**Verification:**
- All methods are defined in design
- HTTP endpoints are specified (GET, POST, DELETE)
- Request/response types are defined

#### Task 1.12: Define Report Service Operations

**Steps:**
1. Define `generate` method for report generation
2. Define `get` method for retrieving report
3. Define `list` method for listing reports
4. Add HTTP endpoint definitions
5. Define report context and narrative types
6. Define error responses

**Verification:**
- Report service methods are defined
- Context types include audience and tone options
- Error responses are properly defined

#### Task 1.13: Generate Goa Code

**Steps:**
1. Install Goa CLI: `go install goa.design/goa/v3/cmd/goa@latest`
2. Run code generation: `goa gen ./design -o gen`
3. Verify generated code appears in `gen/` directory
4. Check for generation errors in output
5. Add `gen/` to `.gitignore` (generated code shouldn't be committed)

**Verification:**
- `gen/` directory contains generated code
- HTTP server code is generated
- Service interfaces are generated
- No generation errors

### Day 7: Basic Query Execution

#### Task 1.14: Create Database Connection Module

**Steps:**
1. Create `internal/db/connection.go` file
2. Implement connection pool setup using pgxpool
3. Create separate connection pools for read-only and app operations
4. Add connection configuration from environment variables
5. Implement connection health check function
6. Add connection closing functionality

**Verification:**
- Can create connection pool successfully
- Connection pool connects to database
- Health check returns success when database is accessible
- Read-only and app pools connect with different roles

#### Task 1.15: Implement Query Runner - Basic Execution

**Steps:**
1. Create `internal/queryrunner/runner.go` file
2. Implement basic query execution function
3. Execute SQL query using read-only connection pool
4. Fetch query results (columns and rows)
5. Return results as structured data
6. Add query timeout (30 seconds)
7. Enforce result limit (1000 rows maximum)

**Verification:**
- Can execute simple SELECT query
- Results are returned with columns and rows
- Query timeout works (test with slow query)
- Result limit is enforced (test with large result set)

#### Task 1.16: Implement Query Validator - Basic Validation

**Steps:**
1. Create `internal/queryrunner/validator.go` file
2. Implement query length check (max 10,000 characters)
3. Implement check for only SELECT statements
4. Implement disallowed keywords check (INSERT, UPDATE, DELETE, etc.)
5. Implement schema access validation (only allowed schemas)
6. Add clear error messages for each validation failure

**Verification:**
- Valid SELECT queries are accepted
- Invalid queries (INSERT, UPDATE) are rejected
- Queries referencing disallowed schemas are rejected
- Error messages clearly explain why query was rejected

#### Task 1.17: Create Basic API Service

**Steps:**
1. Create service implementation in `internal/service/` directory
2. Implement query service interface (from generated code)
3. Implement basic `run` method that calls query runner
4. Return query results in expected format
5. Handle validation errors properly
6. Return appropriate HTTP responses

**Verification:**
- Service implements generated interface
- Can handle valid query requests
- Returns proper error responses for invalid queries
- Response format matches API design

#### Task 1.18: Create Server Entry Point

**Steps:**
1. Create `cmd/server/main.go` file
2. Set up HTTP server using generated code
3. Register query service with HTTP server
4. Configure server with port and host from environment
5. Add graceful shutdown handling
6. Add basic logging

**Verification:**
- Server starts without errors
- Server listens on configured port (default 8080)
- Can send HTTP request to server (GET /health or similar)
- Server shuts down gracefully on interrupt

#### Task 1.19: Test Query Execution Endpoint

**Steps:**
1. Start server: `make run` or `go run cmd/server/main.go`
2. Send POST request to `/api/v1/queries/run` with test SQL
3. Verify response status is 200 OK
4. Verify response contains columns and rows
5. Verify row count matches database
6. Test with invalid query and verify error response

**Verification:**
- API endpoint responds to requests
- Valid queries return results
- Invalid queries return error responses
- Response format matches API design

**Testing Checklist for Phase 1:**
- [ ] Project structure is correct
- [ ] Dependencies are installed
- [ ] Database is set up with schemas and tables
- [ ] Demo data is seeded
- [ ] Roles and permissions are configured
- [ ] Goa API design is complete
- [ ] Code is generated from design
- [ ] Query execution works
- [ ] Query validation works
- [ ] API endpoint responds correctly
- [ ] All unit tests pass (if written)

---

## Phase 2: Core Features (Week 2)

### Goal
Implement result profiling, metrics calculation, LLM integration, and basic narrative generation.

### Day 8-9: Result Profiling

#### Task 2.1: Implement Column Type Detection

**Steps:**
1. Create `internal/queryrunner/profiler.go` file
2. Implement function to analyze column types from query results
3. Detect numeric columns (INTEGER, DECIMAL, NUMERIC, FLOAT)
4. Detect date columns (DATE, TIMESTAMP, TIMESTAMPTZ)
5. Detect category columns (TEXT with limited distinct values)
6. Store column metadata (name, type, detected characteristics)

**Verification:**
- Numeric columns are correctly identified
- Date columns are detected properly
- Category columns are recognized (few distinct values)
- Column metadata includes all required information

#### Task 2.2: Implement Pattern Detection

**Steps:**
1. Detect time-series patterns (date column present with sequential dates)
2. Detect grouped data structures (GROUP BY patterns)
3. Identify measure columns (numeric columns suitable for aggregation)
4. Identify dimension columns (category or date columns for grouping)
5. Detect empty result sets
6. Detect single-row results (aggregated data)

**Verification:**
- Time-series patterns are correctly identified
- Grouped structures are detected
- Measure and dimension columns are properly categorized
- Edge cases (empty, single row) are handled

#### Task 2.3: Integrate Profiling with Query Runner

**Steps:**
1. Modify query runner to call profiler after executing query
2. Include profile information in query results
3. Add profiling metadata to result structure
4. Ensure profiling doesn't significantly slow down query execution

**Verification:**
- Query results include profile information
- Profiling completes quickly (< 50ms)
- Profile data is accurate and useful
- Integration doesn't break existing functionality

### Day 10-11: Metrics Calculation

#### Task 2.4: Implement Basic Aggregates

**Steps:**
1. Create `internal/metrics/calculator.go` file
2. Implement total calculation (sum of numeric columns)
3. Implement average calculation (avg of numeric columns)
4. Implement minimum and maximum calculations
5. Calculate counts (total rows, distinct values)
6. Handle NULL values in calculations

**Verification:**
- Totals are calculated correctly for known data
- Averages are computed accurately
- Min/max values are identified correctly
- NULL values are handled properly

#### Task 2.5: Implement Top Categories

**Steps:**
1. Identify category columns from profiling
2. Calculate top N categories by measure (revenue, count, etc.)
3. Calculate contribution percentages for top categories
4. Include category value, measure value, and percentage
5. Limit to top 10 categories by default

**Verification:**
- Top categories are correctly identified
- Contribution percentages are accurate
- Categories are sorted by measure value
- Handles cases with fewer than N categories

#### Task 2.6: Implement Time-Series Metrics

**Steps:**
1. Detect time-series data from profiling
2. Calculate period-over-period changes (current vs previous period)
3. Calculate growth rates and percentages
4. Detect trend direction (upward, downward, flat)
5. Calculate moving averages if applicable

**Verification:**
- Period-over-period changes are calculated correctly
- Growth percentages are accurate
- Trend direction is correctly identified
- Handles edge cases (single period, missing periods)

#### Task 2.7: Integrate Metrics with Query Results

**Steps:**
1. Modify query runner to calculate metrics after profiling
2. Include metrics in query result response
3. Ensure metrics calculation is fast (< 100ms for typical queries)
4. Handle edge cases (empty results, single row) gracefully

**Verification:**
- Query results include computed metrics
- Metrics are calculated quickly
- Metrics are accurate for test data
- Edge cases don't cause errors

### Day 12-13: LLM Integration - Ollama Setup

#### Task 2.8: Install and Configure Ollama

**Steps:**
1. Install Ollama (local installation or Docker)
2. Pull recommended model: `ollama pull llama3.2`
3. Start Ollama server (if not auto-started)
4. Verify Ollama is running: `curl http://localhost:11434/api/tags`
5. Test model works: `ollama run llama3.2 "Hello"`

**Verification:**
- Ollama is installed and running
- Model is downloaded and available
- Can generate responses from Ollama
- Ollama API responds to requests

#### Task 2.9: Create LLM Client Interface

**Steps:**
1. Create `internal/llm/client.go` file
2. Define Client interface with required methods
3. Define request and response types
4. Include provider name and model methods
5. Include health check method

**Verification:**
- Interface is defined with all required methods
- Types include all necessary fields
- Interface is extensible for multiple providers

#### Task 2.10: Implement Ollama Client

**Steps:**
1. Create `internal/llm/ollama.go` file
2. Implement Client interface for Ollama
3. Create HTTP client to communicate with Ollama API
4. Implement GenerateNarrative method
5. Handle API requests and responses
6. Parse Ollama API response format
7. Add timeout handling (120 seconds for LLM calls)

**Verification:**
- Can connect to Ollama API
- Can send generate request to Ollama
- Receives responses from Ollama
- Parses responses correctly
- Handles timeouts and errors

#### Task 2.11: Implement Prompt Building

**Steps:**
1. Create `internal/llm/prompt.go` file
2. Build system instructions with guardrails
3. Format query context (SQL, results, metrics)
4. Include audience and tone specifications
5. Add JSON schema requirements for structured output
6. Build complete prompt with all context

**Verification:**
- Prompts include all required information
- Guardrails are clearly stated
- JSON schema is properly formatted
- Prompts are clear and unambiguous

### Day 14: Narrative Generation

#### Task 2.12: Implement Narrative Parser

**Steps:**
1. Create `internal/story/parser.go` file
2. Extract JSON from LLM response (may include markdown formatting)
3. Parse JSON into NarrativeContent structure
4. Validate required fields are present
5. Validate field types and formats
6. Handle parsing errors gracefully

**Verification:**
- Can extract JSON from various response formats
- Parses valid JSON correctly
- Detects and reports parsing errors
- Validates all required fields

#### Task 2.13: Implement Narrative Generator

**Steps:**
1. Create `internal/story/generator.go` file
2. Implement function that orchestrates narrative generation
3. Build prompt from query results and context
4. Call LLM client to generate narrative
5. Parse LLM response into structured narrative
6. Validate narrative structure and content
7. Return complete narrative with metrics

**Verification:**
- Generates narrative from query results
- Narrative includes required sections (headline, takeaways, etc.)
- All claims cite source metrics
- Narrative structure is valid

#### Task 2.14: Integrate Narrative Generation with API

**Steps:**
1. Implement report generation service method
2. Execute query if not provided
3. Calculate metrics from query results
4. Generate narrative using LLM
5. Store report in database
6. Return report to user

**Verification:**
- API endpoint generates reports successfully
- Reports are stored in database
- Report responses include complete narrative
- Error handling works (LLM unavailable, invalid query)

**Testing Checklist for Phase 2:**
- [ ] Result profiling detects column types correctly
- [ ] Pattern detection identifies time-series and grouped data
- [ ] Metrics are calculated accurately
- [ ] Top categories are identified correctly
- [ ] Time-series metrics work properly
- [ ] Ollama client connects and generates responses
- [ ] Prompts are well-formed and include guardrails
- [ ] Narrative parser extracts JSON correctly
- [ ] Narrative generator produces valid narratives
- [ ] API endpoint generates reports successfully
- [ ] All unit tests pass
- [ ] Integration tests pass

---

## Phase 3: UI & Polish (Week 3)

### Goal
Create web user interface, implement saved queries management, and add export functionality.

### Day 15-16: Basic Web UI Setup

#### Task 3.1: Set Up Templ and HTMX

**Steps:**
1. Install Templ: `go install github.com/a-h/templ/cmd/templ@latest`
2. Install HTMX: Download htmx.min.js to `web/static/js/`
3. Create `web/templates/` directory
4. Create `web/static/css/` directory
5. Create basic CSS file for styling

**Verification:**
- Templ CLI is installed and accessible
- HTMX library is in correct location
- Directories are created properly

#### Task 3.2: Create Base Template

**Steps:**
1. Create `web/templates/base.templ` file
2. Define HTML structure (head, body, navigation)
3. Include HTMX library in head
4. Include CSS file
5. Define layout with header, main content area, footer
6. Add navigation links (Query, Saved Queries, Reports)

**Verification:**
- Base template compiles without errors
- HTML structure is correct
- HTMX is included
- Layout is clean and usable

#### Task 3.3: Create Query Page Template

**Steps:**
1. Create `web/templates/query.templ` file
2. Create SQL query editor (textarea)
3. Add Run Query button
4. Add Results display area
5. Add Generate Report button
6. Integrate with base template

**Verification:**
- Query page loads correctly
- Editor is functional
- Buttons are present and styled
- Page integrates with base template

#### Task 3.4: Serve Static Files and Templates

**Steps:**
1. Configure HTTP server to serve static files from `web/static/`
2. Register Templ-generated handlers
3. Add route for query page
4. Test that page loads in browser

**Verification:**
- Static files (CSS, JS) are served correctly
- Query page loads in browser
- HTMX library loads
- No 404 errors for assets

### Day 17-18: HTMX Integration

#### Task 3.5: Implement Query Execution via HTMX

**Steps:**
1. Add HTMX attributes to Run Query button
2. Configure HTMX to POST to `/api/v1/queries/run`
3. Display results in results area
4. Handle loading states (show loading indicator)
5. Handle error states (display error messages)

**Verification:**
- Clicking Run Query sends request
- Results are displayed in page
- Loading indicator shows during request
- Errors are displayed clearly

#### Task 3.6: Create Results Display Template

**Steps:**
1. Create `web/templates/results.templ` file
2. Display query results as table
3. Show column headers
4. Display rows with formatting
5. Show row count and execution time
6. Display computed metrics summary

**Verification:**
- Results table displays correctly
- Data is formatted properly
- Metrics are visible
- Large results are handled (scrollable)

#### Task 3.7: Implement Report Generation via HTMX

**Steps:**
1. Add HTMX attributes to Generate Report button
2. Configure HTMX to POST to `/api/v1/reports/generate`
3. Show loading state during generation (can take 5-10 seconds)
4. Display generated report when complete
5. Handle errors (LLM unavailable, timeout)

**Verification:**
- Report generation triggers correctly
- Loading state is clear to user
- Generated report is displayed
- Errors are handled gracefully

#### Task 3.8: Create Narrative Display Template

**Steps:**
1. Create `web/templates/narrative.templ` file
2. Display headline prominently
3. Show summary section
4. Display key takeaways as list
5. Show notable changes
6. Display top drivers
7. Show risks/limitations
8. Display confidence level

**Verification:**
- Narrative is well-formatted and readable
- All sections are displayed
- Visual hierarchy is clear
- Readable for non-technical users

### Day 19: Saved Queries Management

#### Task 3.9: Implement Save Query API Endpoint

**Steps:**
1. Implement save query service method
2. Validate query name and SQL
3. Save query to database using app connection pool
4. Generate tags automatically or from input
5. Return saved query with ID

**Verification:**
- Can save queries via API
- Queries are stored in database
- Validation works (empty name, invalid SQL)
- Saved queries are retrievable

#### Task 3.10: Implement List Saved Queries

**Steps:**
1. Implement list saved queries service method
2. Support filtering by tags
3. Support pagination (limit/offset)
4. Return list of saved queries with metadata
5. Sort by creation date (newest first)

**Verification:**
- Can list saved queries via API
- Tag filtering works
- Pagination works correctly
- Results are sorted properly

#### Task 3.11: Create Saved Queries UI

**Steps:**
1. Create saved queries page template
2. Display list of saved queries
3. Show query name, description, tags, creation date
4. Add button to load query into editor
5. Add button to generate report from saved query
6. Add delete button with confirmation

**Verification:**
- Saved queries page displays list
- Can load query into editor
- Can generate report from saved query
- Can delete saved queries

#### Task 3.12: Implement Query Loading

**Steps:**
1. Add HTMX attribute to "Load Query" buttons
2. Fetch saved query from API
3. Populate query editor with SQL
4. Optionally run query automatically
5. Update UI to show query is loaded

**Verification:**
- Clicking Load Query populates editor
- SQL is displayed correctly
- Can run loaded query
- UI updates appropriately

### Day 20-21: Export & Polish

#### Task 3.13: Implement Report Export

**Steps:**
1. Create markdown export function
2. Format narrative as markdown document
3. Include metadata (date, query, metrics summary)
4. Create JSON export function
5. Add export buttons to narrative display

**Verification:**
- Markdown export creates valid markdown
- JSON export creates valid JSON
- Export buttons work
- Downloaded files are properly formatted

#### Task 3.14: Add Report List Page

**Steps:**
1. Implement list reports API endpoint
2. Support filtering by saved_query_id
3. Support pagination
4. Create reports list page template
5. Display report metadata (date, query, headline)
6. Add link to view full report

**Verification:**
- Can list reports via API
- Reports page displays correctly
- Can view individual reports
- Filtering and pagination work

#### Task 3.15: Polish UI/UX

**Steps:**
1. Improve CSS styling for better appearance
2. Add loading indicators for all async operations
3. Improve error message display
4. Add success messages for actions
5. Improve responsive design (works on mobile)
6. Add keyboard shortcuts if helpful

**Verification:**
- UI looks professional and clean
- Loading states are clear
- Error messages are helpful
- UI works on different screen sizes

**Testing Checklist for Phase 3:**
- [ ] Web UI loads correctly
- [ ] Query editor works
- [ ] Results are displayed properly
- [ ] Report generation works via UI
- [ ] Saved queries can be saved
- [ ] Saved queries can be listed and loaded
- [ ] Reports can be exported (Markdown and JSON)
- [ ] UI is responsive and usable
- [ ] All UI tests pass (if written)
- [ ] Manual testing of complete user flows

---

## Phase 4: Enhancements (Week 4)

### Goal
Add time-series analysis, chart suggestions, additional LLM providers, and improve error handling.

### Day 22-23: Advanced Metrics

#### Task 4.1: Enhance Time-Series Analysis

**Steps:**
1. Improve time-series pattern detection
2. Detect seasonal patterns (monthly, quarterly, yearly)
3. Calculate compound growth rates
4. Detect anomalies in time-series data
5. Provide more detailed trend analysis

**Verification:**
- Seasonal patterns are detected correctly
- Growth rates are calculated accurately
- Anomalies are identified when present
- Trend analysis is more detailed

#### Task 4.2: Implement Chart Suggestions

**Steps:**
1. Analyze query results to suggest chart types
2. Suggest bar charts for category comparisons
3. Suggest line charts for time-series data
4. Suggest pie charts for composition analysis
5. Include chart suggestion in report response
6. Display chart suggestions in UI

**Verification:**
- Chart suggestions are relevant to data
- Suggestions are based on result structure
- Multiple chart types are suggested appropriately
- Suggestions are displayed in UI

#### Task 4.3: Add Period Comparisons

**Steps:**
1. Enable automatic period-over-period comparisons
2. Compare to previous period (day, week, month, year)
3. Calculate percentage changes
4. Include comparisons in narrative generation
5. Highlight significant changes

**Verification:**
- Period comparisons are calculated correctly
- Comparisons are included in narratives
- Significant changes are highlighted
- Works for different time periods

### Day 24-25: Additional LLM Providers

#### Task 4.4: Implement Groq Client

**Steps:**
1. Get Groq API key from console.groq.com
2. Create `internal/llm/groq.go` file
3. Implement Client interface for Groq
4. Configure HTTP client for Groq API
5. Implement API request/response handling
6. Add Groq to configuration options

**Verification:**
- Can connect to Groq API
- Can generate narratives using Groq
- Groq responses are parsed correctly
- Groq is faster than Ollama for testing

#### Task 4.5: Implement Gemini Client

**Steps:**
1. Get Gemini API key from aistudio.google.com
2. Create `internal/llm/gemini.go` file
3. Implement Client interface for Gemini
4. Configure HTTP client for Gemini API
5. Implement API request/response handling
6. Add Gemini to configuration options

**Verification:**
- Can connect to Gemini API
- Can generate narratives using Gemini
- Gemini responses are parsed correctly
- Provider switching works

#### Task 4.6: Add LLM Provider Selection

**Steps:**
1. Add provider selection to configuration
2. Implement provider factory function
3. Support switching providers via environment variable
4. Update documentation with provider setup instructions
5. Test all three providers work correctly

**Verification:**
- Can switch between providers via config
- All providers generate narratives successfully
- Configuration is clear and documented
- Default provider (Ollama) works

### Day 26-27: Error Handling & Robustness

#### Task 4.7: Improve Error Handling

**Steps:**
1. Review all error paths in code
2. Add clear, user-friendly error messages
3. Add error logging for debugging
4. Implement error recovery where possible
5. Add retry logic for LLM API calls
6. Handle network timeouts gracefully

**Verification:**
- All errors have clear messages
- Errors are logged for debugging
- Retry logic works for transient failures
- Users see helpful error messages

#### Task 4.8: Add Input Validation

**Steps:**
1. Validate all API inputs thoroughly
2. Check SQL query length and complexity
3. Validate report context values
4. Sanitize user inputs where appropriate
5. Return validation errors early

**Verification:**
- Invalid inputs are rejected with clear messages
- Validation happens before processing
- Error messages help users fix issues
- Security checks prevent malicious input

#### Task 4.9: Add Performance Monitoring

**Steps:**
1. Add execution time tracking for queries
2. Track LLM response times
3. Log slow operations (> 1 second)
4. Add basic metrics collection
5. Monitor memory usage

**Verification:**
- Execution times are tracked
- Slow operations are identified
- Metrics are logged
- Performance is acceptable

### Day 28: Testing & Documentation

#### Task 4.10: Comprehensive Testing

**Steps:**
1. Write unit tests for all new features
2. Add integration tests for provider switching
3. Test error scenarios thoroughly
4. Test edge cases (empty data, very large results)
5. Run full test suite and fix any failures
6. Achieve target test coverage (90%+ for core logic)

**Verification:**
- All tests pass
- Test coverage meets goals
- Edge cases are covered
- Error scenarios are tested

#### Task 4.11: Documentation Updates

**Steps:**
1. Update README with all features
2. Document all API endpoints
3. Add setup instructions for all LLM providers
4. Document configuration options
5. Add troubleshooting guide
6. Include example queries and expected outputs

**Verification:**
- README is comprehensive and up-to-date
- API documentation is complete
- Setup instructions work for new users
- Examples are clear and helpful

**Testing Checklist for Phase 4:**
- [ ] Time-series analysis is enhanced
- [ ] Chart suggestions are accurate
- [ ] Period comparisons work correctly
- [ ] Groq integration works
- [ ] Gemini integration works
- [ ] Provider switching works
- [ ] Error handling is robust
- [ ] Input validation is comprehensive
- [ ] Performance monitoring is in place
- [ ] All tests pass
- [ ] Documentation is complete

---

## Phase 5: Advanced Features (Future - Optional)

### RAG & Vectorization (Optional Enhancement)

**When to Add:** After MVP is stable and working well.

#### Task 5.1: Set Up pgvector Extension

**Steps:**
1. Install pgvector extension in PostgreSQL
2. Enable extension in database
3. Verify extension is installed and working
4. Test basic vector operations

**Verification:**
- pgvector extension is enabled
- Can create vector columns
- Basic vector operations work

#### Task 5.2: Add Embedding Columns to Tables

**Steps:**
1. Create migration to add embedding columns to saved_queries
2. Add embedding columns to reports table
3. Create vector indexes using IVFFlat (or HNSW for large datasets)
4. Run migrations

**Verification:**
- Embedding columns are added
- Indexes are created
- Database accepts vector data

#### Task 5.3: Implement Embedding Generation

**Steps:**
1. Choose embedding model (384 dimensions recommended)
2. Set up embedding generation service (local or API)
3. Implement function to generate embeddings for text
4. Generate embeddings for saved queries (async)
5. Generate embeddings for reports (async)
6. Store embeddings in database

**Verification:**
- Embeddings are generated successfully
- Embeddings are stored in database
- Generation happens asynchronously (doesn't block saves)

#### Task 5.4: Implement Semantic Search

**Steps:**
1. Implement function to search saved queries by similarity
2. Generate embedding for search query
3. Use vector similarity search in PostgreSQL
4. Return similar queries sorted by similarity
5. Add semantic search to query discovery API

**Verification:**
- Can search queries by meaning
- Results are relevant to search query
- Search is fast (< 50ms for typical searches)
- Results are ranked by similarity

#### Task 5.5: Implement Context Retrieval

**Steps:**
1. Implement function to find similar historical reports
2. Use vector similarity to find related reports
3. Retrieve context from similar reports
4. Include context in narrative generation prompts
5. Update narrative generator to use retrieved context

**Verification:**
- Similar reports are found correctly
- Context is retrieved from historical reports
- Context improves narrative quality
- Narrative generation time remains acceptable

### MCP Server Integration (Optional Enhancement)

**When to Add:** After core features are stable, if natural language interface is needed.

#### Task 5.6: Set Up MCP Server Foundation

**Steps:**
1. Learn MCP protocol basics
2. Set up MCP server structure (separate service or integrated)
3. Implement basic MCP protocol handlers
4. Test MCP server starts correctly

**Verification:**
- MCP server can be started
- MCP protocol handlers work
- Server responds to MCP requests

#### Task 5.7: Implement Schema Discovery Tools

**Steps:**
1. Implement "get_table_schema" tool
2. Implement "list_available_tables" tool
3. Implement "get_table_sample" tool
4. Test tools return correct information
5. Expose tools via MCP protocol

**Verification:**
- Tools are available via MCP
- Tools return correct schema information
- Tools handle errors gracefully

#### Task 5.8: Integrate MCP with LLM

**Steps:**
1. Update LLM client to support MCP tools
2. Provide tool definitions to LLM
3. Enable LLM to call MCP tools during generation
4. Test LLM uses tools appropriately
5. Verify tool calls improve narrative quality

**Verification:**
- LLM can call MCP tools
- Tool calls improve context
- Narratives are more accurate with tool usage
- Performance impact is acceptable

**Testing Checklist for Phase 5 (Optional):**
- [ ] pgvector is set up and working
- [ ] Embeddings are generated correctly
- [ ] Semantic search finds relevant queries
- [ ] Context retrieval works
- [ ] MCP server works (if implemented)
- [ ] MCP tools are functional
- [ ] LLM integration with MCP works
- [ ] All features improve user experience

---

## 17. Learning Resources for Beginners

### PostgreSQL Topics to Study

**Before Starting Development:**

**1. Essential PostgreSQL Concepts (Priority 1):**
- Schema management: Multiple schemas, schema access
- Roles and permissions: CREATE ROLE, GRANT, REVOKE
- Data types: UUID, JSONB, TEXT[], TIMESTAMPTZ
- Basic indexes: B-tree indexes, when to use them

**2. SQL Features (Priority 1):**
- SELECT statements: Basic queries, filtering, sorting
- Aggregation: SUM, AVG, COUNT, GROUP BY, HAVING
- JOINs: INNER JOIN, LEFT JOIN
- Date functions: DATE_TRUNC, EXTRACT, INTERVAL arithmetic
- Common Table Expressions (CTEs): WITH clauses

**3. Advanced SQL (Priority 2):**
- Window functions: ROW_NUMBER, LAG, LEAD
- Time-series queries: Monthly/quarterly grouping
- Subqueries: Correlated and uncorrelated
- Conditional logic: CASE statements

**4. PostgreSQL-Specific (Priority 2):**
- JSON/JSONB operations: ->, ->>, @>, jsonb_build_object
- Array operations: ANY, ALL, array operators
- Index types: GIN indexes for arrays/JSONB
- Connection pooling: pgxpool basics

**5. Advanced Features (Priority 3 - Future):**
- Full-text search: to_tsvector, to_tsquery
- Functions and stored procedures
- Triggers and event notifications
- Extensions: pgvector, pg_trgm

**Recommended Learning Path:**

**Week 1 (Before Development):**
- Study: Schema management, roles, basic data types
- Practice: Create databases, schemas, tables locally
- Hands-on: Set up test database and create sample tables

**Week 2 (During Phase 1):**
- Study: SQL aggregation, GROUP BY, date functions
- Practice: Write queries similar to what PgQueryNarrative will execute
- Hands-on: Create seed data and test various query patterns

**Week 3 (During Phase 2):**
- Study: Window functions, CTEs, advanced queries
- Practice: Time-series queries, period comparisons
- Hands-on: Write queries that match real use cases

### Go Learning Resources

**Essential Go Concepts for PgQueryNarrative:**

**1. Go Basics (Priority 1):**
- Package structure and imports
- Functions, methods, and interfaces
- Error handling (error return values)
- Structs and type definitions
- Pointers and references

**2. Concurrency (Priority 2):**
- Goroutines basics
- Channels for communication
- Context package for cancellation
- sync package for synchronization

**3. Standard Library (Priority 1):**
- net/http: HTTP server and clients
- context: Request cancellation and timeouts
- encoding/json: JSON encoding and decoding
- database/sql and pgx: Database access
- os and os/exec: Environment variables, command execution

**4. Testing (Priority 1):**
- Writing tests: Test functions, test files
- testify package: Assertions and test helpers
- Table-driven tests
- Mocking interfaces

**Recommended Learning Path:**

**Week 1 (Before Development):**
- Study: Go basics, error handling, structs
- Practice: Write simple Go programs
- Hands-on: Create small HTTP server

**Week 2 (During Development):**
- Study: Database access with pgx, HTTP handlers
- Practice: Connect to PostgreSQL, execute queries
- Hands-on: Build simple query execution program

**Week 3 (During Development):**
- Study: Testing in Go, interfaces, dependency injection
- Practice: Write tests for database operations
- Hands-on: Create test suite for components

### Online Resources

**PostgreSQL Learning:**
- PostgreSQL Tutorial: www.postgresqltutorial.com (interactive lessons)
- PostgreSQL Documentation: www.postgresql.org/docs (official docs)
- pgAdmin tutorials: GUI-based learning
- YouTube: "PostgreSQL for Beginners" (video tutorials)

**Go Learning:**
- A Tour of Go: tour.golang.org (interactive tutorial)
- Go by Example: gobyexample.com (code examples)
- Effective Go: go.dev/doc/effective_go (best practices)
- Go Standard Library: pkg.go.dev/std (package documentation)

**Practice Platforms:**
- Create local PostgreSQL instance for practice
- Write sample queries using seed data
- Build small Go programs to test concepts
- Use Docker for isolated development environments

### Learning Schedule Recommendation

**Pre-Development (1-2 weeks):**
- Day 1-3: PostgreSQL basics (schemas, roles, data types)
- Day 4-6: SQL essentials (SELECT, aggregations, JOINs)
- Day 7-9: Go basics (syntax, error handling, HTTP)
- Day 10-12: Database access in Go (pgx basics)
- Day 13-14: Testing basics (Go testing, testify)

**During Development:**
- Learn advanced topics as needed for each phase
- Reference documentation frequently
- Practice with small examples before implementing
- Ask questions and seek help when stuck

---

## 18. Success Metrics

### Performance Targets

**Query Execution:**
- Target: < 500ms for queries returning < 1000 rows
- Measurement: Track query execution time in API responses
- Monitoring: Log slow queries (> 1 second) for optimization

**Narrative Generation:**
- Target: < 5 seconds for typical reports (LLM dependent)
- Measurement: Track LLM response time in report metadata
- Note: Time varies by LLM provider (Ollama slower, Groq faster)

**API Response Times:**
- Target: < 100ms for non-LLM operations
- Measurement: Track API endpoint response times
- Monitoring: Set up performance monitoring for all endpoints

**Database Operations:**
- Target: < 50ms for saved query operations
- Target: < 200ms for report retrieval
- Measurement: Track database query execution times

### Quality Targets

**Test Coverage:**
- Unit tests: 90%+ coverage for core logic
- Integration tests: 80%+ coverage for component interactions
- E2E tests: All critical user flows covered
- Measurement: Use `go test -cover` to measure coverage

**Narrative Quality:**
- Zero hallucinations: All claims cite source metrics
- Evidence-based: No invented facts or explanations
- Clear limitations: Acknowledges data constraints
- Appropriate confidence levels: Low/medium/high based on data quality

**Error Handling:**
- Clear error messages: Users understand what went wrong
- Graceful degradation: System continues operating when components fail
- Proper error logging: All errors logged for debugging
- User-friendly: Technical errors are translated to readable messages

### Usage Metrics (Future)

**Adoption:**
- Number of queries executed
- Number of reports generated
- Number of saved queries created
- Active users per day/week

**Performance:**
- Average query execution time
- Average narrative generation time
- Cache hit rate (if caching implemented)
- Error rate by type

**Quality:**
- User feedback on narrative quality
- Report export/download frequency
- Saved query reuse rate
- Error reports and resolutions

---

## 17. Success Metrics

- Query execution time < 500ms (for queries returning < 1000 rows)
- Narrative generation time < 5s (LLM dependent)
- Zero hallucinations (all claims cite source metrics)
- 95%+ test coverage for core logic
- API response times < 100ms (excluding LLM calls)

---

## 19. Future Enhancements

### Core Feature Enhancements

**Scheduled Report Generation:**
- Automatically generate reports on schedule (daily, weekly, monthly)
- Email reports to stakeholders
- Compare reports across periods automatically
- Alert on significant changes

**Report Templates:**
- Pre-defined report templates for common use cases
- Customizable templates for specific business needs
- Template library for different industries
- Template sharing between users

**Multi-language Narratives:**
- Generate narratives in multiple languages
- Automatic translation of narratives
- Language-specific formatting and terminology
- Localization support

**Collaborative Editing:**
- Multiple users can edit saved queries
- Version control for saved queries
- Comments and annotations on reports
- Sharing and permissions

**Advanced Visualizations:**
- Interactive charts and graphs
- Export charts as images
- Multiple chart types per report
- Dashboard view for multiple reports

### Technical Enhancements

**Custom Metric Definitions:**
- Users define custom metrics
- Reusable metric formulas
- Metric library and sharing
- Complex metric calculations

**API Rate Limiting:**
- Per-user rate limiting
- Tiered access levels
- Rate limit notifications
- Fair usage policies

**Authentication and Authorization:**
- User authentication (JWT, OAuth)
- Role-based access control (RBAC)
- Resource-level permissions
- Multi-tenant support

**Multi-tenant Support:**
- Organization-based data isolation
- Tenant-specific configurations
- Cross-tenant analytics
- Tenant management interface

### Advanced Features (RAG & AI)

**RAG & Vectorization (Phase 5):**
- Semantic query search using embeddings
- Historical context retrieval for narratives
- Schema knowledge base for domain understanding
- Semantic caching for faster responses
- Vector similarity search for query discovery

**MCP Server Integration (Phase 5):**
- Natural language to SQL query generation
- Intelligent query suggestions based on schema
- Schema-aware narrative generation
- Automated context retrieval
- Multi-database support via MCP

**Advanced AI Features:**
- Query optimization suggestions
- Anomaly detection in data
- Predictive insights
- Automated follow-up question generation
- Conversation-based query building

### Platform Enhancements

**API Improvements:**
- GraphQL API option
- WebSocket support for real-time updates
- API versioning strategy
- API rate limiting and quotas
- API analytics and monitoring

**Integration Capabilities:**
- Slack integration for report notifications
- Email integration for scheduled reports
- BI tool integrations (Tableau, Power BI connectors)
- Data warehouse integrations
- ETL pipeline integrations

**Data Sources:**
- Support for multiple database types (MySQL, SQL Server)
- CSV file upload and query
- Data warehouse connections (Snowflake, BigQuery)
- API data source connectors
- Real-time data streaming support

**Enterprise Features:**
- Audit logging and compliance reporting
- Data governance and lineage tracking
- Advanced security (encryption at rest, field-level encryption)
- High availability and disaster recovery
- Performance monitoring and alerting
- Cost optimization features

---
