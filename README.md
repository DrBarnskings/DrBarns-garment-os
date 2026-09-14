SYSTEM ROLE & GOAL:
You are an expert Principal Full-Stack Engineer and System Architect.
I am providing you with the exact HTML, CSS, and SVG design assets for "DRB GARMENTS" — a digital operating system for Nigerian fashion houses, tailoring businesses, and garment factories.

Your task is to take these UI layout files (cloned/extracted from my GitHub repository), preserve 100% of the visual design system, and wire up all real backend functionality, routing, state management, Firebase authentication, Firestore multi-tenant database integration, and Role-Based Access Control (RBAC).

----------------------------------------------------------------------
1. DESIGN & ASSET INGESTION RULES
----------------------------------------------------------------------
- Use the provided HTML structure, CSS rules, and embedded SVG icons directly as the primary presentation layer. Do not replace the visual theme with generic components.
- Retain the exact visual identity: Soft lavender/purple backgrounds (#F8F6FF), white rounded cards, deep navy typography (#0F172A), circular badges, purple gradient buttons, and Naira (₦) formatting.
- Convert static HTML templates into dynamic, reusable React/Next.js (or Vite + TypeScript) components where applicable.
- All SVG icons provided in the repo must be mapped to their corresponding buttons, navigational elements, and status indicators.

----------------------------------------------------------------------
2. TECH STACK & INFRASTRUCTURE CONFIGURATION
----------------------------------------------------------------------
- Frontend Framework: React / Next.js (TypeScript) + Tailwind CSS (matching the provided CSS rules).
- Authentication: Firebase Auth (Google OAuth + Email/Password).
- Database: Cloud Firestore (Strict multi-tenant architecture isolated by `facilityId`).
- Storage: Firebase Storage (For facility logos, client garment reference photos, and style uploads).
- Primary Currency: Nigerian Naira (₦). Ensure all financial values format as ₦X,XXX,XXX.00.

----------------------------------------------------------------------
3. ROLE-BASED ACCESS CONTROL (RBAC) & ROUTING GATEWAY
----------------------------------------------------------------------
Implement a strict Role-Based Routing Gateway upon user authentication (`/auth/login`):

1. LEAD GENERAL ADMIN (`role: 'LEAD_ADMIN'`):
   - Redirects to `/admin/dashboard`.
   - Access to platform-wide operational health, facility monitoring profiles, monthly return ledgers, and pause/reinstate facility controls.

2. FACILITY OWNER (`role: 'FACILITY_OWNER'`):
   - Redirects to `/facility/dashboard`.
   - Full commercial & operational control over their specific facility workspace (e.g., Bani Couture).
   - Full visibility into Revenue, Material Purchases, Opex, Profit, Staff Management, and Settings.

3. FACILITY MANAGER (`role: 'FACILITY_MANAGER'`):
   - Access to production pipelines, customer measurements, job assignments, inventory, purchases, and operational reporting.
   - Restricted from changing owner-level account settings or altering facility ownership.

4. RECEPTIONIST (`role: 'RECEPTIONIST'`):
   - Access to `/facility/clients`, customer onboarding, creating measurement profiles, creating job orders, and issuing invoices.
   - Blocked from seeing business financial ledgers, net profit summaries, or staff management.

5. LINE SEWER / CUTTER (`role: 'LINE_SEWER'` or `role: 'CUTTER'`):
   - Optimized mobile-first view `/facility/tailor-queue`.
   - Can ONLY view assigned jobs, accept/decline work tasks, update job stages (e.g., move from `SEWING` to `IRONING_FINISHING`), and upload quality check photos.

----------------------------------------------------------------------
4. CORE WORKFLOW FUNCTIONALITY TO IMPLEMENT
----------------------------------------------------------------------

WORKFLOW A: Customer Reception & Non-Destructive Measurements
- Record new customers or search existing client directory.
- Version-Controlled Measurements: When a client's measurements change, NEVER overwrite previous entries. Save as `Measurement Version 1`, `Version 2`, etc. Allow jobs to select specific versions.

WORKFLOW B: Production State Machine & Garment Lifecycle
- Map job card transitions across all 10 stages:
  `RECEPTION` ➔ `MEASUREMENTS` ➔ `JOB_CREATED` ➔ `MATERIALS_ASSIGNED` ➔ `CUTTING` ➔ `SEWING` ➔ `IRONING_FINISHING` ➔ `QUALITY_CONTROL` ➔ `READY_FOR_PICKUP` ➔ `DELIVERED`
- Auto-generate unique Job IDs (e.g., `JOB-2026-0142`).
- Track tailor assignments (Pending, Accepted, Declined, Reassigned).

WORKFLOW C: Financials & Money Movement Ledger
- Explicitly separate business financial terms:
  * MONEY GENERATED (Revenue): Invoices, client deposits, final payments.
  * MONEY MOVED OUT (Purchases & Expenses): Fabric spools, linings, buttons, trims, generator diesel, workshop rent, transport.
  * NET OPERATING RESULT: `Revenue - Purchases - Operating Expenses = Net Profit`.
- Include a financial transaction ledger with full date, supplier, and payment method filtering (Cash, Bank Transfer, POS).

WORKFLOW D: Lead Admin Command Center & Facility Lifecycle
- Monitor all onboarded facilities across health states: `CURRENT` (Green), `DUE SOON` (Amber), `OVERDUE` (Red), `PAUSED`.
- Monthly Return Ledger: Tracks billable transaction usage per facility (e.g., ₦50 per generated invoice) and remittance status.
- Sophisticated Pause Action: When the Lead Admin pauses a facility (due to overdue returns):
  * Do NOT delete data.
  * Display a paused banner to the facility owner.
  * Lock operational actions (block new job creation, invoicing, staff onboarding).
  * Preserve Read-Only access so the business never loses historical client records, past measurements, or tax reports.

----------------------------------------------------------------------
5. FIRESTORE DATABASE SCHEMA TO BIND
----------------------------------------------------------------------
Bind the dynamic HTML inputs to the following Firestore collections:

1. `facilities/{facilityId}` -> Holds facility metadata, owner UID, status (`ACTIVE`, `PAUSED`).
2. `users/{uid}` -> Stores `facilityId`, `role`, `displayName`, `email`, `isActive`.
3. `facilities/{facilityId}/jobs/{jobId}` -> Job status, assigned tailor, style reference URLs, payment status.
4. `facilities/{facilityId}/clients/{clientId}` -> Contact details, measurement versions array.
5. `facilities/{facilityId}/ledger/{transactionId}` -> Financial income/expense/purchase entries.
6. `platform_returns/{returnId}` -> Monthly returns ledger for Lead Admin revenue control.
7. `admin_audit_logs/{logId}` -> Logs all administrative actions (`PAUSE_FACILITY`, `REINSTATE_FACILITY`, `RECORD_PAYMENT`).

----------------------------------------------------------------------
6. OUTPUT EXPECTATION
----------------------------------------------------------------------
1. Parse all provided HTML/CSS/SVG templates from the repo.
2. Output clean, modular React/TypeScript code structured into components, route pages, context providers, and Firebase utility hooks.
3. Include real Firebase authorization rules and route guards preventing cross-tenant data leaks between facilities.
4. Provide full, working state handling for forms, table filters, workflow progression buttons, and modal dialogs.
