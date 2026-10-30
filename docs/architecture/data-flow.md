# Data Flow Diagrams

## REST API Request Flow

```mermaid
sequenceDiagram
    participant Client as Web Client
    participant Middleware as Middleware Pipeline
    participant Auth as Auth Middleware
    participant Controller as REST Controller
    participant Validator as FluentValidation
    participant Handler as Command Handler
    participant Domain as Domain Layer
    participant Repository as Repository
    participant DB as SQL Database
    participant Outbox as Outbox
    participant EventBus as Event Bus

    Client->>Middleware: HTTP Request
    Middleware->>Auth: Validate JWT
    Auth-->>Middleware: User Claims
    Middleware->>Controller: Route Request

    Controller->>Validator: Validate Input
    Validator-->>Controller: Validation Result

    Controller->>Handler: Execute Command
    Handler->>Domain: Load Aggregate
    Domain->>Repository: Query Data
    Repository->>DB: SELECT
    DB-->>Repository: Data
    Repository-->>Domain: Entity
    Domain-->>Handler: Aggregate

    Handler->>Domain: Execute Business Logic
    Domain->>Domain: Raise Domain Events

    Handler->>Repository: Save Changes
    Repository->>DB: INSERT/UPDATE
    DB-->>Repository: Success
    Repository-->>Handler: Saved

    Handler->>Outbox: Store Outbox Messages
    Outbox->>DB: INSERT Outbox
    DB-->>Outbox: Stored

    Handler->>EventBus: Publish Events
    EventBus-->>Handler: Published

    Handler-->>Controller: Result
    Controller-->>Middleware: Response
    Middleware-->>Client: HTTP Response
```

## GraphQL Query Flow

```mermaid
sequenceDiagram
    participant Client as Web Client
    participant GraphQL as GraphQL Server
    participant Schema as GraphQL Schema
    participant Resolver as Resolver
    participant DataLoader as DataLoader
    participant Handler as Query Handler
    participant DB as SQL Database
    participant Cosmos as Cosmos DB

    Client->>GraphQL: GraphQL Query
    GraphQL->>Schema: Parse & Validate
    Schema-->>GraphQL: Validated Query

    GraphQL->>Resolver: Execute Field
    Resolver->>DataLoader: Batch Load
    DataLoader->>Handler: Execute Query
    Handler->>DB: Query Data
    DB-->>Handler: Results
    Handler-->>DataLoader: Data
    DataLoader-->>Resolver: Entities

    Resolver->>Cosmos: Query Projections
    Cosmos-->>Resolver: Projection Data

    Resolver-->>GraphQL: Field Result
    GraphQL->>Resolver: Execute Next Field
    Resolver-->>GraphQL: Field Result

    GraphQL-->>Client: GraphQL Response
```

## Document Upload Flow

```mermaid
sequenceDiagram
    participant Client as Web Client
    participant API as REST API
    participant Validator as Document Validator
    participant Metadata as Document Service
    participant Blob as Blob Storage
    participant DB as SQL Database
    participant Cosmos as Cosmos DB
    participant Search as Azure AI Search

    Client->>API: POST /documents/initiate
    API->>Validator: Validate Request
    Validator-->>API: Valid
    API->>Metadata: Create Document Record
    Metadata->>DB: INSERT Document
    DB-->>Metadata: Created
    Metadata-->>API: Document ID + SAS URL

    API-->>Client: Upload URL

    Client->>Blob: Upload File (SAS URL)
    Blob-->>Client: Upload Complete

    Client->>API: POST /documents/{id}/confirm
    API->>Metadata: Confirm Upload
    Metadata->>Blob: Verify File
    Blob-->>Metadata: Verified
    Metadata->>DB: UPDATE Document Status
    DB-->>Metadata: Updated

    Metadata->>Cosmos: Store Projection
    Cosmos-->>Metadata: Stored

    Metadata->>Search: Index Document
    Search-->>Metadata: Indexed

    Metadata-->>API: Success
    API-->>Client: Document Created
```

## Lease Accounting Calculation Flow

```mermaid
sequenceDiagram
    participant User as Accountant
    participant API as REST API
    participant Handler as Accounting Handler
    participant Domain as Accounting Domain
    participant CalcService[Calculation Service]
    participant DB as SQL Database
    participant Audit as Audit Service

    User->>API: POST /leases/{id}/measurements
    API->>Handler: Create Measurement Command
    Handler->>Domain: Load Lease
    Domain->>DB: Query Lease Data
    DB-->>Domain: Lease Aggregate

    Handler->>CalcService: Calculate ROU Asset
    CalcService->>CalcService: Discount Cash Flows
    CalcService->>CalcService: Calculate Amortization
    CalcService-->>Handler: ROU Asset Value

    Handler->>CalcService: Calculate Lease Liability
    CalcService->>CalcService: Calculate Present Value
    CalcService-->>Handler: Liability Value

    Handler->>Domain: Create Measurement
    Domain->>Domain: Generate Journal Entries
    Domain-->>Handler: Measurement Created

    Handler->>DB: Save Measurement
    DB-->>Handler: Saved

    Handler->>Audit: Log Calculation
    Audit->>DB: Store Audit Event
    DB-->>Audit: Stored

    Handler-->>API: Measurement Result
    API-->>User: Measurement Details
```

## Event-Driven Background Processing Flow

```mermaid
sequenceDiagram
    participant API as API Handler
    participant Outbox as Outbox Table
    participant Worker as Background Worker
    participant ServiceBus as Service Bus
    participant Function as Azure Function
    participant External as External System

    API->>Outbox: Store Outbox Message
    Outbox->>DB: INSERT with Pending Status
    DB-->>Outbox: Stored
    API-->>API: Continue

    Worker->>Outbox: Poll for Pending Messages
    Outbox->>DB: SELECT Pending
    DB-->>Outbox: Messages
    Outbox-->>Worker: Messages

    Worker->>ServiceBus: Send Message
    ServiceBus-->>Worker: Sent

    Worker->>Outbox: Mark as Processed
    Outbox->>DB: UPDATE Status
    DB-->>Outbox: Updated

    ServiceBus->>Function: Trigger Function
    Function->>External: Call External API
    External-->>Function: Response

    Function->>DB: Update State
    DB-->>Function: Updated
```

## Search Indexing Flow

```mermaid
sequenceDiagram
    participant API as API Handler
    participant DB as SQL Database
    participant Projection as Projection Service
    participant Cosmos as Cosmos DB
    participant Search as Azure AI Search
    participant Client as Web Client

    API->>DB: Update Entity
    DB-->>API: Updated

    API->>Projection: Generate Projection
    Projection->>DB: Query Related Data
    DB-->>Projection: Data
    Projection->>Projection: Build Projection Document
    Projection-->>API: Projection

    API->>Cosmos: Upsert Projection
    Cosmos-->>API: Stored

    API->>Search: Index Document
    Search-->>API: Indexed

    Client->>Search: Search Query
    Search->>Search: Execute Search
    Search-->>Client: Search Results
```
