# CityPulse — Public Data ETL Backend: Production Roadmap

## Project Overview

Build a backend system that periodically ingests public city/environmental data, processes it with PySpark, stores cleaned and aggregated results in PostgreSQL, and exposes results through a REST API.

---

## Phase 1: Foundation & Setup (Week 1-2)

### Milestone 1.1: Project Architecture & Infrastructure

- [ ] Define data sources (APIs to ingest from: weather, air quality, traffic, etc.)
- [ ] Design database schema (entities: cities, measurements, aggregations, metadata)
- [ ] Set up PostgreSQL database locally and on staging server
- [ ] Document architecture diagram (data flow: ingestion → processing → storage → API)
- [ ] Create `.env.example` with all required environment variables
- [ ] Set up Docker Compose for local development (PostgreSQL + PySpark container)

### Milestone 1.2: Development Environment Setup

- [ ] Initialize Python virtual environment (.venv)
- [ ] Set up project dependencies:
  - FastAPI for REST API
  - PySpark for data processing
  - psycopg2 or SQLAlchemy for PostgreSQL
  - APScheduler or Celery for scheduling
  - Requests for API calls
  - pytest for testing
  - python-dotenv for configuration
- [ ] Create requirements.txt and requirements-dev.txt
- [ ] Configure logging (structured logging with rotation)
- [ ] Set up git repository with .gitignore

### Milestone 1.3: Database Setup

- [ ] Create PostgreSQL tables:
  - `cities` (id, name, country, latitude, longitude, created_at)
  - `raw_data` (id, city_id, source, data_type, payload, ingested_at)
  - `processed_data` (id, city_id, data_type, metric_key, metric_value, processed_at)
  - `aggregations` (id, city_id, data_type, period, metric, value, aggregated_at)
- [ ] Create database indices for performance
- [ ] Set up connection pooling
- [ ] Create database migration scripts

---

## Phase 2: Core ETL Pipeline (Week 3-4)

### Milestone 2.1: Data Ingestion Service

- [ ] Build data ingestion module:
  - API client for fetching data from sources (e.g., OpenWeatherMap, AirVisual)
  - Error handling and retry logic (exponential backoff)
  - Rate limiting to respect API limits
  - Validate and normalize incoming data
- [ ] Store raw data in PostgreSQL `raw_data` table
- [ ] Implement logging for ingestion events
- [ ] Create unit tests for ingestion service (at least 80% coverage)

### Milestone 2.2: PySpark Data Processing

- [ ] Set up PySpark cluster (local or standalone mode)
- [ ] Build data transformation pipeline:
  - Read raw data from PostgreSQL
  - Data validation and cleaning
  - Handle missing/anomalous values
  - Data type conversions and standardization
- [ ] Write processed data to PostgreSQL `processed_data` table
- [ ] Implement data quality checks
- [ ] Create unit tests for transformations

### Milestone 2.3: Scheduled Data Aggregation

- [ ] Create aggregation logic:
  - Hourly aggregations (avg, min, max, count)
  - Daily aggregations
  - Weekly/monthly aggregations
  - City-level and regional aggregations
- [ ] Store aggregations in PostgreSQL `aggregations` table
- [ ] Implement APScheduler jobs for periodic execution:
  - Ingestion: every 15 minutes
  - Processing: every 30 minutes
  - Aggregation: hourly
- [ ] Add scheduler monitoring/heartbeat logging

---

## Phase 3: REST API Development (Week 5-6)

### Milestone 3.1: API Endpoints - Data Query

- [ ] GET `/api/v1/cities` - List all cities
- [ ] GET `/api/v1/cities/{city_id}` - Get city details
- [ ] GET `/api/v1/cities/{city_id}/latest` - Get latest measurements
- [ ] GET `/api/v1/cities/{city_id}/timeseries` - Get time-series data with filtering
  - Query parameters: `data_type`, `start_date`, `end_date`, `aggregation_period`
- [ ] GET `/api/v1/aggregations` - Get aggregated metrics with filters
- [ ] Implement pagination for large result sets
- [ ] Add query validation and error handling

### Milestone 3.2: API Endpoints - Administration

- [ ] POST `/api/v1/cities` - Add new city (admin only)
- [ ] PUT `/api/v1/cities/{city_id}` - Update city info (admin only)
- [ ] POST `/api/v1/ingest` - Trigger manual data ingestion (admin only)
- [ ] POST `/api/v1/reprocess` - Trigger reprocessing (admin only)
- [ ] GET `/api/v1/health` - Health check endpoint
- [ ] GET `/api/v1/metrics` - System metrics and pipeline status

### Milestone 3.3: API Security & Documentation

- [ ] Implement authentication (JWT or API keys)
- [ ] Add authorization for admin endpoints
- [ ] Set up rate limiting per endpoint
- [ ] Add CORS configuration
- [ ] Generate OpenAPI/Swagger documentation
- [ ] Create API response standardization (success/error formats)
- [ ] Implement request validation with Pydantic models

### Milestone 3.4: API Testing

- [ ] Write integration tests for all endpoints
- [ ] Test error scenarios and edge cases
- [ ] Load test critical endpoints (simulate 100+ concurrent requests)
- [ ] Test authentication/authorization flows
- [ ] Achieve 80%+ code coverage

---

## Phase 4: Data Quality & Monitoring (Week 7)

### Milestone 4.1: Data Quality Framework

- [ ] Implement data validation rules:
  - Schema validation
  - Value range validation
  - Null/missing data handling
  - Outlier detection
- [ ] Create data quality dashboards (metrics stored in DB)
- [ ] Set up alerts for data anomalies
- [ ] Create data lineage tracking

### Milestone 4.2: Monitoring & Observability

- [ ] Implement structured logging (JSON format)
- [ ] Set up centralized log collection (e.g., ELK stack or CloudWatch)
- [ ] Create application metrics (Prometheus format):
  - Pipeline success/failure rates
  - Data ingestion latency
  - API response times
  - Database connection pool health
- [ ] Set up monitoring dashboard (Grafana or similar)
- [ ] Define alerting rules (pipeline failures, data staleness, API errors)

### Milestone 4.3: Error Handling & Recovery

- [ ] Implement comprehensive exception handling
- [ ] Create automatic retry mechanisms with backoff
- [ ] Add dead-letter queue for failed records
- [ ] Implement data reconciliation jobs
- [ ] Create incident response procedures

---

## Phase 5: Performance Optimization (Week 8)

### Milestone 5.1: Database Optimization

- [ ] Analyze query performance with EXPLAIN ANALYZE
- [ ] Add appropriate indices on frequently queried columns
- [ ] Implement query result caching (Redis)
- [ ] Optimize aggregation queries
- [ ] Set up connection pooling (HikariCP or similar)

### Milestone 5.2: PySpark Optimization

- [ ] Optimize partition strategy for data processing
- [ ] Implement caching for frequently-used RDDs/DataFrames
- [ ] Tune JVM memory settings
- [ ] Profile code for bottlenecks
- [ ] Optimize I/O operations

### Milestone 5.3: API Optimization

- [ ] Implement response caching (HTTP caching headers)
- [ ] Compress API responses (gzip)
- [ ] Optimize database queries (n+1 problem resolution)
- [ ] Load test and establish SLAs

---

## Phase 6: Testing & Quality Assurance (Week 9)

### Milestone 6.1: Comprehensive Testing

- [ ] Unit tests: 80%+ coverage for core modules
- [ ] Integration tests: End-to-end pipeline testing
- [ ] API tests: All endpoints and error scenarios
- [ ] Database tests: Migration and schema validation
- [ ] Performance tests: Benchmark critical operations

### Milestone 6.2: Code Quality

- [ ] Set up linting (pylint, flake8)
- [ ] Configure code formatting (Black)
- [ ] Set up type checking (mypy)
- [ ] Implement CI/CD pipeline (GitHub Actions/GitLab CI)
- [ ] Enforce pre-commit hooks

### Milestone 6.3: Security Testing

- [ ] Dependency vulnerability scanning (safety, bandit)
- [ ] Code security review
- [ ] SQL injection/XSS prevention validation
- [ ] Authentication/authorization testing
- [ ] Rate limiting and DDoS protection testing

---

## Phase 7: Deployment & Infrastructure (Week 10-11)

### Milestone 7.1: Containerization

- [ ] Create Dockerfile for main application
- [ ] Create Dockerfile for PySpark worker
- [ ] Set up Docker Compose for multi-container orchestration
- [ ] Optimize container images (minimize size, security best practices)

### Milestone 7.2: Kubernetes Configuration (if scaling to production)

- [ ] Create Kubernetes manifests:
  - Deployment for FastAPI app
  - StatefulSet or separate deployment for PySpark
  - Service for internal communication
  - ConfigMap for configuration
  - Secret for sensitive data
- [ ] Set up Helm charts for easy deployment
- [ ] Configure resource limits and requests
- [ ] Set up horizontal pod autoscaling

### Milestone 7.3: Infrastructure as Code

- [ ] Define infrastructure with Terraform/CloudFormation:
  - PostgreSQL RDS instance
  - Application servers/containers
  - Load balancer
  - VPC and security groups
  - S3/storage for backups
- [ ] Create staging and production environments
- [ ] Set up backup and disaster recovery

### Milestone 7.4: CI/CD Pipeline

- [ ] Set up automated testing on commits
- [ ] Configure Docker image building and registry push
- [ ] Set up automated deployment to staging
- [ ] Manual approval gates for production deployment
- [ ] Rollback procedures

---

## Phase 8: Production Readiness (Week 12)

### Milestone 8.1: Documentation

- [ ] API documentation (Swagger/OpenAPI)
- [ ] Architecture documentation
- [ ] Deployment guide
- [ ] Operational runbooks
- [ ] Troubleshooting guide
- [ ] Data dictionary and schema documentation

### Milestone 8.2: Pre-Production Checklist

- [ ] [ ] All tests passing (100% on critical paths)
- [ ] [ ] All security vulnerabilities resolved
- [ ] [ ] Load testing completed and SLAs defined
- [ ] [ ] Monitoring and alerting configured
- [ ] [ ] Backup and recovery procedures tested
- [ ] [ ] Data retention policies defined
- [ ] [ ] Capacity planning completed
- [ ] [ ] Incident response plan documented
- [ ] [ ] All dependencies documented and pinned

### Milestone 8.3: Blue-Green Deployment Setup

- [ ] Set up parallel production environments (blue and green)
- [ ] Implement traffic switching mechanism
- [ ] Create rollback procedures
- [ ] Document deployment process

### Milestone 8.4: Launch Preparation

- [ ] Pilot testing with limited data
- [ ] Gradual rollout strategy
- [ ] On-call rotation setup for monitoring
- [ ] Communication plan for stakeholders

---

## Phase 9: Production Launch & Post-Launch (Week 13+)

### Milestone 9.1: Production Launch

- [ ] Final production environment setup
- [ ] Data seeding (initial city data and configurations)
- [ ] Launch monitoring intensification
- [ ] Go/No-Go decision meeting

### Milestone 9.2: Post-Launch (First 2 weeks)

- [ ] Monitor all metrics closely
- [ ] Respond to any issues immediately
- [ ] Collect performance data
- [ ] Validate SLAs are being met
- [ ] Gather user feedback

### Milestone 9.3: Ongoing Operations

- [ ] Weekly performance reviews
- [ ] Monthly security audits
- [ ] Quarterly capacity planning
- [ ] Regular documentation updates
- [ ] Continuous optimization based on usage patterns

---

## Key Technologies & Stack

**Backend Framework:** FastAPI
**Data Processing:** PySpark
**Database:** PostgreSQL
**Task Scheduling:** APScheduler or Celery
**Caching:** Redis (optional but recommended)
**Containerization:** Docker & Docker Compose
**Orchestration:** Kubernetes (for scaling)
**Infrastructure:** Terraform/CloudFormation
**CI/CD:** GitHub Actions or GitLab CI
**Monitoring:** Prometheus + Grafana
**Logging:** ELK Stack or CloudWatch
**Testing:** pytest, locust (load testing)

---

## Success Criteria

- **Uptime:** 99.5% or higher
- **API Response Time:** <500ms (p95) for read operations
- **Data Freshness:** Latest data available within 1 hour of ingestion
- **Data Quality:** 99%+ data validation pass rate
- **Test Coverage:** 80%+ for all code
- **Security:** Zero critical vulnerabilities, annual penetration testing
- **Scalability:** Support 1000+ concurrent API requests

---

## Risk Mitigation

| Risk                     | Mitigation                                                 |
| ------------------------ | ---------------------------------------------------------- |
| Data source API downtime | Implement fallback data sources, caching, circuit breakers |
| Database performance     | Query optimization, caching, read replicas                 |
| PySpark job failures     | Checkpointing, retry logic, monitoring                     |
| API latency spikes       | Load testing, auto-scaling, rate limiting                  |
| Data quality issues      | Validation rules, automated alerts, manual reviews         |
| Security breaches        | Authentication, encryption, regular audits                 |

---

## Timeline Summary

- **Phase 1:** Week 1-2 (Foundation)
- **Phase 2:** Week 3-4 (ETL Pipeline)
- **Phase 3:** Week 5-6 (REST API)
- **Phase 4:** Week 7 (Quality & Monitoring)
- **Phase 5:** Week 8 (Performance)
- **Phase 6:** Week 9 (Testing & QA)
- **Phase 7:** Week 10-11 (Deployment)
- **Phase 8:** Week 12 (Production Readiness)
- **Phase 9:** Week 13+ (Launch & Operations)

**Total Estimated Timeline:** 12-13 weeks

---

## Next Steps

1. Review and customize this roadmap based on your specific requirements
2. Identify your data sources and validate API availability
3. Set up your development environment following Phase 1
4. Start Phase 1 implementation and track progress

Good luck with CityPulse! 🚀
