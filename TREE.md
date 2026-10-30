# LeaseCore Repository Structure

```
LeaseCore/
├── LeaseCore.slnx
├── global.json
├── Directory.Build.props
├── Directory.Packages.props
├── .editorconfig
├── .gitignore
├── .dockerignore
├── README.md
├── LICENSE
├── SECURITY.md
├── CONTRIBUTING.md
│
├── src/
│   ├── BuildingBlocks/
│   │   ├── LeaseCore.BuildingBlocks.Domain/
│   │   ├── LeaseCore.BuildingBlocks.Application/
│   │   └── LeaseCore.BuildingBlocks.Infrastructure/
│   │
│   ├── Modules/
│   │   ├── Identity/
│   │   │   ├── LeaseCore.Identity.Domain/
│   │   │   ├── LeaseCore.Identity.Application/
│   │   │   └── LeaseCore.Identity.Infrastructure/
│   │   ├── Portfolio/
│   │   │   ├── LeaseCore.Portfolio.Domain/
│   │   │   ├── LeaseCore.Portfolio.Application/
│   │   │   └── LeaseCore.Portfolio.Infrastructure/
│   │   ├── Leases/
│   │   │   ├── LeaseCore.Leases.Domain/
│   │   │   ├── LeaseCore.Leases.Application/
│   │   │   └── LeaseCore.Leases.Infrastructure/
│   │   ├── Documents/
│   │   │   ├── LeaseCore.Documents.Domain/
│   │   │   ├── LeaseCore.Documents.Application/
│   │   │   └── LeaseCore.Documents.Infrastructure/
│   │   ├── Obligations/
│   │   │   ├── LeaseCore.Obligations.Domain/
│   │   │   ├── LeaseCore.Obligations.Application/
│   │   │   └── LeaseCore.Obligations.Infrastructure/
│   │   ├── WorkManagement/
│   │   │   ├── LeaseCore.WorkManagement.Domain/
│   │   │   ├── LeaseCore.WorkManagement.Application/
│   │   │   └── LeaseCore.WorkManagement.Infrastructure/
│   │   ├── Accounting/
│   │   │   ├── LeaseCore.Accounting.Domain/
│   │   │   ├── LeaseCore.Accounting.Application/
│   │   │   └── LeaseCore.Accounting.Infrastructure/
│   │   ├── Search/
│   │   │   ├── LeaseCore.Search.Application/
│   │   │   └── LeaseCore.Search.Infrastructure/
│   │   └── Audit/
│   │       ├── LeaseCore.Audit.Domain/
│   │       ├── LeaseCore.Audit.Application/
│   │       └── LeaseCore.Audit.Infrastructure/
│   │
│   ├── Hosts/
│   │   ├── LeaseCore.Api/
│   │   │   ├── Endpoints/
│   │   │   ├── GraphQL/
│   │   │   ├── Middleware/
│   │   │   ├── Authorization/
│   │   │   ├── OpenApi/
│   │   │   └── Program.cs
│   │   ├── LeaseCore.Worker/
│   │   └── LeaseCore.Functions/
│   │
│   └── Frontend/
│       └── leasecore-web/
│           ├── src/
│           │   ├── app/
│           │   ├── features/
│           │   ├── components/
│           │   ├── graphql/
│           │   ├── api/
│           │   └── routes/
│           ├── public/
│           └── package.json
│
├── tests/
│   ├── Unit/
│   │   ├── LeaseCore.Leases.UnitTests/
│   │   ├── LeaseCore.Accounting.UnitTests/
│   │   └── LeaseCore.Portfolio.UnitTests/
│   ├── Integration/
│   │   ├── LeaseCore.Api.IntegrationTests/
│   │   ├── LeaseCore.Persistence.IntegrationTests/
│   │   └── LeaseCore.Azure.IntegrationTests/
│   ├── Architecture/
│   │   └── LeaseCore.ArchitectureTests/
│   └── Contract/
│       └── LeaseCore.ContractTests/
│
├── deploy/
│   ├── docker/
│   ├── compose/
│   ├── bicep/
│   ├── scripts/
│   └── environments/
│       ├── development/
│       ├── staging/
│       └── production/
│
├── pipelines/
│   ├── github/
│   └── azure-devops/
│
└── docs/
    ├── architecture/
    │   ├── system-context.md
    │   ├── container-diagram.md
    │   ├── component-diagrams.md
    │   ├── data-flow.md
    │   └── decisions/
    ├── domain/
    │   ├── glossary.md
    │   ├── bounded-contexts.md
    │   ├── lease-lifecycle.md
    │   └── accounting-domain.md
    ├── database/
    │   ├── erd.md
    │   ├── sql-schema.md
    │   └── cosmos-data-model.md
    ├── api/
    │   ├── rest-api.md
    │   ├── graphql-schema.md
    │   └── authorization-matrix.md
    ├── security/
    │   ├── threat-model.md
    │   └── security-controls.md
    ├── operations/
    │   ├── runbook.md
    │   ├── backup-restore.md
    │   └── incident-response.md
    └── milestones/
        ├── implementation-roadmap.md
        └── acceptance-criteria.md
```
