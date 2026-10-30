# Entity Relationship Diagram

```mermaid
erDiagram
    %% Identity & Tenancy
    TENANT ||--o{ ORGANISATION : "has"
    TENANT ||--o{ USER : "has"
    TENANT ||--o{ ROLE : "defines"
    ORGANISATION ||--o{ PORTFOLIO : "owns"
    ORGANISATION ||--o{ USER_MEMBERSHIP : "manages"
    USER ||--o{ USER_MEMBERSHIP : "belongs to"
    ROLE ||--o{ USER_MEMBERSHIP : "assigned to"
    ROLE ||--o{ PERMISSION : "has"
    USER_MEMBERSHIP ||--o{ PERMISSION : "grants"

    %% Portfolio
    PORTFOLIO ||--o{ PROPERTY : "contains"
    PROPERTY ||--o{ BUILDING : "has"
    BUILDING ||--o{ UNIT : "contains"
    ORGANISATION ||--o{ OWNERSHIP_INTEREST : "has"
    PROPERTY ||--o{ OWNERSHIP_INTEREST : "referenced by"

    %% Leases
    ORGANISATION ||--o{ LEASE : "manages"
    PROPERTY ||--o{ LEASE : "located at"
    UNIT ||--o{ LEASE : "for"
    LEASE ||--o{ LEASE_PARTY : "has"
    LEASE ||--o{ LEASE_TERM : "has"
    LEASE ||--o{ LEASE_AMENDMENT : "has"
    LEASE ||--o{ RENT_SCHEDULE : "has"
    RENT_SCHEDULE ||--o{ RENT_PAYMENT : "contains"
    LEASE ||--o{ RENT_REVIEW : "has"
    LEASE ||--o{ LEASE_OPTION : "has"
    LEASE ||--o{ LEASE_CHARGE : "has"

    %% Documents
    LEASE ||--o{ DOCUMENT : "has"
    PROPERTY ||--o{ DOCUMENT : "has"
    UNIT ||--o{ DOCUMENT : "has"
    DOCUMENT ||--o{ DOCUMENT_VERSION : "has"
    DOCUMENT_CATEGORY ||--o{ DOCUMENT : "classifies"
    DOCUMENT_VERSION ||--o{ DOCUMENT_STORAGE_REF : "references"
    DOCUMENT ||--o{ DOCUMENT_ACCESS_GRANT : "grants access to"

    %% Obligations
    LEASE ||--o{ KEY_DATE : "has"
    LEASE ||--o{ OBLIGATION : "has"
    KEY_DATE ||--o{ REMINDER : "triggers"
    OBLIGATION ||--o{ REMINDER : "triggers"
    REMINDER ||--o{ NOTIFICATION_DELIVERY : "generates"

    %% Work Management
    LEASE ||--o{ TASK : "related to"
    PROPERTY ||--o{ TASK : "related to"
    TASK ||--o{ TASK_ASSIGNMENT : "has"
    USER ||--o{ TASK_ASSIGNMENT : "assigned to"

    %% Accounting
    LEASE ||--o{ PAYMENT_SCHEDULE : "has"
    PAYMENT_SCHEDULE ||--o{ PAYMENT : "contains"
    LEASE ||--o{ DISCOUNT_RATE_ASSUMPTION : "has"
    LEASE ||--o{ LEASE_MEASUREMENT : "has"
    LEASE_MEASUREMENT ||--o{ RIGHT_OF_USE_ASSET : "produces"
    LEASE_MEASUREMENT ||--o{ LEASE_LIABILITY : "produces"
    LEASE_MEASUREMENT ||--o{ JOURNAL_ENTRY : "generates"
    JOURNAL_ENTRY ||--o{ JOURNAL_LINE : "contains"
    ACCOUNTING_PERIOD ||--o{ JOURNAL_ENTRY : "contains"
    LEASE ||--o{ REASSESSMENT : "has"
    LEASE ||--o{ MODIFICATION : "has"

    %% Audit
    USER ||--o{ AUDIT_EVENT : "performs"
    TENANT ||--o{ AUDIT_EVENT : "scoped to"
    LEASE ||--o{ AUDIT_EVENT : "related to"

    %% Outbox & Idempotency
    LEASE ||--o{ OUTBOX_MESSAGE : "generates"
    LEASE ||--o{ IDEMPOTENCY_RECORD : "idempotent"

    TENANT {
        uuid PK
        string name
        datetime created_at
        datetime updated_at
    }

    ORGANISATION {
        uuid PK
        uuid tenant_id FK
        string name
        string legal_name
        datetime created_at
        datetime updated_at
    }

    USER {
        uuid PK
        string email
        string display_name
        datetime created_at
        datetime updated_at
    }

    USER_MEMBERSHIP {
        uuid PK
        uuid user_id FK
        uuid organisation_id FK
        uuid tenant_id FK
        uuid role_id FK
        datetime created_at
        datetime updated_at
    }

    ROLE {
        uuid PK
        uuid tenant_id FK
        string name
        string description
        datetime created_at
        datetime updated_at
    }

    PERMISSION {
        uuid PK
        string code
        string description
    }

    PORTFOLIO {
        uuid PK
        uuid organisation_id FK
        uuid tenant_id FK
        string name
        string description
        datetime created_at
        datetime updated_at
    }

    PROPERTY {
        uuid PK
        uuid portfolio_id FK
        uuid tenant_id FK
        string name
        string address
        string city
        string state
        string postal_code
        string country
        datetime created_at
        datetime updated_at
    }

    BUILDING {
        uuid PK
        uuid property_id FK
        uuid tenant_id FK
        string name
        string description
        decimal total_area
        string area_unit
        datetime created_at
        datetime updated_at
    }

    UNIT {
        uuid PK
        uuid building_id FK
        uuid tenant_id FK
        string name
        string unit_number
        decimal area
        string area_unit
        string unit_type
        datetime created_at
        datetime updated_at
    }

    OWNERSHIP_INTEREST {
        uuid PK
        uuid organisation_id FK
        uuid property_id FK
        uuid tenant_id FK
        decimal ownership_percentage
        datetime effective_from
        datetime effective_to
        datetime created_at
        datetime updated_at
    }

    LEASE {
        uuid PK
        uuid organisation_id FK
        uuid property_id FK
        uuid unit_id FK
        uuid tenant_id FK
        string reference
        string status
        date start_date
        date end_date
        decimal base_rent
        string currency
        datetime created_at
        datetime updated_at
    }

    LEASE_PARTY {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string party_type
        string name
        string contact_email
        datetime created_at
        datetime updated_at
    }

    LEASE_TERM {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string term_type
        string description
        datetime created_at
        datetime updated_at
    }

    LEASE_AMENDMENT {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string amendment_type
        string description
        date effective_date
        datetime created_at
        datetime updated_at
    }

    RENT_SCHEDULE {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string schedule_type
        datetime created_at
        datetime updated_at
    }

    RENT_PAYMENT {
        uuid PK
        uuid rent_schedule_id FK
        uuid tenant_id FK
        date due_date
        decimal amount
        string currency
        datetime created_at
        datetime updated_at
    }

    RENT_REVIEW {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        date review_date
        string review_method
        datetime created_at
        datetime updated_at
    }

    LEASE_OPTION {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string option_type
        date exercise_deadline
        string description
        datetime created_at
        datetime updated_at
    }

    LEASE_CHARGE {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string charge_type
        string description
        decimal amount
        string currency
        string frequency
        datetime created_at
        datetime updated_at
    }

    DOCUMENT {
        uuid PK
        uuid lease_id FK
        uuid property_id FK
        uuid unit_id FK
        uuid tenant_id FK
        uuid document_category_id FK
        string title
        string description
        string file_name
        string mime_type
        decimal file_size
        string status
        datetime created_at
        datetime updated_at
    }

    DOCUMENT_VERSION {
        uuid PK
        uuid document_id FK
        uuid tenant_id FK
        int version_number
        string storage_path
        string checksum
        datetime created_at
    }

    DOCUMENT_CATEGORY {
        uuid PK
        uuid tenant_id FK
        string name
        string description
        datetime created_at
        datetime updated_at
    }

    DOCUMENT_STORAGE_REF {
        uuid PK
        uuid document_version_id FK
        uuid tenant_id FK
        string container_name
        string blob_name
        string sas_url_expiry
        datetime created_at
    }

    DOCUMENT_ACCESS_GRANT {
        uuid PK
        uuid document_id FK
        uuid user_id FK
        uuid tenant_id FK
        string access_level
        datetime granted_at
        datetime expires_at
    }

    KEY_DATE {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string date_type
        date due_date
        string description
        boolean is_active
        datetime created_at
        datetime updated_at
    }

    OBLIGATION {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string obligation_type
        string description
        date due_date
        string frequency
        string status
        datetime created_at
        datetime updated_at
    }

    REMINDER {
        uuid PK
        uuid key_date_id FK
        uuid obligation_id FK
        uuid tenant_id FK
        int days_before
        string description
        datetime created_at
        datetime updated_at
    }

    NOTIFICATION_DELIVERY {
        uuid PK
        uuid reminder_id FK
        uuid tenant_id FK
        uuid user_id FK
        string status
        datetime sent_at
        datetime created_at
    }

    TASK {
        uuid PK
        uuid lease_id FK
        uuid property_id FK
        uuid tenant_id FK
        string title
        string description
        string priority
        string status
        date due_date
        datetime created_at
        datetime updated_at
    }

    TASK_ASSIGNMENT {
        uuid PK
        uuid task_id FK
        uuid user_id FK
        uuid tenant_id FK
        datetime assigned_at
        datetime created_at
    }

    PAYMENT_SCHEDULE {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string schedule_type
        datetime created_at
        datetime updated_at
    }

    PAYMENT {
        uuid PK
        uuid payment_schedule_id FK
        uuid tenant_id FK
        date payment_date
        decimal amount
        string currency
        string status
        datetime created_at
        datetime updated_at
    }

    DISCOUNT_RATE_ASSUMPTION {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        decimal rate
        string rate_type
        date effective_date
        datetime created_at
        datetime updated_at
    }

    LEASE_MEASUREMENT {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string measurement_type
        date measurement_date
        decimal rou_asset_value
        decimal lease_liability_value
        string currency
        datetime created_at
    }

    RIGHT_OF_USE_ASSET {
        uuid PK
        uuid lease_measurement_id FK
        uuid tenant_id FK
        decimal initial_value
        decimal accumulated_amortization
        decimal carrying_amount
        datetime created_at
        datetime updated_at
    }

    LEASE_LIABILITY {
        uuid PK
        uuid lease_measurement_id FK
        uuid tenant_id FK
        decimal initial_value
        decimal interest_accrued
        decimal payments_made
        decimal carrying_amount
        datetime created_at
        datetime updated_at
    }

    JOURNAL_ENTRY {
        uuid PK
        uuid lease_measurement_id FK
        uuid accounting_period_id FK
        uuid tenant_id FK
        string entry_type
        date posting_date
        string description
        string status
        datetime created_at
    }

    JOURNAL_LINE {
        uuid PK
        uuid journal_entry_id FK
        uuid tenant_id FK
        string account_code
        string account_description
        decimal debit_amount
        decimal credit_amount
        string currency
        datetime created_at
    }

    ACCOUNTING_PERIOD {
        uuid PK
        uuid tenant_id FK
        string period_type
        date start_date
        date end_date
        string status
        datetime created_at
        datetime updated_at
    }

    REASSESSMENT {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string reassessment_type
        date reassessment_date
        string description
        datetime created_at
    }

    MODIFICATION {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string modification_type
        date effective_date
        string description
        datetime created_at
    }

    AUDIT_EVENT {
        uuid PK
        uuid tenant_id FK
        uuid user_id FK
        uuid lease_id FK
        string event_type
        string action
        string entity_type
        uuid entity_id
        jsonb changes
        datetime created_at
    }

    OUTBOX_MESSAGE {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string message_type
        jsonb payload
        string status
        datetime created_at
        datetime processed_at
    }

    IDEMPOTENCY_RECORD {
        uuid PK
        uuid lease_id FK
        uuid tenant_id FK
        string idempotency_key
        string request_hash
        datetime created_at
    }
```

## ERD Notes

### Primary Relationships

1. **Multi-tenancy**: All entities are scoped to a tenant via `tenant_id` foreign key
2. **Organizational hierarchy**: Tenant → Organisation → Portfolio → Property → Building → Unit
3. **Lease relationships**: Lease connects to Property, Unit, and Organisation
4. **Accounting flow**: Lease → Payment Schedule → Lease Measurement → Journal Entries
5. **Document management**: Documents can be linked to Lease, Property, or Unit
6. **Task assignment**: Tasks can be linked to Lease or Property, assigned to Users

### Key Design Decisions

- **Tenant isolation**: Every table includes `tenant_id` for multi-tenancy
- **Audit trail**: `created_at` and `updated_at` timestamps on all entities
- **Soft relationships**: Optional foreign keys (e.g., document can be linked to lease OR property)
- **Immutable records**: Document versions and audit events are immutable
- **Event sourcing**: Outbox pattern for reliable event publishing
- **Idempotency**: Records to prevent duplicate command execution
