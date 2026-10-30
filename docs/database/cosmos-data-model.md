# Cosmos DB Data Model

## Purpose

Cosmos DB is used for document-oriented data that benefits from flexible schema, append-only patterns, and read-optimized access patterns. It is NOT a second write authority for transactional data stored in SQL Server.

## Containers

### 1. Activity Timeline (ActivityTimeline)

Stores append-only activity events for entity timelines and audit trails.

```json
{
  "id": "activity-uuid",
  "partitionKey": "tenant-id",
  "tenantId": "tenant-uuid",
  "entityType": "Lease|Property|Document|Task",
  "entityId": "entity-uuid",
  "activityType": "Created|Updated|Deleted|StatusChanged",
  "actorId": "user-uuid",
  "actorName": "John Doe",
  "description": "Lease created with reference REF-001",
  "changes": {
    "before": {},
    "after": {}
  },
  "timestamp": "2026-01-15T10:30:00Z",
  "metadata": {}
}
```

**Partition Key**: `tenantId`
**Indexing**: Optimized for querying by `entityId` and `timestamp`

### 2. Search Projections (SearchProjections)

Read-optimized documents for search and reporting queries.

```json
{
  "id": "projection-uuid",
  "partitionKey": "tenant-id",
  "tenantId": "tenant-uuid",
  "projectionType": "LeaseSummary|PropertySummary|PortfolioOverview",
  "leaseId": "lease-uuid",
  "propertyId": "property-uuid",
  "portfolioId": "portfolio-uuid",
  "organisationId": "organisation-uuid",
  "lease": {
    "reference": "REF-001",
    "status": "Active",
    "startDate": "2026-01-01",
    "endDate": "2028-12-31",
    "baseRent": 5000.00,
    "currency": "USD",
    "tenantName": "ABC Corp",
    "landlordName": "XYZ Properties"
  },
  "property": {
    "name": "Downtown Office Tower",
    "address": "123 Main St",
    "city": "New York",
    "state": "NY"
  },
  "nextKeyDates": [
    {
      "type": "RentReview",
      "dueDate": "2026-07-01",
      "description": "Annual rent review"
    }
  ],
  "tasks": {
    "open": 3,
    "overdue": 1
  },
  "lastModified": "2026-01-15T10:30:00Z",
  "searchText": "ABC Corp XYZ Properties Downtown Office Tower REF-001"
}
```

**Partition Key**: `tenantId`
**Indexing**: Full-text search on `searchText`, filters on status, dates

### 3. Document Projections (DocumentProjections)

Optimized document metadata for search and retrieval.

```json
{
  "id": "doc-projection-uuid",
  "partitionKey": "tenant-id",
  "tenantId": "tenant-uuid",
  "documentId": "document-uuid",
  "leaseId": "lease-uuid",
  "propertyId": "property-uuid",
  "title": "Lease Agreement - REF-001",
  "category": "Lease Agreement",
  "fileName": "lease-ref-001.pdf",
  "fileSize": 2457890,
  "mimeType": "application/pdf",
  "status": "Active",
  "uploadedBy": "user-uuid",
  "uploadedByName": "John Doe",
  "version": 1,
  "tags": ["lease", "agreement", "signed"],
  "uploadedAt": "2026-01-15T10:30:00Z",
  "lastAccessedAt": "2026-01-20T14:45:00Z",
  "searchText": "Lease Agreement REF-001 ABC Corp signed"
}
```

**Partition Key**: `tenantId`
**Indexing**: Search on title, category, tags, filters on date ranges

### 4. Reporting Snapshots (ReportingSnapshots)

Pre-aggregated data for operational reports.

```json
{
  "id": "snapshot-uuid",
  "partitionKey": "tenant-id:report-type:period",
  "tenantId": "tenant-uuid",
  "reportType": "LeaseExpirations|RentRoll|PortfolioPerformance",
  "period": "2026-01",
  "generatedAt": "2026-02-01T00:00:00Z",
  "data": {
    "totalLeases": 150,
    "activeLeases": 120,
    "expiringLeases": 15,
    "totalRent": 750000.00,
    "currency": "USD",
    "byStatus": {
      "Active": 120,
      "Pending": 10,
      "Expired": 20
    },
    "byPropertyType": {
      "Office": 80,
      "Retail": 40,
      "Industrial": 30
    }
  }
}
```

**Partition Key**: `tenantId:reportType:period`
**Indexing**: Query by tenant, report type, and period

### 5. Integration Events (IntegrationEvents)

Outbox pattern for reliable event publishing to external systems.

```json
{
  "id": "event-uuid",
  "partitionKey": "tenant-id",
  "tenantId": "tenant-uuid",
  "eventType": "LeaseCreated|LeaseAmended|DocumentUploaded",
  "eventId": "event-uuid",
  "correlationId": "correlation-uuid",
  "payload": {
    "leaseId": "lease-uuid",
    "reference": "REF-001",
    "changes": {}
  },
  "status": "Pending|Processed|Failed",
  "retryCount": 0,
  "lastError": null,
  "createdAt": "2026-01-15T10:30:00Z",
  "processedAt": null
}
```

**Partition Key**: `tenantId`
**Indexing**: Query by status for background processing

## Partitioning Strategy

### Tenant-Based Partitioning

All containers use `tenantId` as the primary partition key to:
- Ensure data isolation between tenants
- Distribute load evenly across tenants
- Simplify tenant-scoped queries
- Support multi-tenant scale-out

### Composite Partition Keys

For reporting snapshots, use composite keys: `tenantId:reportType:period`
- Groups related data together
- Enables efficient time-series queries
- Maintains tenant isolation

## Consistency Levels

| Container | Consistency Level | Rationale |
|-----------|-------------------|-----------|
| ActivityTimeline | Session | Strong consistency for audit trails within a session |
| SearchProjections | Eventual | Acceptable for search data, higher throughput |
| DocumentProjections | Eventual | Acceptable for document metadata, higher throughput |
| ReportingSnapshots | Eventual | Pre-aggregated data, consistency not critical |
| IntegrationEvents | Session | Ensure events are processed in order |

## TTL (Time To Live)

| Container | TTL | Rationale |
|-----------|-----|-----------|
| ActivityTimeline | 730 days (2 years) | Retain audit trail for compliance |
| SearchProjections | No TTL | Keep until explicitly deleted |
| DocumentProjections | No TTL | Keep until document deleted |
| ReportingSnapshots | 1825 days (5 years) | Historical reporting data |
| IntegrationEvents | 30 days | Clean up after processing |

## Indexing Policy

### ActivityTimeline

```json
{
  "indexingMode": "consistent",
  "includedPaths": [
    {
      "path": "/",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "number"
        },
        {
          "kind": "Range",
          "dataType": "string"
        },
        {
          "kind": "Spatial",
          "dataType": "point"
        }
      ]
    }
  ],
  "excludedPaths": [
    {
      "path": "/\"metadata\"/*"
    }
  ]
}
```

### SearchProjections

```json
{
  "indexingMode": "consistent",
  "includedPaths": [
    {
      "path": "/",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "number"
        },
        {
          "kind": "Range",
          "dataType": "string"
        }
      ]
    }
  ],
  "compositeIndexes": [
    [
      { "path": "/tenantId", "order": "ascending" },
      { "path": "/lease/status", "order": "ascending" }
    ],
    [
      { "path": "/tenantId", "order": "ascending" },
      { "path": "/lastModified", "order": "descending" }
    ]
  ],
  "fullTextIndexes": [
    {
      "path": "/searchText",
      "indexKind": "Lexical"
    }
  ]
}
```

## Data Synchronization

### SQL → Cosmos Projections

Projections are updated via:
1. Domain events from SQL Server changes
2. Background worker processes
3. Outbox pattern for reliability

### Consistency Guarantees

- **Eventual consistency** for projections is acceptable
- Projections are **read-only** from client perspective
- **Idempotent** projection updates handle retries
- **Versioning** on source entities ensures correct ordering

## Query Patterns

### Timeline Query

```sql
SELECT * FROM c
WHERE c.partitionKey = @tenantId
  AND c.entityId = @entityId
ORDER BY c.timestamp DESC
```

### Search Query

```sql
SELECT * FROM c
WHERE c.partitionKey = @tenantId
  AND CONTAINS(c.searchText, @searchTerm)
ORDER BY c.lastModified DESC
```

### Reporting Query

```sql
SELECT * FROM c
WHERE c.partitionKey = @tenantId + ":" + @reportType + ":" + @period
```

## Cost Optimization

1. **RU Efficiency**: Design queries to use partition key
2. **Document Size**: Keep projections lean, avoid over-fetching
3. **Indexing**: Exclude unnecessary paths from indexing
4. **TTL**: Automatically delete old data
5. **Caching**: Cache frequently accessed projections in Redis
