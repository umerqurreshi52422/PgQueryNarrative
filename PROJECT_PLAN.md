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
│  └── Report Generator                │
└──────┬───────────────────┬───────────┘
       │                   │
       ▼                   ▼
┌─────────────┐    ┌──────────────┐
│ PostgreSQL  │    │ Free LLM     │
│  Database   │    │ (Ollama/     │
│             │    │  Groq/Gemini)│
└─────────────┘    └──────────────┘
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

### Unit Tests

- Query validation
- Metrics calculation
- Result profiling
- LLM response parsing
- Storage operations

### Integration Tests

- Database operations
- LLM client integration
- API endpoint integration
- Report generation workflow

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

**E2E Test Implementation**:

```go
// test/e2e/e2e_test.go
package e2e

import (
    "context"
    "testing"
    "github.com/stretchr/testify/require"
)

func TestE2E_QueryToReport(t *testing.T) {
    ctx := context.Background()
    
    // Setup test environment
    client := setupTestClient(t)
    db := setupTestDB(t)
    defer cleanup(t, db)
    
    // Execute query
    queryResp, err := client.RunQuery(ctx, &RunQueryRequest{
        SQL: "SELECT * FROM demo.sales LIMIT 10",
        Limit: 100,
    })
    require.NoError(t, err)
    require.NotNil(t, queryResp)
    require.Greater(t, len(queryResp.Rows), 0)
    
    // Generate report
    reportResp, err := client.GenerateReport(ctx, &GenerateReportRequest{
        SQL: "SELECT * FROM demo.sales LIMIT 10",
        Context: ReportContext{
            Audience: "executive",
            Tone: "concise",
        },
    })
    require.NoError(t, err)
    require.NotNil(t, reportResp)
    require.NotEmpty(t, reportResp.Narrative.Headline)
    require.Greater(t, len(reportResp.Narrative.KeyTakeaways), 0)
    
    // Verify report stored
    storedReport, err := client.GetReport(ctx, reportResp.ID)
    require.NoError(t, err)
    require.Equal(t, reportResp.ID, storedReport.ID)
}
```

**E2E Test Infrastructure**:

- **Test Containers**: Use testcontainers-go for PostgreSQL
- **Mock LLM**: Use in-memory mock for LLM responses
- **Test Data**: Seed scripts with consistent test data
- **Test Helpers**: Reusable setup/teardown utilities

**E2E Test Execution**:

- `make test-e2e` - Run all E2E tests
- `make test-e2e-slow` - Include slow LLM integration tests
- `make test-e2e-docker` - Run with Docker containers

### Test Data

- Seed scripts with realistic demo data
- Fixtures for various query patterns
- Edge cases (empty results, single row, etc.)
- Performance test datasets (large result sets)

### Test Coverage Goals

- Unit tests: 90%+ coverage
- Integration tests: 80%+ coverage
- E2E tests: All critical user flows
- Code coverage reports: `make test-coverage`

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

## 16. MVP Implementation Phases

### Phase 1: Foundation (Week 1)

- Project structure setup
- Goa API design
- Database schema and migrations
- Basic query execution

### Phase 2: Core Features (Week 2)

- Result profiling
- Metrics calculation
- Ollama LLM integration
- Basic narrative generation

### Phase 3: UI & Polish (Week 3)

- Templ templates
- HTMX interactions
- Saved queries management
- Report display and export

### Phase 4: Enhancements (Week 4)

- Time-series analysis
- Chart suggestions
- Additional LLM providers
- Error handling improvements

---

## 17. Success Metrics

- Query execution time < 500ms (for queries returning < 1000 rows)
- Narrative generation time < 5s (LLM dependent)
- Zero hallucinations (all claims cite source metrics)
- 95%+ test coverage for core logic
- API response times < 100ms (excluding LLM calls)

---

## 18. Future Enhancements

- Scheduled report generation
- Report templates
- Multi-language narratives
- Collaborative editing
- Version control for saved queries
- Advanced visualizations
- Custom metric definitions
- API rate limiting
- Authentication and authorization
- Multi-tenant support