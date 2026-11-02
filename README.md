# KAMBAC Integrated Management System (KIMS)

KAMBAC Integrated Management System (KIMS) is a modular platform for managing members, loans, payments, livestock, equipment, livelihood products, attendance, announcements, analytics, and role-based access in one integrated system.

> [!NOTE]
> Assumptions:
> - The proposal names the system domains and tables but not all field-level schema, so added fields in the class and object diagrams are assumptions.
> - Loan and equipment lifecycle states not explicitly named in the proposal are modeled as assumptions.
> - Notification channels, dashboard drill-downs, and any detailed workflow logic beyond the stated scenarios are assumptions.
> - The architecture is modeled as a centralized web application with modular services and a REST API because the proposal describes layered and modular design rather than separate microservices.
> - The sample member ID format `KMB-2026-0001` is used only as an example object value.
> - The use case permissions are aligned to the intended RBAC model, but the detailed matrix cell values were not enumerated in the source text, so they are generalized.
> Mermaid has no native use case, activity, DFD, component, package, object, communication, interaction overview, composite structure or timing diagram, so those use flowcharts, class diagrams or a Gantt chart.

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
Layered KIMS architecture showing user roles, business modules, the REST API, RBAC middleware, database, notification module, and off-site backup.

```mermaid
flowchart LR
    subgraph Roles["User Roles"]
        P["President"]
        B["Bookkeeper"]
        G["General Manager"]
        O["Officers and Staff"]
        M["Members"]
    end

    subgraph Modules["KIMS Modules"]
        M1["Member Management"]
        M2["Loan and Payment Management"]
        M3["Livestock Management"]
        M4["Equipment Management"]
        M5["Livelihood Products"]
        M6["Attendance Management"]
        M7["Announcements and Notifications"]
        M8["Dashboard Analytics and Reporting"]
        M9["Role-Based Access Control and Security"]
    end

    A["Web Client"]
    API["REST API"]
    RBAC["RBAC Middleware"]
    DB["Database"]
    N["Notification Module"]
    AUD["Audit Log"]
    BAK["Off-Site Backup"]

    P --> M1
    B --> M2
    G --> M8
    O --> M3
    O --> M4
    O --> M5
    O --> M6
    O --> M7
    M --> M1
    M --> M2

    A --> M1
    A --> M2
    A --> M3
    A --> M4
    A --> M5
    A --> M6
    A --> M7
    A --> M8
    A --> M9

    M1 --> API
    M2 --> API
    M3 --> API
    M4 --> API
    M5 --> API
    M6 --> API
    M7 --> API
    M8 --> API
    M9 --> API

    API --> RBAC
    RBAC --> DB
    API --> N
    API --> AUD
    DB --> BAK
```

Note: This is a layered architecture view approximating a UML system architecture.

## Diagram 2: Class (Structural)
Core domain model for the master tables and transactional records, with sample class relationships for the proposal's management domains.

```mermaid
classDiagram
    class Member {
        +member_id: string
        +member_name: string
        +member_status: string
    }

    class Role {
        +role_id: string
        +role_name: string
        +permissions: string
    }

    class Loan {
        +loan_id: string
        +member_id: string
        +loan_amount: decimal
        +loan_balance: decimal
        +status: string
    }

    class Payment {
        +payment_id: string
        +loan_id: string
        +payment_amount: decimal
        +payment_date: date
    }

    class Livestock {
        +livestock_id: string
        +livestock_type: string
        +status: string
    }

    class Equipment {
        +equipment_id: string
        +equipment_name: string
        +status: string
    }

    class EquipmentBorrowing {
        +borrowing_id: string
        +equipment_id: string
        +member_id: string
        +borrowed_date: date
        +returned_date: date
        +status: string
    }

    class Product {
        +product_id: string
        +product_name: string
        +quantity: int
        +status: string
    }

    class Attendance {
        +attendance_id: string
        +member_id: string
        +attendance_date: date
        +status: string
    }

    class Announcement {
        +announcement_id: string
        +title: string
        +message: string
        +posted_date: date
    }

    class AuditLog {
        +log_id: string
        +user_id: string
        +action: string
        +event_time: datetime
        +details: string
    }

    Member "1" --> "0..*" Loan
    Loan "1" --> "0..*" Payment
    Member "1" --> "0..*" Attendance
    Member "1" --> "0..*" EquipmentBorrowing
    Equipment "1" --> "0..*" EquipmentBorrowing
    Role "1" --> "0..*" Member
    Livestock "1" --> "0..*" Member
    Product "1" --> "0..*" Announcement
    AuditLog "0..*" --> "1" Member
```

Note: This is a domain model approximation because the proposal does not provide full field-level schemas.

## Diagram 3: Component (Structural)
Application component view showing form validation, RBAC enforcement, business services, repository access, audit logging, and notifications.

```mermaid
flowchart TB
    subgraph UI["Presentation Layer"]
        FE["Web Forms and Dashboard"]
    end

    subgraph APP["Application Layer"]
        V["Validator"]
        RB["RBAC Checker"]
        S["Module Services"]
        R["Repository"]
        AL["Audit Logger"]
        N["Notifier"]
    end

    subgraph DATA["Data Layer"]
        DB["Database"]
        LOG["Audit Log"]
        MSG["Notification Gateway"]
    end

    FE --> V
    V --> RB
    RB --> S
    S --> R
    S --> AL
    S --> N
    R --> DB
    AL --> LOG
    N --> MSG
```

Note: This is an approximate UML component diagram using a flowchart.

## Diagram 4: Deployment (Structural)
Deployment view for the KIMS hosting environment, web server, application server, DBMS, and off-site backup infrastructure.

```mermaid
flowchart TB
    C["Client Devices"]
    WS["Web Server"]
    APP["Application Server"]
    API["REST API"]
    DB["DBMS"]
    FS["Backup Storage"]
    OFF["Off-Site Backup"]
    MON["Security Monitoring and Audit"]

    C --> WS
    WS --> APP
    APP --> API
    API --> DB
    API --> MON
    DB --> FS
    FS --> OFF
```

Note: This is an approximate UML deployment diagram using a flowchart.

## Diagram 5: Object (Structural)
Example object instances using the proposed master and transactional tables, with sample member ID values.

```mermaid
classDiagram
    class "Member: KMB-2026-0001" {
        member_id = "KMB-2026-0001"
        member_name = "Sample Member"
        member_status = "Active"
    }

    class "Role: Bookkeeper" {
        role_id = "ROL-02"
        role_name = "Bookkeeper"
        permissions = "loan approval, payment posting"
    }

    class "Loan: LN-2026-0001" {
        loan_id = "LN-2026-0001"
        member_id = "KMB-2026-0001"
        loan_amount = 15000.00
        loan_balance = 6500.00
        status = "Active"
    }

    class "Payment: PY-2026-0001" {
        payment_id = "PY-2026-0001"
        loan_id = "LN-2026-0001"
        payment_amount = 2500.00
        payment_date = "2026-02-05"
    }

    class "Livestock: LV-2026-0001" {
        livestock_id = "LV-2026-0001"
        livestock_type = "Carabao"
        status = "Assigned"
    }

    class "Equipment: EQ-2026-0001" {
        equipment_id = "EQ-2026-0001"
        equipment_name = "Tractor"
        status = "Available"
    }

    class "EquipmentBorrowing: EB-2026-0001" {
        borrowing_id = "EB-2026-0001"
        equipment_id = "EQ-2026-0001"
        member_id = "KMB-2026-0001"
        borrowed_date = "2026-02-07"
        returned_date = "2026-02-10"
        status = "Returned"
    }

    class "Product: PD-2026-0001" {
        product_id = "PD-2026-0001"
        product_name = "Rice Seed"
        quantity = 120
        status = "Available"
    }

    class "Attendance: AT-2026-0001" {
        attendance_id = "AT-2026-0001"
        member_id = "KMB-2026-0001"
        attendance_date = "2026-02-08"
        status = "Present"
    }

    class "Announcement: AN-2026-0001" {
        announcement_id = "AN-2026-0001"
        title = "General Assembly"
        message = "Schedule update"
        posted_date = "2026-02-06"
    }

    class "AuditLog: AD-2026-0001" {
        log_id = "AD-2026-0001"
        user_id = "KMB-2026-0001"
        action = "Loan approved"
        event_time = "2026-02-04T10:00:00"
        details = "Approval recorded"
    }
```

Note: This is an object-view approximation because Mermaid does not have a native object diagram in the simplified form used here.

## Diagram 6: Package (Structural)
Package decomposition for the KIMS presentation, application modules, security, and data layers.

```mermaid
flowchart TB
    subgraph P1["Presentation Layer"]
        UI["Web Portal and Dashboard"]
    end

    subgraph P2["Application Packages"]
        PM["Member Management"]
        LP["Loan and Payment Management"]
        LM["Livestock Management"]
        EM["Equipment Management"]
        PROD["Livelihood Products"]
        ATT["Attendance Management"]
        ANN["Announcements and Notifications"]
        DASH["Dashboard Analytics and Reporting"]
        SEC["Role-Based Access Control and Security"]
    end

    subgraph P3["Data and Audit Layer"]
        TBL["Master Tables"]
        LOG["Audit Log"]
    end

    UI --> PM
    UI --> LP
    UI --> LM
    UI --> EM
    UI --> PROD
    UI --> ATT
    UI --> ANN
    UI --> DASH
    UI --> SEC

    PM --> TBL
    LP --> TBL
    LM --> TBL
    EM --> TBL
    PROD --> TBL
    ATT --> TBL
    ANN --> TBL
    SEC --> LOG
```

Note: This is an approximate UML package diagram using subgraphs.

## Diagram 7: Composite Structure (Structural)
Internal architecture of the REST API and application layer showing validation, access control, business services, repository, auditing, and notification responsibilities.

```mermaid
flowchart LR
    subgraph API["REST API"]
        V["Request Validator"]
        RB["RBAC Checker"]
        S["Module Services"]
        R["Repository"]
        A["Audit Logger"]
        N["Notifier"]
    end

    C["Client Request"] --> V
    V --> RB
    RB --> S
    S --> R
    S --> A
    S --> N
    R --> DB["Database"]
    A --> AL["Audit Log"]
    N --> MSG["Notification Channel"]
```

Note: This is an approximate UML composite structure using a flowchart.

## Diagram 8: Data Flow Diagram Level 1 (Behavioral)
Level 1 DFD showing the main roles, the system processes, and the master tables used to persist KIMS records.

```mermaid
flowchart LR
    E1["President"]
    E2["Bookkeeper"]
    E3["General Manager"]
    E4["Officers and Staff"]
    E5["Members"]

    subgraph P["Processes"]
        P1["Member Management"]
        P2["Loan and Payment Management"]
        P3["Livestock Management"]
        P4["Equipment Management"]
        P5["Livelihood Products"]
        P6["Attendance Management"]
        P7["Announcements and Notifications"]
        P8["Dashboard Analytics and Reporting"]
        P9["Role-Based Access Control and Security"]
    end

    subgraph D["Data Stores"]
        D1["tbl_members"]
        D2["tbl_roles"]
        D3["tbl_equipment"]
        D4["tbl_livestock"]
        D5["tbl_products"]
        D6["Audit Log"]
    end

    E1 --> P1
    E2 --> P2
    E3 --> P8
    E4 --> P3
    E4 --> P4
    E4 --> P5
    E4 --> P6
    E4 --> P7
    E5 --> P1
    E5 --> P2

    P1 --> D1
    P2 --> D1
    P3 --> D4
    P4 --> D3
    P5 --> D5
    P7 --> D6
    P9 --> D2
    D1 --> P8
    D3 --> P8
    D4 --> P8
    D5 --> P8
```

Note: This is a data-flow style approximation, not a native DFD diagram.

## Diagram 9: Use Case (Behavioral)
Core user interactions for all proposal-defined roles and the workflow areas tied to the RBAC model.

```mermaid
flowchart LR
    subgraph Actors["Actors"]
        P["President"]
        B["Bookkeeper"]
        G["General Manager"]
        O["Officers and Staff"]
        M["Members"]
    end

    subgraph UseCases["Use Cases"]
        U1["Manage Members"]
        U2["Approve Loan"]
        U3["Record Payment"]
        U4["Manage Livestock"]
        U5["Borrow Equipment"]
        U6["Manage Products"]
        U7["Record Attendance"]
        U8["Publish Announcement"]
        U9["View Dashboard"]
        U10["Manage Access"]
        U11["Audit Security"]
    end

    P --> U1
    P --> U10
    B --> U2
    B --> U3
    G --> U9
    G --> U11
    O --> U4
    O --> U5
    O --> U6
    O --> U7
    O --> U8
    M --> U1
    M --> U3
    M --> U9
```

Note: This is a flowchart approximation of a UML use case diagram.

## Diagram 10: Activity (Behavioral)
Loan workflow showing member request, Bookkeeper approval, payment entry, and balance update.

```mermaid
flowchart TD
    S["Start"] --> A["Member files loan request"]
    A --> B["Bookkeeper reviews request"]
    B --> C{"RBAC permission valid?"}
    C -- Yes --> D["Approve loan"]
    D --> E["Record payment"]
    E --> F["Update loan balance"]
    F --> G["Audit log entry"]
    G --> H["End"]
    C -- No --> I["Deny access"]
    I --> G
```

Note: This is a flowchart approximation of a UML activity diagram.

## Diagram 11: Sequence (Behavioral)
Loan request and payment flow with an unauthorized access branch for a 403 Forbidden result.

```mermaid
sequenceDiagram
    actor Member
    actor Bookkeeper
    participant API as REST API
    participant RBAC as RBAC Checker
    participant LoanSvc as Loan and Payment Service
    participant DB as Database
    participant Audit as Audit Log

    Member->>API: Submit loan request
    API->>RBAC: Check role and permission
    alt Unauthorized request
        RBAC-->>API: 403 Forbidden
        API-->>Member: Return 403
        API->>Audit: Record denied access
    else Authorized
        RBAC-->>API: Granted
        API->>LoanSvc: Create loan record
        LoanSvc->>DB: Insert loan
        Bookkeeper->>API: Approve loan
        API->>RBAC: Validate Bookkeeper rights
        RBAC-->>API: Granted
        API->>LoanSvc: Approve loan
        LoanSvc->>DB: Update loan status
        Member->>API: Record payment
        API->>LoanSvc: Apply payment
        LoanSvc->>DB: Update payment and balance
        LoanSvc->>Audit: Write audit entry
        LoanSvc-->>Member: Return updated balance
    end
```

Note: This is a sequence diagram showing the proposal's loan scenario and RBAC denial path.

## Diagram 12: State Machine (Behavioral)
Loan lifecycle from application through approval, activity, payment, and closure, with assumption states included where the proposal does not define them explicitly.

```mermaid
stateDiagram-v2
    [*] --> Applied
    Applied --> Approved : Bookkeeper approves
    Applied --> Rejected : Bookkeeper denies
    Approved --> Active : Loan activated
    Active --> PartiallyPaid : Payment recorded
    PartiallyPaid --> Active : Additional payment posted
    Active --> FullyPaid : Balance reaches zero
    FullyPaid --> Closed : Final record stored
    Rejected --> [*]
    Closed --> [*]
```

Note: Assumption states include Rejected, PartiallyPaid, and Closed because the proposal only signals the overall loan lifecycle without naming each state.

## Diagram 13: Communication (Behavioral)
Message exchange for loan approval and payments between the member, Bookkeeper, services, database, and audit log.

```mermaid
flowchart LR
    M["Member"]
    B["Bookkeeper"]
    LS["Loan Service"]
    PS["Payment Service"]
    DB["Database"]
    AL["Audit Log"]

    M -->|"Loan request"| LS
    B -->|"Approve loan"| LS
    LS -->|"Update loan status"| DB
    M -->|"Payment record"| PS
    PS -->|"Update loan balance"| DB
    LS -->|"Audit event"| AL
    PS -->|"Audit event"| AL
```

Note: This is a communication-style flowchart approximation.

## Diagram 14: Interaction Overview (Behavioral)
Combined interaction view for the loan, livestock assignment, and equipment borrowing scenarios.

```mermaid
flowchart TB
    subgraph Loan["Loan Workflow"]
        L1["Member applies"]
        L2["Bookkeeper approves"]
        L3["Payment entered"]
        L4["Balance updated"]
    end

    subgraph Livestock["Livestock Workflow"]
        S1["Assign livestock"]
        S2["Track status"]
    end

    subgraph Equipment["Equipment Workflow"]
        E1["Borrow equipment"]
        E2["Return equipment"]
    end

    L1 --> L2 --> L3 --> L4
    L2 --> S1 --> S2
    L3 --> E1 --> E2
```

Note: This is an interaction-overview style synthesis of the proposal's main operational scenarios.

## Diagram 15: Timing (Behavioral)
Example timeline for a user session, loan balance progression, equipment status changes, and audit log events using a Gantt chart because Mermaid has no native timing diagram.

```mermaid
gantt
    title KIMS Example Timeline
    dateFormat  YYYY-MM-DD
    axisFormat %d %b

    section User Session
    Login and authorization      :a1, 2026-02-01, 1d
    Dashboard view               :a2, after a1, 1d
    Logout                       :a3, after a2, 1d

    section Loan Balance
    Applied                      :b1, 2026-02-03, 1d
    Approved                     :b2, after b1, 2d
    Payment applied              :b3, after b2, 1d
    Balance reduced              :b4, after b3, 1d

    section Equipment Item
    Available                   :c1, 2026-02-05, 2d
    Borrowed                    :c2, after c1, 3d
    Returned                    :c3, after c2, 1d

    section Audit Log
    Login event                 :d1, 2026-02-01, 1d
    Loan approval event         :d2, 2026-02-04, 1d
    Payment event               :d3, 2026-02-05, 1d
    Return event                :d4, 2026-02-08, 1d
```

Note: This is a Gantt chart approximation of a UML timing diagram.

## Diagram 16: Requirement (Behavioral)
Requirements traceability linking modules to requirements and to validation scenarios from the proposal.

```mermaid
requirementDiagram

    requirement "R1 Member data integrity" {
        id: 1
        text: The system shall maintain member records in the approved member table.
        risk: high
        verifymethod: test
    }

    requirement "R2 Loan and payment accuracy" {
        id: 2
        text: The system shall keep loan balances and payment records synchronized.
        risk: high
        verifymethod: test
    }

    requirement "R3 Livestock traceability" {
        id: 3
        text: The system shall maintain livestock records and assignment status.
        risk: medium
        verifymethod: inspection
    }

    requirement "R4 Equipment borrowing control" {
        id: 4
        text: The system shall track equipment borrowing and return status.
        risk: medium
        verifymethod: test
    }

    requirement "R5 Backup and recovery" {
        id: 5
        text: The system shall support backup, recovery, and continuity.
        risk: high
        verifymethod: test
    }

    requirement "R6 Security and audit" {
        id: 6
        text: The system shall enforce RBAC, encryption, and audit logging.
        risk: high
        verifymethod: inspection
    }

    element "Member Management" {}
    element "Loan and Payment Management" {}
    element "Livestock Management" {}
    element "Equipment Management" {}
    element "Backup and Recovery" {}
    element "Role-Based Access Control and Security" {}

    testCase "TC-01 Member registration" {}
    testCase "TC-02 Loan approval and payment" {}
    testCase "TC-03 Livestock assignment" {}
    testCase "TC-04 Equipment return" {}
    testCase "TC-05 Backup restore" {}
    testCase "TC-06 RBAC and audit log" {}

    "Member Management" -- satisfies --> "R1"
    "Loan and Payment Management" -- satisfies --> "R2"
    "Livestock Management" -- satisfies --> "R3"
    "Equipment Management" -- satisfies --> "R4"
    "Backup and Recovery" -- satisfies --> "R5"
    "Role-Based Access Control and Security" -- satisfies --> "R6"

    "TC-01 Member registration" -- verifies --> "R1"
    "TC-02 Loan approval and payment" -- verifies --> "R2"
    "TC-03 Livestock assignment" -- verifies --> "R3"
    "TC-04 Equipment return" -- verifies --> "R4"
    "TC-05 Backup restore" -- verifies --> "R5"
    "TC-06 RBAC and audit log" -- verifies --> "R6"
```

Note: This is a requirement-traceability diagram approximating a UML requirement model.

