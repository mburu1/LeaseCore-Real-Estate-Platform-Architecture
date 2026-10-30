# Domain Glossary

## Portfolio Terms

| Term | Definition |
|------|------------|
| **Organisation** | A legal entity that owns or manages properties and leases. Can be a landlord, property management company, or tenant organization. |
| **Portfolio** | A collection of properties and assets managed by an organization for investment or operational purposes. |
| **Property** | A distinct real estate asset, typically identified by address or legal description. Can contain multiple buildings. |
| **Building** | A physical structure on a property. Can contain multiple rentable units. |
| **Unit** | A rentable space within a building (e.g., office suite, retail space, warehouse). |
| **Ownership Interest** | The percentage or share of ownership an organization has in a property or portfolio. |

## Lease Terms

| Term | Definition |
|------|------------|
| **Lease** | A contractual agreement granting the right to use a property for a specified period in exchange for rent. |
| **Lease Party** | An entity involved in a lease agreement (landlord, tenant, guarantor, etc.). |
| **Lease Term** | The duration of a lease agreement, defined by start and end dates. |
| **Lease Amendment** | A formal change to the terms of an existing lease agreement. |
| **Rent Schedule** | The schedule of rent payments, including amounts, due dates, and escalation clauses. |
| **Rent Review** | A provision allowing rent to be reviewed and potentially adjusted during the lease term. |
| **Lease Option** | A contractual right for a tenant to extend, terminate, or purchase the leased property. |
| **Break Clause** | A provision allowing either party to terminate the lease before the end date under specified conditions. |
| **Lease Reference** | A unique identifier for a lease within an organization. |

## Document Terms

| Term | Definition |
|------|------------|
| **Document** | A file associated with a lease, property, or business process, with metadata for classification and retrieval. |
| **Document Version** | A specific iteration of a document, with immutable content and version history. |
| **Document Category** | A classification for documents (e.g., lease agreement, insurance certificate, inspection report). |
| **Storage Reference** | A pointer to the physical location of a document file in blob storage. |

## Obligation Terms

| Term | Definition |
|------|------------|
| **Key Date** | A significant date associated with a lease (e.g., expiry, rent review, insurance renewal). |
| **Obligation** | A contractual duty or requirement arising from a lease agreement. |
| **Notice Window** | The period before a key date during which notice must be given. |
| **Reminder** | A notification triggered before a key date or obligation deadline. |
| **Recurring Obligation** | An obligation that repeats on a schedule (e.g., monthly maintenance, annual inspection). |

## Work Management Terms

| Term | Definition |
|------|------------|
| **Task** | A unit of work assigned to a user, with a due date, priority, and status. |
| **Task Assignment** | The allocation of a task to a specific user or role. |
| **Priority** | The importance or urgency level of a task (e.g., low, medium, high, critical). |
| **Escalation** | The process of raising a task to a higher level of authority if not completed by its due date. |

## Accounting Terms

| Term | Definition |
|------|------------|
| **Payment Schedule** | The detailed schedule of lease payments, including timing and amounts. |
| **Discount Rate** | The interest rate used to calculate the present value of future lease payments. |
| **Lease Measurement** | The calculation of lease asset and liability values under accounting standards (e.g., IFRS 16). |
| **Right-of-Use (ROU) Asset** | The asset recognized on the balance sheet representing the right to use a leased asset. |
| **Lease Liability** | The liability recognized on the balance sheet representing the obligation to make lease payments. |
| **Journal Entry** | An accounting record capturing a financial transaction with debits and credits. |
| **Accounting Period** | A time frame for financial reporting (e.g., monthly, quarterly, annual). |
| **Reassessment** | The process of re-evaluating lease assumptions and measurements. |
| **Modification** | A change to lease terms that requires remeasurement of assets and liabilities. |
| **Present Value** | The current value of future cash flows, discounted at an appropriate rate. |
| **Amortization** | The systematic allocation of the cost of an asset over its useful life. |

## Identity & Tenancy Terms

| Term | Definition |
|------|------------|
| **Tenant** | An organization using the LeaseCore platform, with isolated data and users. |
| **User** | An individual who accesses the platform, with authentication credentials. |
| **Role** | A collection of permissions granted to users (e.g., Property Manager, Accountant, Viewer). |
| **Permission** | A specific right to perform an action or access a resource. |
| **Membership** | The association of a user with a tenant, including roles and permissions. |

## Audit Terms

| Term | Definition |
|------|------------|
| **Audit Event** | A record of a significant action or change in the system, for compliance and traceability. |
| **Security Event** | An audit event related to authentication, authorization, or access control. |
| **Business Event** | An audit event related to business transactions (e.g., lease creation, amendment). |

## Technical Terms

| Term | Definition |
|------|------------|
| **Aggregate** | A cluster of domain objects treated as a single unit for data changes. |
| **Value Object** | An immutable object defined by its attributes rather than identity. |
| **Domain Event** | An event that occurs within the domain and triggers side effects. |
| **Bounded Context** | A distinct part of the domain logic with clear boundaries. |
| **CQRS** | Command Query Responsibility Segregation - separating read and write operations. |
| **Outbox** | A pattern for reliably publishing domain events to external systems. |
| **Idempotency** | The property of an operation that can be applied multiple times without changing the result. |
