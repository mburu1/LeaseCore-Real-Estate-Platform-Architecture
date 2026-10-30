# GraphQL Schema

## Schema Overview

The GraphQL API provides a flexible, typed interface for querying and mutating data. It uses Hot Chocolate for the GraphQL server implementation.

## Endpoint

```
https://api.leasecore.com/graphql
```

## Authentication

JWT Bearer token required via Authorization header:

```
Authorization: Bearer <jwt-token>
```

## Root Types

### Query

```graphql
type Query {
  # Portfolio queries
  portfolio(id: UUID!): Portfolio
  portfolios(input: PortfolioFilterInput!): PortfolioConnection!

  # Property queries
  property(id: UUID!): Property
  properties(input: PropertyFilterInput!): PropertyConnection!

  # Lease queries
  lease(id: UUID!): Lease
  leases(input: LeaseFilterInput!): LeaseConnection!

  # Document queries
  document(id: UUID!): Document
  documents(input: DocumentFilterInput!): DocumentConnection!

  # Task queries
  task(id: UUID!): Task
  tasks(input: TaskFilterInput!): TaskConnection!

  # Accounting queries
  leaseAccounting(leaseId: UUID!): LeaseAccounting
  leaseMeasurements(leaseId: UUID!): [LeaseMeasurement!]!

  # Reporting queries
  leaseExpirations(input: ExpirationReportInput!): LeaseExpirationReport!
  portfolioSummary(portfolioId: UUID!): PortfolioSummary!
}
```

### Mutation

```graphql
type Mutation {
  # Portfolio mutations
  createPortfolio(input: CreatePortfolioInput!): CreatePortfolioPayload!
  updatePortfolio(input: UpdatePortfolioInput!): UpdatePortfolioPayload!
  deletePortfolio(id: UUID!): DeletePortfolioPayload!

  # Property mutations
  createProperty(input: CreatePropertyInput!): CreatePropertyPayload!
  updateProperty(input: UpdatePropertyInput!): UpdatePropertyPayload!
  deleteProperty(id: UUID!): DeletePropertyPayload!

  # Lease mutations
  createLease(input: CreateLeaseInput!): CreateLeasePayload!
  updateLease(input: UpdateLeaseInput!): UpdateLeasePayload!
  deleteLease(id: UUID!): DeleteLeasePayload!
  createLeaseAmendment(input: CreateLeaseAmendmentInput!): CreateLeaseAmendmentPayload!

  # Document mutations
  initiateDocumentUpload(input: InitiateDocumentUploadInput!): InitiateDocumentUploadPayload!
  confirmDocumentUpload(id: UUID!): ConfirmDocumentUploadPayload!

  # Task mutations
  createTask(input: CreateTaskInput!): CreateTaskPayload!
  updateTaskStatus(input: UpdateTaskStatusInput!): UpdateTaskStatusPayload!
  assignTask(input: AssignTaskInput!): AssignTaskPayload!

  # Accounting mutations
  createLeaseMeasurement(input: CreateLeaseMeasurementInput!): CreateLeaseMeasurementPayload!
}
```

## Types

### Portfolio

```graphql
type Portfolio {
  id: UUID!
  name: String!
  description: String
  organisation: Organisation!
  properties(first: Int, after: String): PropertyConnection!
  propertyCount: Int!
  createdAt: DateTime!
  updatedAt: DateTime!
}
```

### Property

```graphql
type Property {
  id: UUID!
  name: String!
  address: String
  city: String
  state: String
  postalCode: String
  country: String
  portfolio: Portfolio!
  buildings(first: Int, after: String): BuildingConnection!
  leases(first: Int, after: String): LeaseConnection!
  createdAt: DateTime!
  updatedAt: DateTime!
}
```

### Building

```graphql
type Building {
  id: UUID!
  name: String!
  description: String
  totalArea: Float
  areaUnit: String
  property: Property!
  units(first: Int, after: String): UnitConnection!
  createdAt: DateTime!
  updatedAt: DateTime!
}
```

### Unit

```graphql
type Unit {
  id: UUID!
  name: String!
  unitNumber: String
  area: Float
  areaUnit: String
  unitType: String
  building: Building!
  leases(first: Int, after: String): LeaseConnection!
  createdAt: DateTime!
  updatedAt: DateTime!
}
```

### Lease

```graphql
type Lease {
  id: UUID!
  reference: String!
  status: LeaseStatus!
  startDate: Date!
  endDate: Date!
  baseRent: Float!
  currency: String!
  property: Property!
  unit: Unit!
  parties: [LeaseParty!]!
  terms: [LeaseTerm!]!
  amendments(first: Int, after: String): LeaseAmendmentConnection!
  rentSchedule: RentSchedule
  rentReviews(first: Int, after: String): RentReviewConnection!
  options(first: Int, after: String): LeaseOptionConnection!
  keyDates(first: Int, after: String): KeyDateConnection!
  documents(first: Int, after: String): DocumentConnection!
  tasks(first: Int, after: String): TaskConnection!
  accounting: LeaseAccounting
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum LeaseStatus {
  Draft
  Submitted
  Approved
  Rejected
  Executed
  Active
  UnderRenewal
  Terminating
  Terminated
  Expired
}
```

### LeaseParty

```graphql
type LeaseParty {
  id: UUID!
  partyType: LeasePartyType!
  name: String!
  contactEmail: String
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum LeasePartyType {
  Landlord
  Tenant
  Guarantor
  Agent
}
```

### Document

```graphql
type Document {
  id: UUID!
  title: String!
  description: String
  category: DocumentCategory!
  fileName: String!
  fileSize: Float!
  mimeType: String!
  status: DocumentStatus!
  lease: Lease
  property: Property
  versions(first: Int, after: String): DocumentVersionConnection!
  uploadedBy: User!
  uploadedAt: DateTime!
  lastAccessedAt: DateTime
}

enum DocumentStatus {
  Uploading
  Active
  Archived
  Deleted
}
```

### Task

```graphql
type Task {
  id: UUID!
  title: String!
  description: String
  priority: TaskPriority!
  status: TaskStatus!
  dueDate: Date
  lease: Lease
  property: Property
  assignments: [TaskAssignment!]!
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum TaskPriority {
  Low
  Medium
  High
  Critical
}

enum TaskStatus {
  Open
  InProgress
  Completed
  Cancelled
}
```

### LeaseAccounting

```graphql
type LeaseAccounting {
  leaseId: UUID!
  rouAsset: RightOfUseAsset!
  leaseLiability: LeaseLiability!
  currency: String!
  lastMeasurementDate: Date
  measurements(first: Int, after: String): LeaseMeasurementConnection!
}

type RightOfUseAsset {
  initialValue: Float!
  accumulatedAmortization: Float!
  carryingAmount: Float!
}

type LeaseLiability {
  initialValue: Float!
  interestAccrued: Float!
  paymentsMade: Float!
  carryingAmount: Float!
}
```

## Connections (Pagination)

```graphql
type PortfolioConnection {
  edges: [PortfolioEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type PortfolioEdge {
  node: Portfolio!
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}
```

## Input Types

### CreateLeaseInput

```graphql
input CreateLeaseInput {
  reference: String!
  propertyId: UUID!
  unitId: UUID!
  status: LeaseStatus!
  startDate: Date!
  endDate: Date!
  baseRent: Float!
  currency: String!
  landlord: CreateLeasePartyInput!
  tenant: CreateLeasePartyInput!
}
```

### LeaseFilterInput

```graphql
input LeaseFilterInput {
  propertyId: UUID
  status: LeaseStatus
  startDateFrom: Date
  startDateTo: Date
  endDateFrom: Date
  endDateTo: Date
  search: String
  first: Int
  after: String
}
```

## Payload Types (Mutation Results)

```graphql
type CreateLeasePayload {
  lease: Lease
  errors: [UserError!]!
}

type UserError {
  field: String
  message: String!
  code: String
}
```

## Example Queries

### Portfolio Overview

```graphql
query PortfolioOverview($portfolioId: UUID!) {
  portfolio(id: $portfolioId) {
    id
    name
    description
    propertyCount
    properties(first: 10) {
      edges {
        node {
          id
          name
          city
          leaseCount
        }
      }
    }
  }
}
```

### Lease Details with Related Data

```graphql
query LeaseDetails($leaseId: UUID!) {
  lease(id: $leaseId) {
    id
    reference
    status
    startDate
    endDate
    baseRent
    currency
    property {
      id
      name
      address
    }
    unit {
      id
      name
      area
    }
    parties {
      id
      partyType
      name
      contactEmail
    }
    keyDates(first: 10) {
      edges {
        node {
          id
          dateType
          dueDate
          description
        }
      }
    }
    documents(first: 5) {
      edges {
        node {
          id
          title
          category
          fileName
        }
      }
    }
    accounting {
      rouAsset {
        initialValue
        carryingAmount
      }
      leaseLiability {
        initialValue
        carryingAmount
      }
    }
  }
}
```

### Search Leases

```graphql
query SearchLeases($input: LeaseFilterInput!) {
  leases(input: $input) {
    edges {
      node {
        id
        reference
        status
        startDate
        endDate
        baseRent
        property {
          name
          city
        }
        tenant {
          name
        }
      }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
    totalCount
  }
}
```

## Example Mutations

### Create Lease

```graphql
mutation CreateLease($input: CreateLeaseInput!) {
  createLease(input: $input) {
    lease {
      id
      reference
      status
    }
    errors {
      field
      message
    }
  }
}
```

### Create Task

```graphql
mutation CreateTask($input: CreateTaskInput!) {
  createTask(input: $input) {
    task {
      id
      title
      status
      dueDate
    }
    errors {
      field
      message
    }
  }
}
```

## Directives

### @auth

Authorization directive to enforce permissions:

```graphql
directive @auth(
  require: String
) on FIELD_DEFINITION

type Mutation {
  deleteLease(id: UUID!): DeleteLeasePayload! @auth(require: "lease:delete")
}
```

### @validate

Validation directive for input:

```graphql
directive @validate on ARGUMENT_DEFINITION | INPUT_FIELD_DEFINITION

input CreateLeaseInput {
  reference: String! @validate(pattern: "^[A-Z]{2}-\\d{3}$")
  startDate: Date! @validate
  endDate: Date! @validate
}
```

## Query Complexity & Limits

### Complexity Limits

- Default complexity limit: 1000
- Maximum query depth: 10
- Maximum field count: 100

### Rate Limiting

- Same as REST API: 1000 requests per minute per tenant

### Query Analysis

Queries are analyzed before execution to enforce complexity limits. If a query exceeds limits, it is rejected with an error.

## N+1 Prevention

Use DataLoaders for batching related entity queries:

```graphql
# Without DataLoader (N+1 query)
leases {
  edges {
    node {
      property { name }  # N queries
    }
  }
}

# With DataLoader (batched query)
leases {
  edges {
    node {
      property { name }  # 1 batched query
    }
  }
}
```

## Scalar Types

```graphql
scalar UUID
scalar Date
scalar DateTime
scalar Float
scalar Int
scalar String
scalar Boolean
```

## Error Handling

GraphQL errors are returned in the `errors` field of the response:

```json
{
  "data": {
    "lease": null
  },
  "errors": [
    {
      "message": "Lease not found",
      "path": ["lease"],
      "extensions": {
        "code": "NOT_FOUND"
      }
    }
  ]
}
```

Mutation-specific errors are returned in the payload's `errors` field for partial success handling.
