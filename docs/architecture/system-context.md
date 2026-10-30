# System Context Diagram

```mermaid
flowchart TB
    subgraph "External Actors"
        PropertyManager[Property Manager]
        Tenant[Tenant]
        Landlord[Landlord]
        Accountant[Accountant]
        Auditor[Auditor]
    end

    subgraph "LeaseCore Platform"
        WebApp[Web Application<br/>React + TypeScript]
        API[REST API<br/>ASP.NET Core]
        GraphQL[GraphQL API<br/>Hot Chocolate]
        Auth[Authentication<br/>Entra ID]
    end

    subgraph "External Systems"
        AzureServices[Azure Services]
        PaymentGateway[Payment Gateway]
        EmailService[Email Service]
    end

    PropertyManager --> WebApp
    Tenant --> WebApp
    Landlord --> WebApp
    Accountant --> WebApp
    Auditor --> WebApp

    WebApp --> API
    WebApp --> GraphQL
    API --> Auth
    GraphQL --> Auth

    API --> AzureServices
    GraphQL --> AzureServices

    API --> PaymentGateway
    GraphQL --> PaymentGateway

    API --> EmailService
    GraphQL --> EmailService

    style PropertyManager fill:#e1f5e1
    style Tenant fill:#e1f5e1
    style Landlord fill:#e1f5e1
    style Accountant fill:#e1f5e1
    style Auditor fill:#e1f5e1
    style WebApp fill:#e1e5f5
    style API fill:#e1e5f5
    style GraphQL fill:#e1e5f5
    style Auth fill:#e1e5f5
    style AzureServices fill:#f5e1e1
    style PaymentGateway fill:#f5e1e1
    style EmailService fill:#f5e1e1
```

## System Overview

The LeaseCore platform serves multiple external actors through a unified web application interface:

### External Actors

- **Property Manager**: Manages portfolios, properties, and leases
- **Tenant**: Views lease information, obligations, and documents
- **Landlord**: Reviews property performance and lease terms
- **Accountant**: Handles lease accounting and financial reporting
- **Auditor**: Audits compliance and financial records

### Core Components

- **Web Application**: React + TypeScript frontend providing user interface
- **REST API**: ASP.NET Core Web API for resource operations
- **GraphQL API**: Hot Chocolate for complex data queries
- **Authentication**: Microsoft Entra ID for identity and access management

### External Integrations

- **Azure Services**: SQL, Cosmos DB, Blob Storage, Functions, Service Bus
- **Payment Gateway**: For rent payment processing
- **Email Service**: For notifications and reminders
