# KAMBAC Integrated Management System (KIMS)

KAMBAC Integrated Management System (KIMS) is the proposed integrated platform for managing member records, loans and payments, livestock assignments, equipment borrowing, product inventory, attendance, announcements, reporting, and security/audit processes across the KAMBAC organization.

> [!NOTE]
> Assumptions used for this README:
> - The proposal defines the modules, roles, master tables, data flows, and security controls but does not provide full field-level schemas for every table, so the class/object diagrams include representative attributes only.
> - Loan lifecycle states not explicitly named in the proposal are modeled as assumptions: Applied, Approved, Active, Partially Paid, Fully Paid, Rejected, and Overdue.
> - Equipment borrowing states are represented as Available, Borrowed, and Returned for the timing diagram, as a general UML-style lifecycle simplification.
> - The proposal names a layered, modular RESTful architecture; this README treats it as a single web application with modular services rather than a microservice topology.
> - The user roles are President, Bookkeeper, General Manager, Officers and Staff, and Members, as stated in Section VI.
> - The master tables referenced in Section XI are treated consistently as tbl_members, tbl_roles, tbl_equipment, tbl_livestock, and tbl_products.
> - The proposal guidance for loan, livestock, and equipment workflows is translated into simplified example scenarios for the behavioral diagrams.
>
> "Mermaid has no native use case, activity, DFD, component, package, object, communication, interaction overview, or timing diagrams. These are approximated using flowchart, sequence, class, state, requirement, and gantt diagrams in this README."

## Contents

| No. | Diagram | Type |
| --- | --- | --- |
| 1 | System Architecture | Structural |
| 2 | Class | Structural |
| 3 | Component | Structural |
| 4 | Deployment | Structural |
| 5 | Object | Structural |
| 6 | Package | Structural |
| 7 | Composite Structure | Structural |
| 8 | Data Flow Diagram Level 1 | Behavioral |
| 9 | Use Case | Behavioral |
| 10 | Activity | Behavioral |
| 11 | Sequence | Behavioral |
| 12 | State Machine | Behavioral |
| 13 | Communication | Behavioral |
| 14 | Interaction Overview | Behavioral |
| 15 | Timing | Behavioral |
| 16 | Requirement | Behavioral |

## Diagram 1: System Architecture (Structural)
Layered KIMS architecture showing core modules, REST API, RBAC middleware, database, notifications, audit, and off-site backup.

```mermaid
flowchart LR
    U["President<br/>Bookkeeper<br/>General Manager<br/>Officers and Staff<br/>Members"] --> W["Web Client"]
    W --> API["REST API"]
    API --> RBAC["RBAC Middleware"]
    RBAC --> M1["Member Management"]
    RBAC --> M2["Loan and Payment Management"]
    RBAC --> M3["Livestock Management"]
    RBAC --> M4["Equipment Management"]
    RBAC --> M5["Product / Inventory Management"]
    RBAC --> M6["Attendance / Announcements"]
    M1 --> DAO["Repository / Data Access"]
    M2 --> DAO
    M3 --> DAO
    M4 --> DAO
    M5 --> DAO
    M6 --> DAO
    DAO --> DB["Relational DBMS"]
    DB --> TBL["Master Tables"]
    API --> AUD["Audit Logger"]
    API --> NOTIF["Notifier"]
    NOTIF --> MSG["Email / SMS / Alerts"]
    DB --> BAK["Backup Storage"]
    BAK --> OFF["Off-Site Backup"]
    API --> MON["Security Monitoring"]
```

Note: This is a simplified architecture view derived from the layered and modular design described in Sections VIII, X, XII, and XV.

## Diagram 2: Class (Structural)
Domain model for the core entities and relationships among members, roles, loans, payments, livestock, equipment, products, attendance, announcements, and audit records.

```mermaid
classDiagram
    class Role {
        +role_id
        +role_name
        +permissions
    }

    class Member {
        +member_id
        +full_name
        +status
        +contact_info
        +create_member()
        +update_member()
    }

    class Loan {
        +loan_id
        +member_id
        +amount
        +balance
        +status
        +apply_loan()
        +approve_loan()
        +update_balance()
    }

    class Payment {
        +payment_id
        +loan_id
        +amount
        +payment_date
        +record_payment()
    }

    class Livestock {
        +livestock_id
        +tag_no
        +type
        +status
        +assign_livestock()
    }

    class Equipment {
        +equipment_id
        +name
        +serial_no
        +status
        +borrow_item()
        +return_item()
    }

    class EquipmentBorrowing {
        +borrowing_id
        +equipment_id
        +member_id
        +borrowed_date
        +due_date
        +return_date
        +status
    }

    class Product {
        +product_id
        +product_name
        +category
        +stock_on_hand
        +update_stock()
    }

    class Attendance {
        +attendance_id
        +member_id
        +date
        +status
        +record_attendance()
    }

    class Announcement {
        +announcement_id
        +title
        +message
        +posted_by
        +publish_announcement()
    }

    class AuditLog {
        +audit_id
        +actor
        +event_type
        +timestamp
        +details
        +write_audit()
    }

    Role "1" --> "many" Member
    Member "1" --> "many" Loan
    Loan "1" --> "many" Payment
    Member "1" --> "many" EquipmentBorrowing
    Equipment "1" --> "many" EquipmentBorrowing
    Member "1" --> "many" Attendance
    Member "1" --> "many" Announcement
    Member "1" --> "many" AuditLog
    Loan "1" --> "many" AuditLog
```

Note: This class model is based on the data model and master tables described in Sections VI and XI.

## Diagram 3: Component (Structural)
Logical components of the KIMS application layer and data-access path.

```mermaid
flowchart TB
    UI["Web Portal"] --> V["Validator"]
    V --> RBAC["RBAC Checker"]
    RBAC --> S1["Member Service"]
    RBAC --> S2["Loan Service"]
    RBAC --> S3["Livestock Service"]
    RBAC --> S4["Equipment Service"]
    RBAC --> S5["Product Service"]
    S1 --> REPO["Repository"]
    S2 --> REPO
    S3 --> REPO
    S4 --> REPO
    S5 --> REPO
    REPO --> DB["DBMS"]
    S1 --> AUD["Audit Logger"]
    S2 --> AUD
    S3 --> AUD
    S4 --> AUD
    S5 --> AUD
    S2 --> NOTIF["Notifier"]
    AUD --> ALOG["Audit Log"]
    NOTIF --> MSG["Alerts"]
```

Note: This flowchart approximates the modular application architecture described in Sections VIII and X.

## Diagram 4: Deployment (Structural)
Deployment view of the KIMS hosting environment, web server, DBMS, backup infrastructure, and monitoring.

```mermaid
flowchart LR
    C["Client Device"] --> WS["Web Server"]
    WS --> APP["Application Server"]
    APP --> API["REST API"]
    API --> DB["DBMS"]
    DB --> BAK["Backup Storage"]
    BAK --> OFF["Off-site Backup"]
    API --> MON["Security Monitoring"]
    API --> NOTIF["Notification Service"]
    NOTIF --> EMAIL["Email / SMS / Alerts"]
```

Note: This deployment model reflects the hosting and infrastructure guidance in Sections XII and XV.

## Diagram 5: Object (Structural)
Representative object instances for key KIMS records using sample values.

```mermaid
classDiagram
    class "Member: M-1001" {
        member_id = "M-1001"
        full_name = "Sample Member"
        status = "Active"
    }

    class "Role: Bookkeeper" {
        role_name = "Bookkeeper"
        permissions = "Loan approval, payment posting"
    }

    class "Loan: L-2201" {
        loan_id = "L-2201"
        member_id = "M-1001"
        amount = 25000
        balance = 6500
        status = "Approved"
    }

    class "Payment: P-3301" {
        payment_id = "P-3301"
        loan_id = "L-2201"
        amount = 5000
        payment_date = "2026-11-02"
    }

    class "Livestock: LV-01" {
        livestock_id = "LV-01"
        tag_no = "K001"
        type = "Carabao"
        status = "Assigned"
    }

    class "Equipment: EQ-42" {
        equipment_id = "EQ-42"
        name = "Tractor"
        status = "Available"
    }

    class "EquipmentBorrowing: EB-01" {
        borrowing_id = "EB-01"
        equipment_id = "EQ-42"
        member_id = "M-1001"
        status = "Borrowed"
    }

    class "Product: PR-18" {
        product_id = "PR-18"
        product_name = "Rice Seed"
        stock_on_hand = 120
    }

    class "Attendance: AT-01" {
        attendance_id = "AT-01"
        member_id = "M-1001"
        date = "2026-11-02"
        status = "Present"
    }

    class "AuditLog: AD-01" {
        audit_id = "AD-01"
        actor = "Bookkeeper"
        event_type = "Loan Approved"
        timestamp = "2026-11-02T09:30:00"
    }
```

Note: This is a representative object instance diagram derived from the data model in Sections VI and XI.

## Diagram 6: Package (Structural)
High-level package grouping across modules, security, and data persistence.

```mermaid
flowchart TB
    subgraph P1["Presentation"]
        UI["Web Portal"]
    end

    subgraph P2["Business Modules"]
        PM["Member Management"]
        LP["Loan and Payment Management"]
        LM["Livestock Management"]
        EM["Equipment Management"]
        PROD["Product / Inventory Management"]
        ATT["Attendance"]
        ANN["Announcements"]
    end

    subgraph P3["Cross-cutting Services"]
        SEC["RBAC and Security"]
        AUD["Audit and Logs"]
        NOTIF["Notification Module"]
    end

    subgraph P4["Data Layer"]
        TBL["Master Tables"]
    end

    UI --> PM
    UI --> LP
    UI --> LM
    UI --> EM
    UI --> PROD
    UI --> ATT
    UI --> ANN
    PM --> TBL
    LP --> TBL
    LM --> TBL
    EM --> TBL
    PROD --> TBL
    ATT --> TBL
    ANN --> TBL
    SEC --> AUD
    SEC --> TBL
```

Note: This package grouping approximates the modular design described in Sections VIII and X.

## Diagram 7: Composite Structure (Structural)
Internal composition of the application layer and REST API with validation, RBAC, repositories, notifier, and audit.

```mermaid
flowchart LR
    API["REST API"] --> V["Validator"]
    API --> R["RBAC Checker"]
    API --> S["Module Services"]
    S --> M1["Member Service"]
    S --> M2["Loan Service"]
    S --> M3["Livestock Service"]
    S --> M4["Equipment Service"]
    S --> M5["Product Service"]
    M1 --> REPO["Repository"]
    M2 --> REPO
    M3 --> REPO
    M4 --> REPO
    M5 --> REPO
    REPO --> DB["DBMS"]
    API --> AUD["Audit Logger"]
    API --> NOTIF["Notifier"]
    AUD --> ALOG["Audit Log"]
    NOTIF --> MSG["Messages / Alerts"]
```

Note: This is a simplified composite structure based on the internal application-layer design in Sections VIII and X.

## Diagram 8: Data Flow Diagram Level 1 (Behavioral)
External roles interact with the main modules, which persist data in the master tables and audit log.

```mermaid
flowchart LR
    E1["President"] --> P1["Member Management"]
    E2["Bookkeeper"] --> P2["Loan and Payment Management"]
    E3["General Manager"] --> P3["Livestock Management"]
    E4["Officers and Staff"] --> P4["Equipment Management"]
    E5["Members"] --> P5["Member / Loan Access"]

    P1 --> D1["tbl_members"]
    P1 --> D2["tbl_roles"]
    P2 --> D3["Loan and Payment Records"]
    P3 --> D4["tbl_livestock"]
    P4 --> D5["tbl_equipment"]
    P5 --> D6["Audit Log"]
    D1 --> A["Validation and Synchronization"]
    D2 --> A
    D3 --> A
    D4 --> A
    D5 --> A
```

Note: This broad DFD approximation follows Section XIV for form-to-database flow and Sections XI and XIII for tables and audit/security processing.

## Diagram 9: Use Case (Behavioral)
User roles and the main use cases associated with member management, rentals, livestock, equipment, attendance, and security administration.

```mermaid
flowchart LR
    P["President"] --> U1["Review reports"]
    P --> U2["Manage access policies"]

    B["Bookkeeper"] --> U3["Manage member records"]
    B --> U4["Approve loan"]
    B --> U5["Record payment"]

    G["General Manager"] --> U6["Review operational status"]
    G --> U7["Assign livestock"]

    O["Officers and Staff"] --> U8["Record attendance"]
    O --> U9["Manage equipment borrowing"]
    O --> U10["Publish announcements"]

    M["Members"] --> U11["View member profile"]
    M --> U12["Apply for loan"]
    M --> U13["View payment status"]
```

Note: This approximates the role actors and use-case areas defined in Sections VI and XIII.

## Diagram 10: Activity (Behavioral)
Loan workflow from application through approval, payment entry, balance update, and audit logging.

```mermaid
flowchart TD
    A["Member submits loan request"] --> B["Validate member and policy"]
    B --> C{"Eligible?"}
    C -- No --> D["Reject or request correction"]
    C -- Yes --> E["Bookkeeper reviews"]
    E --> F{"Approved?"}
    F -- No --> G["Return for revision or deny"]
    F -- Yes --> H["Create active loan record"]
    H --> I["Member makes payment"]
    I --> J["Record payment entry"]
    J --> K["Update outstanding balance"]
    K --> L{"Balance fully paid?"}
    L -- No --> M["Continue monitoring loan"]
    L -- Yes --> N["Close loan and log audit"]
```

Note: This follows the loan workflow described in Section XVII and the form-to-database flow in Section XIV.

## Diagram 11: Sequence (Behavioral)
Sequence for the primary loan scenario: member request, bookkeeper approval, payment posting, balance change, and audit log entry.

```mermaid
sequenceDiagram
    autonumber
    actor M as Member
    participant UI as Web Portal
    participant API as REST API
    participant S as Loan Service
    participant R as Repository
    participant DB as DBMS
    participant B as Bookkeeper
    participant L as Audit Logger

    M->>UI: Submit loan request
    UI->>API: POST /loans
    API->>S: Validate member and loan data
    S->>R: Save request
    R->>DB: Insert loan record
    B->>API: Review loan request
    API->>S: Approve loan
    S->>R: Update loan status
    R->>DB: Commit approval
    M->>API: Post payment
    API->>S: Apply payment
    S->>R: Update balance
    R->>DB: Commit payment
    S->>L: Write audit event
    L-->>API: Log recorded
    API-->>UI: Return updated status and balance
```

Note: This is based on the loan scenario, approval logic, and balance update guidance in Sections XIV and XVII.

## Diagram 12: State Machine (Behavioral)
Loan lifecycle states and transitions through approval, payment, and closure.

```mermaid
stateDiagram-v2
    [*] --> Applied
    Applied --> Approved: Bookkeeper approves
    Applied --> Rejected: Validation or policy failure
    Approved --> Active: Loan activated
    Active --> PartiallyPaid: Payment posted
    PartiallyPaid --> Active: More payment posted
    Active --> FullyPaid: Balance reaches zero
    PartiallyPaid --> FullyPaid: Final payment posted
    Active --> Overdue: Payment due beyond threshold
    Overdue --> Active: Payment or reschedule updates
    FullyPaid --> [*]
    Rejected --> [*]
```

Note: The proposal does not name every state; the additional states are assumptions added for readability and completeness.

## Diagram 13: Communication (Behavioral)
Representative message exchange among the member, Bookkeeper, services, database, and audit log for the loan and payment process.

```mermaid
flowchart LR
    M["Member"] -->|"Loan request"| LS["Loan Service"]
    B["Bookkeeper"] -->|"Approve request"| LS
    LS -->|"Update loan status"| DB["Database"]
    M -->|"Post payment"| PS["Payment Service"]
    PS -->|"Update balance"| DB
    LS -->|"Audit event"| AL["Audit Log"]
    PS -->|"Audit event"| AL
```

Note: This communication view expresses the message paths described in the loan workflow and security/audit guidance.

## Diagram 14: Interaction Overview (Behavioral)
Combined interaction patterns for the loan, livestock assignment, and equipment borrowing scenarios.

```mermaid
sequenceDiagram
    autonumber
    participant M as Member
    participant B as Bookkeeper
    participant GM as General Manager
    participant LS as Livestock Service
    participant ES as Equipment Service
    participant DB as DBMS

    par Loan Workflow
        M->>B: File loan request
        B->>DB: Validate and approve
        DB-->>M: Loan status updated
    and Livestock Workflow
        GM->>LS: Assign livestock record
        LS->>DB: Update tbl_livestock
    and Equipment Workflow
        M->>ES: Request equipment borrowing
        ES->>DB: Create borrowing record
    end
```

Note: This combines the proposal’s loan, livestock assignment, and equipment borrowing examples into a single overview.

## Diagram 15: Timing (Behavioral)
Example timeline for a user session, loan balance, equipment state, and audit log entries using a Gantt chart.

```mermaid
gantt
    title KIMS Example Timeline
    dateFormat  YYYY-MM-DD
    axisFormat %b %d

    section User Session
    Login and validation      :a1, 2026-11-01, 1d
    View dashboard            :a2, after a1, 1d
    Submit loan request       :a3, after a2, 1d
    Logout                    :a4, after a3, 1d

    section Loan Balance
    Loan applied              :b1, 2026-11-01, 1d
    Approved and active       :b2, after b1, 3d
    Partial payment           :b3, after b2, 2d
    Fully paid                :b4, after b3, 1d

    section Equipment Item
    Available                 :c1, 2026-11-01, 2d
    Borrowed                 :c2, after c1, 3d
    Returned                 :c3, after c2, 1d

    section Audit Log
    Login event               :d1, 2026-11-01, 1d
    Loan approval logged      :d2, after d1, 1d
    Payment posted            :d3, after d2, 2d
    Return logged             :d4, after d3, 1d
```

Note: Mermaid has no native timing diagram, so this Gantt chart is used as the closest equivalent to the proposal’s example timeline requirement.

## Diagram 16: Requirement (Behavioral)
Requirements traceability linking security, master-data integrity, transactional processing, and recovery controls to business modules and validation scenarios.

```mermaid
requirementDiagram
    requirement R1 {
      id: 1
      text: "Member records and role assignments must remain valid and traceable."
    }

    requirement R2 {
      id: 2
      text: "Loan applications and payment postings must be approved and synchronized with balances."
    }

    requirement R3 {
      id: 3
      text: "Livestock records must track assignments and operational status."
    }

    requirement R4 {
      id: 4
      text: "Equipment borrowing and return must be tracked and controlled."
    }

    requirement R5 {
      id: 5
      text: "Products and inventory must be tracked and updated consistently."
    }

    requirement R6 {
      id: 6
      text: "RBAC, encryption, audit logs, and backup/recovery must protect operations and support recovery."
    }

    element MemberModule
    element LoanModule
    element LivestockModule
    element EquipmentModule
    element ProductModule
    element SecurityModule

    testCase TC01
    testCase TC02
    testCase TC03
    testCase TC04
    testCase TC05
    testCase TC06

    R1 - satisfies -> MemberModule
    R2 - satisfies -> LoanModule
    R3 - satisfies -> LivestockModule
    R4 - satisfies -> EquipmentModule
    R5 - satisfies -> ProductModule
    R6 - satisfies -> SecurityModule

    R1 - verifies -> TC01
    R2 - verifies -> TC02
    R3 - verifies -> TC03
    R4 - verifies -> TC04
    R5 - verifies -> TC05
    R6 - verifies -> TC06
```

Note: This requirement diagram is derived from the scope, master-data, security, and recovery guidance in Sections V, VI, XI, XIII, and XIX.
