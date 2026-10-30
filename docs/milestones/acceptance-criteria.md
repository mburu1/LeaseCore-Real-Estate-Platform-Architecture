# Acceptance Criteria

## Overview

This document defines the acceptance criteria for each milestone in the LeaseCore implementation roadmap.

## Phase 1: Foundation

### Milestone 1.1: Project Setup

#### Functional Requirements

- [ ] Solution builds without errors
- [ ] All projects reference correct NuGet package versions
- [ ] Building blocks projects created (Domain, Application, Infrastructure)
- [ ] Module projects created (Identity, Portfolio, Leases, Documents, etc.)
- [ ] Test projects created (Unit, Integration, Architecture, Contract)
- [ ] .editorconfig configured with coding standards
- [ ] Directory.Build.props configured for shared MSBuild properties
- [ ] Directory.Packages.props configured for shared NuGet packages

#### Non-Functional Requirements

- [ ] Build time < 2 minutes
- [ ] Solution compiles on Windows, Linux, and macOS
- [ ] Code style rules enforced via Roslyn analyzers
- [ ] Git repository initialized with proper .gitignore
- [ ] Git workflow documented in CONTRIBUTING.md

#### Acceptance Tests

- [ ] Solution builds successfully on clean machine
- [ ] All projects reference correct .NET version
- [ ] Code style violations cause build failure
- [ ] .gitignore covers all generated files

---

### Milestone 1.2: Infrastructure Setup

#### Functional Requirements

- [ ] Azure resource group created
- [ ] Azure SQL Database provisioned with required sizing
- [ ] Cosmos DB account provisioned with correct configuration
- [ ] Blob Storage account provisioned with lifecycle policies
- [ ] Key Vault provisioned with access policies
- [ ] Application Insights provisioned
- [ ] Infrastructure as code (Bicep or Terraform) templates created
- [ ] Managed identities configured for Azure resources
- [ ] Network security groups configured
- [ ] Private endpoints configured for data services

#### Non-Functional Requirements

- [ ] Infrastructure deployment < 30 minutes
- [ ] Infrastructure reproducible via IaC
- [ ] All secrets stored in Key Vault
- [ ] No hardcoded connection strings
- [ ] Infrastructure documented

#### Acceptance Tests

- [ ] Infrastructure deploys successfully from IaC
- [ ] All Azure resources accessible via managed identities
- [ ] Key Vault secrets accessible by application
- [ ] Network security rules enforced
- [ ] Infrastructure deployment idempotent

---

### Milestone 1.3: CI/CD Pipeline

#### Functional Requirements

- [ ] GitHub Actions workflow for CI configured
- [ ] GitHub Actions workflow for CD configured
- [ ] Automated unit tests run on every push
- [ ] Automated integration tests run on every push
- [ ] Automated deployment to staging on main branch
- [ ] Automated security scans (SAST, dependency)
- [ ] Infrastructure validation in pipeline
- [ ] Rollback procedure documented and tested

#### Non-Functional Requirements

- [ ] CI pipeline completes < 10 minutes
- [ ] CD pipeline completes < 15 minutes
- [ ] Pipeline failures notify team via Slack
- [ ] Pipeline status visible on GitHub
- [ ] Pipeline documented

#### Acceptance Tests

- [ ] CI pipeline triggers on pull request
- [ ] All tests pass in CI
- [ ] CD pipeline deploys to staging
- [ ] Rollback procedure works correctly
- [ ] Security scans run successfully

---

### Milestone 1.4: Authentication & Authorization

#### Functional Requirements

- [ ] Entra ID integration configured
- [ ] JWT token validation middleware implemented
- [ ] Role-based access control implemented
- [ ] Permission-based authorization implemented
- [ ] Tenant isolation enforced at all layers
- [ ] User management API endpoints functional
- [ ] Role management API endpoints functional
- [ ] Permission management API endpoints functional

#### Non-Functional Requirements

- [ ] Authentication < 100ms
- [ ] Authorization < 50ms
- [ ] JWT token lifetime 15 minutes
- [ ] Refresh token lifetime 7 days
- [ ] Token revocation on logout

#### Acceptance Tests

- [ ] Users can authenticate via Entra ID
- [ ] Invalid tokens rejected
- [ ] Expired tokens rejected
- [ ] Roles enforced correctly
- [ ] Permissions enforced correctly
- [ ] Tenant isolation verified (no cross-tenant data access)
- [ ] Authorization tests passing

---

## Phase 2: Core Features

### Milestone 2.1: Portfolio Management

#### Functional Requirements

- [ ] Portfolio CRUD operations (Create, Read, Update, Delete)
- [ ] Property CRUD operations
- [ ] Building CRUD operations
- [ ] Unit CRUD operations
- [ ] Ownership interest management
- [ ] Portfolio hierarchy validation
- [ ] REST API endpoints for all operations
- [ ] GraphQL schema and resolvers

#### Non-Functional Requirements

- [ ] API response time < 200ms (p95)
- [ ] Data validation enforced
- [ ] Concurrency handling (optimistic locking)
- [ ] Audit logging for all changes

#### Acceptance Tests

- [ ] Portfolio hierarchy correctly modeled
- [ ] CRUD operations work correctly
- [ ] Validation errors returned correctly
- [ ] Concurrency conflicts handled
- [ ] REST API functional
- [ ] GraphQL queries functional
- [ ] Unit tests passing (> 80% coverage)
- [ ] Integration tests passing

---

### Milestone 2.2: Lease Management

#### Functional Requirements

- [ ] Lease CRUD operations
- [ ] Lease party management
- [ ] Lease term management
- [ ] Lease amendment workflow
- [ ] Rent schedule management
- [ ] Rent review management
- [ ] Lease options management
- [ ] Lease lifecycle state machine
- [ ] REST API endpoints
- [ ] GraphQL schema and resolvers

#### Non-Functional Requirements

- [ ] API response time < 300ms (p95)
- [ ] State transitions validated
- [ ] Amendments preserve history
- [ ] Rent schedules calculated correctly
- [ ] Audit logging for all changes

#### Acceptance Tests

- [ ] Lease lifecycle correctly implemented
- [ ] All state transitions validated
- [ ] Invalid transitions rejected
- [ ] Amendments preserve original terms
- [ ] Rent schedules calculated correctly
- [ ] REST API functional
- [ ] GraphQL queries functional
- [ ] Domain tests passing
- [ ] Integration tests passing

---

### Milestone 2.3: Document Management

#### Functional Requirements

- [ ] Document metadata management
- [ ] Document versioning
- [ ] Document upload workflow (initiate, upload, confirm)
- [ ] Document download workflow
- [ ] Document access grants
- [ ] Blob Storage integration
- [ ] File type validation
- [ ] File size validation
- [ ] REST API endpoints
- [ ] GraphQL schema and resolvers

#### Non-Functional Requirements

- [ ] Upload < 5 seconds for 10MB file
- [ ] Download < 2 seconds for 10MB file
- [ ] Document versions immutable
- [ ] Access grants enforced
- [ ] SAS URLs expire after 15 minutes

#### Acceptance Tests

- [ ] Documents upload and download correctly
- [ ] Document versions immutable
- [ ] Access grants enforced
- [ ] File validation working (type, size, magic bytes)
- [ ] Virus scanning integrated
- [ ] SAS URLs expire correctly
- [ ] REST API functional
- [ ] GraphQL queries functional
- [ ] Integration tests passing

---

## Phase 3: Advanced Features

### Milestone 3.1: Obligations & Key Dates

#### Functional Requirements

- [ ] Key date management
- [ ] Key dates derived from lease terms
- [ ] Obligation management
- [ ] Reminder system
- [ ] Notification delivery
- [ ] Email integration
- [ ] REST API endpoints

#### Non-Functional Requirements

- [ ] Reminders triggered accurately
- [ ] Notifications delivered within 5 minutes
- [ ] Email integration reliable
- [ ] Background processing reliable

#### Acceptance Tests

- [ ] Key dates derived correctly from lease terms
- [ ] Reminders triggered at correct time
- [ ] Notifications delivered
- [ ] Email integration working
- [ ] Background processing reliable
- [ ] REST API functional
- [ ] Unit tests passing
- [ ] Integration tests passing

---

### Milestone 3.2: Task Management

#### Functional Requirements

- [ ] Task CRUD operations
- [ ] Task assignment
- [ ] Task status workflow
- [ ] Task escalation
- [ ] Priority management
- [ ] Due date tracking
- [ ] REST API endpoints
- [ ] GraphQL schema and resolvers

#### Non-Functional Requirements

- [ ] API response time < 200ms (p95)
- [ ] Escalation logic working
- [ ] Notifications on overdue tasks

#### Acceptance Tests

- [ ] Tasks created and assigned correctly
- [ ] Status transitions validated
- [ ] Escalation logic working
- [ ] Overdue tasks identified
- [ ] REST API functional
- [ ] GraphQL queries functional
- [ ] Unit tests passing
- [ ] Integration tests passing

---

### Milestone 3.3: Lease Accounting

#### Functional Requirements

- [ ] Payment schedule management
- [ ] Discount rate assumptions
- [ ] Lease measurement calculations
- [ ] Right-of-use asset calculations
- [ ] Lease liability calculations
- [ ] Journal entry generation
- [ ] Journal balancing validation
- [ ] Accounting period management
- [ ] IFRS 16 reporting
- [ ] REST API endpoints

#### Non-Functional Requirements

- [ ] Calculations accurate to 2 decimal places
- [ ] Journal entries always balance
- [ ] Calculations < 1 second
- [ ] Audit trail for all accounting entries

#### Acceptance Tests

- [ ] Payment schedules generated correctly
- [ ] Measurements calculated accurately
- [ ] ROU asset values correct
- [ ] Lease liability values correct
- [ ] Journal entries balance (debits = credits)
- [ ] IFRS 16 reports generated correctly
- [ ] Calculations idempotent
- [ ] REST API functional
- [ ] Unit tests passing
- [ ] Integration tests passing

---

## Phase 4: Integration & Polish

### Milestone 4.1: Search & Reporting

#### Functional Requirements

- [ ] Azure AI Search integration
- [ ] Search projections
- [ ] Full-text search
- [ ] Lease expiration report
- [ ] Rent roll report
- [ ] Portfolio summary report
- [ ] Report export (PDF, Excel)
- [ ] REST API endpoints

#### Non-Functional Requirements

- [ ] Search response time < 500ms
- [ ] Search relevance > 80%
- [ ] Reports generated < 5 seconds
- [ ] Export time < 10 seconds

#### Acceptance Tests

- [ ] Search returns relevant results
- [ ] Search performance acceptable
- [ ] Reports generated correctly
- [ ] Reports exportable
- [ ] Search indexing reliable
- [ ] REST API functional
- [ ] Integration tests passing

---

### Milestone 4.2: GraphQL API

#### Functional Requirements

- [ ] Complete GraphQL schema
- [ ] Query resolvers for all entities
- [ ] Mutation resolvers for all operations
- [ ] DataLoaders for N+1 prevention
- [ ] Query complexity limits
- [ ] Query depth limits
- [ ] Authorization directives
- [ ] Scalar integration

#### Non-Functional Requirements

- [ ] Query response time < 500ms (p95)
- [ ] No N+1 queries
- [ ] Complexity limits enforced
- [ ] Depth limits enforced
- [ ] Authorization enforced

#### Acceptance Tests

- [ ] GraphQL schema complete
- [ ] All queries functional
- [ ] All mutations functional
- [ ] N+1 queries prevented
- [ ] Complexity limits enforced
- [ ] Depth limits enforced
- [ ] Authorization enforced
- [ ] Contract tests passing

---

### Milestone 4.3: Frontend Development

#### Functional Requirements

- [ ] React application setup
- [ ] Authentication integration
- [ ] Portfolio management UI
- [ ] Lease management UI
- [ ] Document management UI
- [ ] Task management UI
- [ ] Accounting UI
- [ ] Reporting UI
- [ ] Responsive design
- [ ] Error handling

#### Non-Functional Requirements

- [ ] Frontend build time < 2 minutes
- [ ] Initial load time < 3 seconds
- [ ] Page transitions < 500ms
- [ ] Responsive on mobile, tablet, desktop
- [ ] Accessibility (WCAG 2.1 AA)

#### Acceptance Tests

- [ ] Frontend builds successfully
- [ ] Authentication flow working
- [ ] All CRUD operations functional in UI
- [ ] Responsive design verified
- [ ] E2E tests passing
- [ ] Accessibility verified (Lighthouse score > 90)

---

### Milestone 4.4: Performance Optimization

#### Functional Requirements

- [ ] Database query optimization
- [ ] Caching strategy implemented
- [ ] API response time optimized
- [ ] GraphQL query optimization
- [ ] Frontend performance optimization
- [ ] Load testing completed

#### Non-Functional Requirements

- [ ] API response time < 500ms (p95)
- [ ] Database queries < 100ms (p95)
- [ ] Cache hit rate > 80%
- [ ] GraphQL queries < 500ms (p95)
- [ ] Frontend load time < 3s
- [ ] System handles 1000 concurrent users

#### Acceptance Tests

- [ ] API response time meets target
- [ ] Database queries optimized
- [ ] Caching effective
- [ ] GraphQL queries efficient
- [ ] Frontend performance meets target
- [ ] Load test passes target metrics

---

## Phase 5: Production

### Milestone 5.1: Security Hardening

#### Functional Requirements

- [ ] Security audit completed
- [ ] Vulnerability remediation
- [ ] Penetration testing
- [ ] Security controls validated
- [ ] Compliance verification (GDPR, SOC 2)
- [ ] Documentation review

#### Non-Functional Requirements

- [ ] 0 critical vulnerabilities
- [ ] < 5 high vulnerabilities
- [ ] Penetration test passed
- [ ] Compliance verified

#### Acceptance Tests

- [ ] Security audit completed
- [ ] Critical vulnerabilities remediated
- [ ] Penetration test passed
- [ ] Security controls validated
- [ ] Compliance verified
- [ ] Security documentation complete

---

### Milestone 5.2: Load Testing

#### Functional Requirements

- [ ] Load test scenarios defined
- [ ] Performance baseline established
- [ ] Stress testing completed
- [ ] Capacity planning completed
- [ ] Scalability validated

#### Non-Functional Requirements

- [ ] System handles 1000 concurrent users
- [ ] System handles 10,000 requests per minute
- [ ] Response time < 500ms under load
- [ ] Error rate < 1% under load

#### Acceptance Tests

- [ ] Load test scenarios defined
- [ ] Performance baseline established
- [ ] System handles target load
- [ ] Bottlenecks identified and addressed
- [ ] Scalability validated

---

### Milestone 5.3: Production Deployment

#### Functional Requirements

- [ ] Production environment setup
- [ ] Database migration to production
- [ ] Production deployment
- [ ] Smoke tests
- [ ] Monitoring and alerting setup
- [ ] Documentation handover
- [ ] Training completed

#### Non-Functional Requirements

- [ ] Deployment time < 30 minutes
- [ ] Zero downtime deployment
- [ ] Rollback time < 10 minutes
- [ ] System availability 99.9%

#### Acceptance Tests

- [ ] Production environment configured
- [ ] Data migrated successfully
- [ ] Application deployed
- [ ] Smoke tests passing
- [ ] Monitoring and alerting active
- [ ] Documentation complete
- [ ] Training completed

---

## General Acceptance Criteria

### Code Quality

- [ ] Code coverage > 80%
- [ ] No critical SonarQube issues
- [ ] < 10 major SonarQube issues
- [ ] Code review approved by at least 2 reviewers
- [ ] No TODO comments in production code

### Documentation

- [ ] API documentation complete (Scalar)
- [ ] GraphQL schema documented
- [ ] Architecture documentation up to date
- [ ] Runbook complete
- [ ] Incident response plan complete
- [ ] Deployment guide complete

### Testing

- [ ] Unit tests passing
- [ ] Integration tests passing
- [ ] Contract tests passing
- [ ] E2E tests passing
- [ ] Performance tests passing
- [ ] Security tests passing

### Monitoring

- [ ] Application Insights configured
- [ ] Health endpoints functional
- [ ] Alert rules configured
- [ ] Dashboards configured
- [ ] Log aggregation working

### Security

- [ ] All secrets in Key Vault
- [ ] TLS 1.3 enabled
- [ ] Security headers configured
- [ ] Input validation enforced
- [ ] SQL injection prevention verified
- [ ] XSS prevention verified

## Sign-off

Each milestone requires sign-off from:

- [ ] Product Owner
- [ ] Tech Lead
- [ ] QA Lead
- [ ] Security Lead (for security-related milestones)

Sign-off template:

```
Milestone: [Milestone Name]
Sign-off Date: [Date]
Approved By:
- Product Owner: [Name, Signature]
- Tech Lead: [Name, Signature]
- QA Lead: [Name, Signature]
- Security Lead: [Name, Signature]

Notes:
[Any notes or concerns]
```
