# Security Controls

## Overview

This document details the security controls implemented in the LeaseCore platform to protect against identified threats.

## Authentication Controls

### Multi-Factor Authentication (MFA)

```mermaid
flowchart LR
    User[User] --> Login[Login Request]
    Login --> Creds[Validate Credentials]
    Creds --> MFA[MFA Challenge]
    MFA --> SMS[SMS Code]
    MFA --> App[Authenticator App]
    SMS --> Verify[Verify Code]
    App --> Verify
    Verify --> Success[Issue JWT]
    Verify --> Fail[Deny Access]
```

**Implementation:**
- Entra ID MFA required for all users
- Conditional access policies based on location, device, risk
- Backup authentication methods (phone, email)

### JWT Token Management

```mermaid
flowchart TB
    User[User] --> Login[Login]
    Login --> Auth[Auth Service]
    Auth --> Entra[Entra ID]
    Entra --> Token[Issue JWT]
    Token --> Client[Client]
    Client --> API[API Request<br/>JWT in Header]
    API --> Validate[Validate JWT]
    Validate --> CheckExpiry[Check Expiry]
    CheckExpiry --> Valid[Valid Token]
    CheckExpiry --> Expired[Expired Token]
    Valid --> Refresh[Check Refresh Token]
    Refresh --> IssueNew[Issue New JWT]
    IssueNew --> Client
    Expired --> Reauth[Re-authenticate]
```

**Token Configuration:**
- Access token lifetime: 15 minutes
- Refresh token lifetime: 7 days
- Refresh token rotation on use
- Token revocation on logout

## Authorization Controls

### Role-Based Access Control (RBAC)

```mermaid
flowchart TB
    User[User] --> Request[Access Request]
    Request --> AuthN[Authentication]
    AuthN --> AuthZ[Authorization]
    AuthZ --> GetRoles[Get User Roles]
    GetRoles --> GetPerms[Get Role Permissions]
    GetPerms --> Check[Check Permission]
    Check --> Allowed[Access Allowed]
    Check --> Denied[Access Denied]
```

**Implementation:**
- Roles defined per tenant
- Permissions assigned to roles
- Users assigned to roles within organizations
- Resource-level authorization where applicable

### Tenant Isolation

```mermaid
flowchart TB
    Request[Request] --> JWT[Extract JWT]
    JWT --> TenantID[Get Tenant ID from JWT]
    TenantID --> Query[Add Tenant Filter to Query]
    Query --> DB[Database Query]
    DB --> Results[Results Scoped to Tenant]
    Results --> Response[Response]
```

**Implementation:**
- Tenant ID derived from JWT, not request
- Global query filters in EF Core
- Row-level security in SQL Server
- Partition key in Cosmos DB

## Data Protection Controls

### Encryption at Rest

| Data Store | Encryption Method | Key Management |
|------------|------------------|----------------|
| SQL Server | Transparent Data Encryption (TDE) | Azure Key Vault |
| Cosmos DB | Service-managed encryption | Azure Key Vault |
| Blob Storage | Microsoft-managed encryption | Azure Key Vault |
| Disk | Azure Disk Encryption | Azure Key Vault |

### Encryption in Transit

- TLS 1.3 for all external communications
- TLS 1.2 minimum for internal communications
- Certificate pinning for external services
- Regular certificate rotation

### Data Masking

- Mask sensitive data in logs (PII, financial data)
- Mask sensitive data in error messages
- Dynamic data masking in SQL Server for privileged users

## API Security Controls

### Rate Limiting

```mermaid
flowchart TB
    Request[API Request] --> Identify[Identify Tenant/User]
    Identify --> CheckLimit[Check Rate Limit]
    CheckLimit --> WithinLimit[Within Limit]
    CheckLimit --> Exceeded[Limit Exceeded]
    WithinLimit --> Process[Process Request]
    Exceeded --> Reject[Reject with 429]
    Reject --> Headers[Return Rate Limit Headers]
    Process --> Update[Update Rate Limit Counter]
```

**Configuration:**
- 1000 requests per minute per tenant
- 100 requests per second burst
- Sliding window algorithm
- Redis-backed distributed counter

### GraphQL Security

```mermaid
flowchart TB
    Query[GraphQL Query] --> Parse[Parse Query]
    Parse --> Validate[Validate Schema]
    Validate --> Analyze[Analyze Complexity]
    Analyze --> CheckDepth[Check Depth Limit]
    CheckDepth --> CheckComplexity[Check Complexity Limit]
    CheckComplexity --> CheckFields[Check Field Count]
    CheckFields --> Execute[Execute Query]
    CheckDepth --> Reject[Reject]
    CheckComplexity --> Reject
    CheckFields --> Reject
```

**Limits:**
- Maximum query depth: 10
- Maximum complexity: 1000
- Maximum field count: 100
- Query timeout: 30 seconds

### Input Validation

```mermaid
flowchart TB
    Input[User Input] --> Schema[Schema Validation]
    Schema --> Type[Type Checking]
    Type --> Format[Format Validation]
    Format --> Range[Range Validation]
    Range --> Business[Business Rules]
    Business --> Sanitize[Sanitize Input]
    Sanitize --> Accept[Accept]
    Schema --> Reject[Reject]
    Type --> Reject
    Format --> Reject
    Range --> Reject
    Business --> Reject
```

**Implementation:**
- FluentValidation for all DTOs
- Regular expression for format validation
- Range checks for numeric values
- Custom business rule validators

## Document Security Controls

### File Upload Validation

```mermaid
flowchart TB
    Upload[File Upload] --> Size[Check File Size]
    Size --> Type[Check File Type]
    Type --> Magic[Magic Byte Verification]
    Magic --> Scan[Virus Scan]
    Scan --> Store[Store in Blob Storage]
    Size --> Reject[Reject]
    Type --> Reject
    Magic --> Reject
    Scan --> Reject
```

**Validation Rules:**
- Maximum file size: 50 MB
- Allowed MIME types: PDF, DOCX, XLSX, JPG, PNG
- Magic byte verification
- Virus scanning with Microsoft Defender

### Document Access Control

```mermaid
flowchart TB
    Request[Document Access] --> Auth[Authenticate]
    Auth --> CheckAccess[Check Access Grant]
    CheckAccess --> HasGrant[Has Access Grant]
    HasGrant --> Generate[Generate Signed URL]
    Generate --> Response[Return URL]
    CheckAccess --> NoGrant[No Access Grant]
    NoGrant --> Deny[Deny Access]
```

**Implementation:**
- Document access grants stored in database
- Signed URLs with SAS tokens
- URL expiration: 15 minutes
- Access logging

## Infrastructure Security Controls

### Key Management

```mermaid
flowchart TB
    App[Application] --> MI[Managed Identity]
    MI --> KV[Key Vault]
    KV --> Secrets[Secrets]
    Secrets --> Rotation[Automatic Rotation]
    Rotation --> Access[Access Logs]
    KV --> Audit[Audit Logs]
```

**Implementation:**
- Azure Key Vault for all secrets
- Managed identities for Azure resources
- Automatic key rotation (90 days)
- Audit logging for all key access

### Network Security

```mermaid
flowchart TB
    Internet[Internet] --> WAF[Web Application Firewall]
    WAF --> CDN[CDN]
    CDN --> LB[Load Balancer]
    LB --> AppGateway[Application Gateway]
    AppGateway --> API[API Services]
    AppGateway --> VNet[Virtual Network]
    VNet --> PEP[Private Endpoints]
    PEP --> SQL[(SQL Server)]
    PEP --> Cosmos[(Cosmos DB)]
    PEP --> Blob[(Blob Storage)]
```

**Implementation:**
- Azure WAF with OWASP rules
- DDoS protection
- Virtual Network isolation
- Private endpoints for Azure services
- Network security groups (NSGs)

## Monitoring & Logging Controls

### Logging Strategy

```mermaid
flowchart TB
    App[Application] --> Logs[Structured Logs]
    Logs --> Serilog[Serilog]
    Serilog --> Sink1[Application Insights]
    Serilog --> Sink2[Log Analytics]
    Serilog --> Sink3[Blob Storage]
    Sink1 --> Alerts[Alert Rules]
    Sink2 --> Queries[Log Queries]
    Sink3 --> Archive[Long-term Archive]
    Alerts --> Notify[Notifications]
```

**Log Types:**
- Security events (login, authorization failures)
- Audit events (data changes, permission changes)
- Performance metrics
- Error logs
- Access logs

### Alerting

```mermaid
flowchart TB
    Metric[Metrics] --> Threshold[Threshold Check]
    Threshold --> Breached[Breached?]
    Breached --> Yes[Trigger Alert]
    Breached --> No[Continue Monitoring]
    Yes --> Severity[Assign Severity]
    Severity --> Notify[Notify On-Call]
    Notify --> Incident[Create Incident]
    Incident --> Escalate[Escalate if Not Acknowledged]
```

**Alert Types:**
- Failed authentication attempts (> 10/min)
- Authorization failures (> 50/min)
- Unusual data access patterns
- API rate limit breaches
- Database connection failures
- Application errors (> 5/min)

## Compliance Controls

### GDPR Compliance

| Requirement | Control |
|-------------|---------|
| Data protection by design | Privacy by design principles in architecture |
| Right to be forgotten | Data deletion workflows |
| Data portability | Export functionality |
| Breach notification | Automated breach detection and notification |
| Consent management | Cookie consent, data processing consent |

### SOC 2 Compliance

| Trust Principle | Control |
|-----------------|---------|
| Security | Access controls, encryption, monitoring |
| Availability | High availability architecture, backups |
| Processing integrity | Change management, audit logging |
| Confidentiality | Data classification, access controls |
| Privacy | Data minimization, consent management |

## Security Testing

### Regular Security Activities

- **Weekly**: Automated dependency vulnerability scanning
- **Monthly**: Security log review, access review
- **Quarterly**: Penetration testing, security architecture review
- **Annually**: Full security audit, compliance audit

### Security Testing Tools

- **SAST**: SonarQube, GitHub Advanced Security
- **DAST**: OWASP ZAP, Burp Suite
- **Dependency Scanning**: Dependabot, Snyk
- **Container Scanning**: Trivy, Azure Container Scan
- **Infrastructure Scanning**: Azure Security Center

## Incident Response

### Incident Response Process

```mermaid
flowchart TB
    Detect[Detect Incident] --> Triage[Triage & Classify]
    Triage --> Contain[Contain Incident]
    Contain --> Eradicate[Eradicate Threat]
    Eradicate --> Recover[Recover Systems]
    Recover --> Post[Post-Incident Review]
    Post --> Improve[Improve Controls]
```

### Incident Response Team

- **Incident Commander**: Overall coordination
- **Security Engineer**: Technical investigation
- **DevOps Engineer**: System recovery
- **Legal/Compliance**: Regulatory requirements
- **Communications**: Stakeholder communication

### Incident Categories

| Category | Severity | Response Time |
|----------|----------|---------------|
| Critical | P0 | 15 minutes |
| High | P1 | 1 hour |
| Medium | P2 | 4 hours |
| Low | P3 | 24 hours |

## Security Best Practices

### Development

- Code reviews required for all changes
- Security training for all developers
- Secure coding guidelines
- Regular security testing in CI/CD

### Operations

- Least privilege access for operations
- Separate admin accounts
- Regular access reviews
- Secure secrets management

### Architecture

- Defense in depth
- Zero trust architecture
- Secure by design
- Regular threat modeling
