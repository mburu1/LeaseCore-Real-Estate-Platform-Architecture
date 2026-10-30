# LeaseCore
## Commercial Real Estate Lease Lifecycle & Accounting Platform

> A production-oriented, multi-tenant SaaS platform for managing commercial property portfolios, lease agreements, documents, critical dates, tasks, lease accounting workflows and compliance.

**Backend:** C# / ASP.NET Core / .NET 10  
**REST API documentation:** Scalar (OpenAPI)  
**GraphQL:** Hot Chocolate  
**Architecture:** Clean Architecture, Domain-Driven Design (DDD), CQRS  
**Databases:** SQL Server / Azure SQL + Azure Cosmos DB  
**Frontend:** React + TypeScript  
**Cloud:** Microsoft Azure  
**Repository status:** Architecture and implementation roadmap

---

## Project vision

LeaseCore demonstrates how to design, implement, test, secure, observe and deploy the backend of a commercial real estate SaaS platform.

It is deliberately more than a property CRUD application. It models the business lifecycle around commercial leases and the operational and financial obligations that arise from them.

The platform supports:

1. **Portfolio and property management** — organisations, portfolios, properties, buildings, units, landlords and tenants.
2. **Lease lifecycle management** — lease creation, approval, execution, amendments, renewals, rent reviews, break clauses and termination.
3. **Document management** — secure upload, metadata, classification, versioning and retrieval of lease-related documents.
4. **Key dates and obligations** — expiry dates, notice windows, rent reviews, insurance renewals, inspections and recurring obligations.
5. **Task management** — assignable work, due dates, ownership, status transitions and escalation.
6. **Lease accounting** — payment schedules, discounting assumptions, right-of-use asset and lease liability calculations, journals and auditability.
7. **Search and reporting** — portfolio search, upcoming events, document discovery, operational reporting and accounting summaries.
8. **Compliance and audit** — immutable business audit events, controlled access and traceability of important changes.

The initial accounting scope is an **IFRS 16-oriented workflow**, not a claim of complete accounting-standard certification. Accounting policies, discount rates, exemptions, reassessments, modifications and journal treatment must be explicitly modelled, validated and reviewed by qualified accounting stakeholders.

## Documentation

- [Architecture Overview](docs/architecture/)
- [Domain Model](docs/domain/)
- [Database Schema](docs/database/)
- [API Documentation](docs/api/)
- [Security](docs/security/)
- [Operations](docs/operations/)
- [Implementation Roadmap](docs/milestones/)

## Getting Started

See the [implementation roadmap](docs/milestones/implementation-roadmap.md) for the current development status and next steps.

## License

See [LICENSE](LICENSE) for details.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for contribution guidelines.
