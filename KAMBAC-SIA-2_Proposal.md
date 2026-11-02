Use the file #file:docs/KAMBAC-SIA-2_Proposal.md as the ONLY source of truth. Read it fully before writing anything. Then create a file named README.md in the repository root that contains 16 diagrams written as Mermaid code blocks (```mermaid) so GitHub renders them natively. Do not use images or external tools.

SCOPE
Model the whole KAMBAC Integrated Management System (KIMS), covering all in-scope modules in the proposal: Member Management, Loan and Payment Management, Livestock Management, Equipment Management (inventory and borrowing), Livelihood Products, Attendance Management, Announcements and Notifications, Dashboard Analytics and Reporting, and Role-Based Access Control and Security. Take every fact from the proposal. Do not invent features. Where the proposal is silent, label the item as an assumption.

WHERE TO FIND THE FACTS
- Project overview, modules, user roles, data covered: Sections II and VI.
- Current manual workflows: Section VII.
- Layers, modules, RESTful API, modular design: Sections VIII and X.
- Master tables (tbl_members, tbl_roles, tbl_equipment, tbl_livestock, tbl_products), data standards, validation, synchronization: Section XI.
- Hosting, web server, DBMS, backup infrastructure: Section XII.
- RBAC matrix, encryption, API security, audit logs, security monitoring: Section XIII.
- Step-by-step data flow from form to database: Section XIV.
- Hardware, software and technology stack: Section XV.
- Testing scenarios: Section XVII.
- Backup and recovery: Section XIX.

THE 16 DIAGRAMS (in this order; each under the heading "## Diagram N: Name (Structural)" or "(Behavioral)" with a one-line description)
Structural: 1 System Architecture, 2 Class, 3 Component, 4 Deployment, 5 Object, 6 Package, 7 Composite Structure.
Behavioral: 8 Data Flow Diagram Level 1, 9 Use Case, 10 Activity, 11 Sequence, 12 State Machine, 13 Communication, 14 Interaction Overview, 15 Timing, 16 Requirement.

DIAGRAM GUIDANCE
- System Architecture, Component, Deployment, Package: draw what Sections VIII, X, XII and XV describe, showing all modules, the REST API, RBAC middleware, database, notification module and off-site backup.
- Class and Object: base them on Section XI and the data lists in Section VI. Include Member, Role, Loan, Payment, Livestock, Equipment, EquipmentBorrowing, Product, Attendance, Announcement and AuditLog. Use only attributes the proposal names; mark any added table, key or field as an assumption. For the object diagram use clearly marked sample data (member ID format KMB-2026-0001).
- Composite Structure: show the internal parts of the REST API and application layer (validator, RBAC checker, module services, repository, audit logger, notifier).
- Use Case: actors are the roles in Section VI (President, Bookkeeper, General Manager, Officers and Staff, Members). Permissions must match every row of the RBAC matrix in Section XIII.
- DFD Level 1: external entities are the five roles; processes are the main modules; data stores are the master tables and the audit log.
- Activity, Sequence, Communication: use the loan scenario from Section XVII (member files a loan, Bookkeeper approves, payment entry, balance update) and the workflow in Section XIV. The sequence diagram must include an alt branch for an unauthorized request (403) based on the RBAC matrix.
- Interaction Overview: link the loan scenario, the livestock assignment scenario and the equipment borrowing scenario.
- State Machine: show the lifecycle of a loan (for example Applied, Approved, Active, Fully Paid). Label every state the proposal does not name as an assumption.
- Timing: show an example timeline for a user session, a loan balance, an equipment item (Available, Borrowed, Returned) and the audit log. Mermaid has no timing diagram, so use a gantt chart and note this.
- Requirement: derive requirements from Sections V, VI, XI, XIII and XIX; link each to a module with "satisfies" and to a test case with "verifies" (see Section XVII).

README STRUCTURE
1. Title, short project description, and a notes box that lists all assumptions plus this sentence: "Mermaid has no native use case, activity, DFD, component, package, object, communication, interaction overview, composite structure or timing diagram, so those use flowcharts, class diagrams or a Gantt chart."
2. A contents table (number, diagram, Structural/Behavioral).
3. The 16 diagrams, with one short note under any diagram that approximates a UML type.

MERMAID RULES
- Use native types where they exist (classDiagram, sequenceDiagram, stateDiagram-v2, requirementDiagram, gantt, flowchart).
- Put every label that contains parentheses, slashes, ampersands, colons or special characters in double quotes. Use <br/> for line breaks. Avoid semicolons and # in labels.
- Node IDs must be simple (letters, digits, underscores) and unique within each diagram.
- Keep each diagram under about 30 nodes. If a diagram would be larger, group modules into subgraphs instead of drawing every detail.
- Use consistent names across diagrams.
- Every block must be valid Mermaid that GitHub can render. If unsure about a syntax feature, choose the simpler form.

OUTPUT
Create or overwrite README.md only. After writing it, list every fact you took from the proposal with its section number, and every assumption you made.
