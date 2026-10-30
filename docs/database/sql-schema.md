# SQL Schema Overview

## Database Design Principles

1. **Multi-tenancy**: All tables include `tenant_id` for data isolation
2. **Audit trails**: All tables include `created_at` and `updated_at` timestamps
3. **Soft deletes**: Consider using `deleted_at` for soft delete patterns where appropriate
4. **Optimistic concurrency**: Include `row_version` (timestamp) for concurrency control
5. **Indexes**: Strategic indexes on foreign keys, tenant_id, and frequently queried columns
6. **Constraints**: Foreign key constraints for referential integrity
7. **Data types**: Use appropriate data types (decimal for financial, uuid for IDs, date for dates)

## Key Tables

### Identity & Tenancy

```sql
CREATE TABLE tenants (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    name varchar(255) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE organisations (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    name varchar(255) NOT NULL,
    legal_name varchar(255),
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_organisation_tenant_name UNIQUE (tenant_id, name)
);

CREATE TABLE users (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    email varchar(255) NOT NULL UNIQUE,
    display_name varchar(255),
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE user_memberships (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id uuid NOT NULL REFERENCES users(id),
    organisation_id uuid NOT NULL REFERENCES organisations(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    role_id uuid NOT NULL REFERENCES roles(id),
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_user_org_tenant UNIQUE (user_id, organisation_id, tenant_id)
);

CREATE TABLE roles (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    name varchar(255) NOT NULL,
    description text,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_role_tenant_name UNIQUE (tenant_id, name)
);

CREATE TABLE permissions (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    code varchar(255) NOT NULL UNIQUE,
    description text
);

CREATE TABLE role_permissions (
    role_id uuid NOT NULL REFERENCES roles(id),
    permission_id uuid NOT NULL REFERENCES permissions(id),
    PRIMARY KEY (role_id, permission_id)
);
```

### Portfolio

```sql
CREATE TABLE portfolios (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    organisation_id uuid NOT NULL REFERENCES organisations(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    name varchar(255) NOT NULL,
    description text,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_portfolio_org_tenant_name UNIQUE (organisation_id, tenant_id, name)
);

CREATE TABLE properties (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    portfolio_id uuid NOT NULL REFERENCES portfolios(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    name varchar(255) NOT NULL,
    address text,
    city varchar(255),
    state varchar(255),
    postal_code varchar(50),
    country varchar(255),
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE buildings (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    property_id uuid NOT NULL REFERENCES properties(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    name varchar(255) NOT NULL,
    description text,
    total_area decimal(18, 2),
    area_unit varchar(50),
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE units (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    building_id uuid NOT NULL REFERENCES buildings(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    name varchar(255) NOT NULL,
    unit_number varchar(100),
    area decimal(18, 2),
    area_unit varchar(50),
    unit_type varchar(100),
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE ownership_interests (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    organisation_id uuid NOT NULL REFERENCES organisations(id),
    property_id uuid NOT NULL REFERENCES properties(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    ownership_percentage decimal(5, 2) NOT NULL,
    effective_from date NOT NULL,
    effective_to date,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

### Leases

```sql
CREATE TABLE leases (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    organisation_id uuid NOT NULL REFERENCES organisations(id),
    property_id uuid NOT NULL REFERENCES properties(id),
    unit_id uuid NOT NULL REFERENCES units(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    reference varchar(100) NOT NULL,
    status varchar(50) NOT NULL,
    start_date date NOT NULL,
    end_date date NOT NULL,
    base_rent decimal(18, 2) NOT NULL,
    currency varchar(3) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    row_version timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_lease_tenant_reference UNIQUE (tenant_id, reference)
);

CREATE TABLE lease_parties (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid NOT NULL REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    party_type varchar(50) NOT NULL,
    name varchar(255) NOT NULL,
    contact_email varchar(255),
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE lease_terms (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid NOT NULL REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    term_type varchar(100) NOT NULL,
    description text,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE lease_amendments (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid NOT NULL REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    amendment_type varchar(100) NOT NULL,
    description text,
    effective_date date NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE rent_schedules (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid NOT NULL REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    schedule_type varchar(50) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE rent_payments (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    rent_schedule_id uuid NOT NULL REFERENCES rent_schedules(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    due_date date NOT NULL,
    amount decimal(18, 2) NOT NULL,
    currency varchar(3) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

### Accounting

```sql
CREATE TABLE payment_schedules (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid NOT NULL REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    schedule_type varchar(50) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE payments (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    payment_schedule_id uuid NOT NULL REFERENCES payment_schedules(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    payment_date date NOT NULL,
    amount decimal(18, 2) NOT NULL,
    currency varchar(3) NOT NULL,
    status varchar(50) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE discount_rate_assumptions (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid NOT NULL REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    rate decimal(10, 6) NOT NULL,
    rate_type varchar(50) NOT NULL,
    effective_date date NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE lease_measurements (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid NOT NULL REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    measurement_type varchar(50) NOT NULL,
    measurement_date date NOT NULL,
    rou_asset_value decimal(18, 2) NOT NULL,
    lease_liability_value decimal(18, 2) NOT NULL,
    currency varchar(3) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE right_of_use_assets (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_measurement_id uuid NOT NULL REFERENCES lease_measurements(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    initial_value decimal(18, 2) NOT NULL,
    accumulated_amortization decimal(18, 2) NOT NULL DEFAULT 0,
    carrying_amount decimal(18, 2) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE lease_liabilities (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_measurement_id uuid NOT NULL REFERENCES lease_measurements(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    initial_value decimal(18, 2) NOT NULL,
    interest_accrued decimal(18, 2) NOT NULL DEFAULT 0,
    payments_made decimal(18, 2) NOT NULL DEFAULT 0,
    carrying_amount decimal(18, 2) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE journal_entries (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_measurement_id uuid REFERENCES lease_measurements(id),
    accounting_period_id uuid NOT NULL REFERENCES accounting_periods(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    entry_type varchar(50) NOT NULL,
    posting_date date NOT NULL,
    description text,
    status varchar(50) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE journal_lines (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    journal_entry_id uuid NOT NULL REFERENCES journal_entries(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    account_code varchar(50) NOT NULL,
    account_description varchar(255),
    debit_amount decimal(18, 2) NOT NULL DEFAULT 0,
    credit_amount decimal(18, 2) NOT NULL DEFAULT 0,
    currency varchar(3) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE accounting_periods (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    period_type varchar(50) NOT NULL,
    start_date date NOT NULL,
    end_date date NOT NULL,
    status varchar(50) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_tenant_period UNIQUE (tenant_id, period_type, start_date, end_date)
);
```

### Audit & Outbox

```sql
CREATE TABLE audit_events (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    user_id uuid REFERENCES users(id),
    lease_id uuid REFERENCES leases(id),
    event_type varchar(100) NOT NULL,
    action varchar(100) NOT NULL,
    entity_type varchar(100) NOT NULL,
    entity_id uuid,
    changes jsonb,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE outbox_messages (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    message_type varchar(255) NOT NULL,
    payload jsonb NOT NULL,
    status varchar(50) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    processed_at timestamp
);

CREATE TABLE idempotency_records (
    id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
    lease_id uuid REFERENCES leases(id),
    tenant_id uuid NOT NULL REFERENCES tenants(id),
    idempotency_key varchar(255) NOT NULL,
    request_hash varchar(255) NOT NULL,
    created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_tenant_idempotency_key UNIQUE (tenant_id, idempotency_key)
);
```

## Indexes

### Critical Indexes

```sql
-- Tenant scoping (all tables)
CREATE INDEX idx_tenants_id ON tenants(id);
CREATE INDEX idx_organisations_tenant_id ON organisations(tenant_id);
CREATE INDEX idx_users_id ON users(id);
CREATE INDEX idx_user_memberships_tenant_id ON user_memberships(tenant_id);
CREATE INDEX idx_user_memberships_user_id ON user_memberships(user_id);
CREATE INDEX idx_user_memberships_organisation_id ON user_memberships(organisation_id);

-- Portfolio queries
CREATE INDEX idx_portfolios_organisation_id ON portfolios(organisation_id);
CREATE INDEX idx_properties_portfolio_id ON properties(portfolio_id);
CREATE INDEX idx_buildings_property_id ON buildings(property_id);
CREATE INDEX idx_units_building_id ON units(building_id);

-- Lease queries
CREATE INDEX idx_leases_tenant_id ON leases(tenant_id);
CREATE INDEX idx_leases_property_id ON leases(property_id);
CREATE INDEX idx_leases_unit_id ON leases(unit_id);
CREATE INDEX idx_leases_status ON leases(status);
CREATE INDEX idx_leases_start_date ON leases(start_date);
CREATE INDEX idx_leases_end_date ON leases(end_date);

-- Accounting queries
CREATE INDEX idx_lease_measurements_lease_id ON lease_measurements(lease_id);
CREATE INDEX idx_journal_entries_accounting_period_id ON journal_entries(accounting_period_id);
CREATE INDEX idx_journal_lines_journal_entry_id ON journal_lines(journal_entry_id);

-- Audit queries
CREATE INDEX idx_audit_events_tenant_id ON audit_events(tenant_id);
CREATE INDEX idx_audit_events_user_id ON audit_events(user_id);
CREATE INDEX idx_audit_events_lease_id ON audit_events(lease_id);
CREATE INDEX idx_audit_events_created_at ON audit_events(created_at);

-- Outbox processing
CREATE INDEX idx_outbox_messages_status ON outbox_messages(status);
CREATE INDEX idx_outbox_messages_created_at ON outbox_messages(created_at);
```

## Data Types

| Type | Usage |
|------|-------|
| `uuid` | Primary keys and foreign keys |
| `varchar(n)` | Text fields with defined length |
| `text` | Unbounded text (descriptions) |
| `decimal(18, 2)` | Financial amounts (2 decimal places) |
| `decimal(10, 6)` | Interest rates (6 decimal places) |
| `date` | Dates without time |
| `timestamp` | Timestamps with timezone |
| `jsonb` | Flexible JSON data (changes, payload) |
| `boolean` | True/false flags |
