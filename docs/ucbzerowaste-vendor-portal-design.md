# UCBZeroWaste Vendor Portal — Product & Technical Design (V1)

## 1) Goals

Design a vendor portal that allows UCBZeroWaste to:

1. Manage core vendor records (company profile, contacts, locations, service capabilities).
2. Track service and commodity coverage (what materials/vendors support by geography).
3. Manage pricing structures by vendor/service/commodity and effective dates.
4. Manage client-vendor relationships, including contracts and insurance documents.
5. Track invoice lifecycle, including vendor self-service uploads and semi-automated data entry.
6. Enable natural-language AI search across vendor, contract, coverage, and invoice data.

---

## 2) User Roles & Permissions

### Internal Roles

- **Admin**: Full control, user management, configuration, audit log access.
- **Operations**: Create/edit vendors, services, commodities, contracts, invoices.
- **Finance**: Invoice validation, status updates, export/payables integration.
- **Account Manager**: Client assignments, contract renewals, insurance tracking.
- **Read-Only Analyst**: Search/reporting and dashboard access.

### External Roles

- **Vendor User**: Access only to their organization’s profile, services, pricing submissions, invoices, and client contract files explicitly shared.

### Permission Model

Use role-based access control (RBAC) with row-level scoping:

- Vendor users can only access their own `vendor_id` data.
- Internal users can access all rows according to role capabilities.
- Sensitive files (contracts, COIs) can have additional policy tags (e.g., finance-only).

---

## 3) Core Modules

## 3.1 Vendor Information

**Purpose:** Single source of truth for each vendor.

**Fields (sample):**
- Legal name, DBA name, tax ID (encrypted), website
- HQ address + service region(s)
- Primary contacts (operations, billing, emergency)
- Certifications and licenses
- Compliance status
- Notes and tags

**Key features:**
- Vendor profile timeline (changes over time)
- Document attachments (W-9, permits)
- Duplicate detection for vendor records

## 3.2 Service Information

**Purpose:** Define service types each vendor offers.

**Examples:**
- Trash hauling
- Recycling pickup
- Organics collection
- Compactor service
- Brokerage/processing

**Fields:**
- Service type
- Service area (state/city/ZIP/polygon)
- Capacity constraints
- Frequency options
- SLA attributes

## 3.3 Commodity Information

**Purpose:** Track what materials vendors can handle.

**Examples:** cardboard, mixed paper, plastics, metals, organics, e-waste.

**Fields:**
- Commodity type and subtype
- Accepted contamination thresholds
- Required preparation/specs
- Destination facility/processing type
- Region supported

## 3.4 Pricing Information

**Purpose:** Store commercial terms and rate history.

**Pricing models supported:**
- Flat monthly service rate
- Per-haul charge
- Per-ton (or per-yard) commodity rate
- Fuel surcharge, environmental fees
- Tiered volume pricing

**Fields:**
- Vendor, service, commodity, geography
- Rate amount + currency + unit
- Effective start/end date
- Minimums/thresholds
- Contractual pricing vs spot pricing

## 3.5 Client Section (Client ↔ Vendor)

**Purpose:** Manage client-specific relationships and legal documents.

**Capabilities:**
- Link clients to one or more vendors.
- Maintain contract repository (PDF uploads + metadata).
- Track contract lifecycle:
  - start date
  - end date
  - renewal period
  - auto-renew flag
  - termination notice period
  - status (active/expiring/terminated)
- Store COI (Certificate of Insurance) per vendor per client:
  - policy type
  - insurer
  - coverage amount
  - expiration date
  - document upload

## 3.6 Invoice Module

**Purpose:** Full visibility into invoice status + vendor self-service intake.

**Invoice statuses (example):**
`uploaded -> parsing -> pending_review -> approved -> exported -> paid -> rejected`

**Vendor self-service flow:**
1. Vendor uploads invoice PDF/CSV.
2. OCR + extraction pre-fills fields (invoice number, client, date, amount, line items).
3. Vendor confirms/edits extracted fields.
4. Internal team reviews and approves.
5. Status updates visible to vendor.

**Internal controls:**
- Duplicate invoice detection
- Invoice-to-contract rate validation
- Missing-field validation rules
- Exception queue for manual review

---

## 4) Proposed Data Model (High-Level)

### Master Entities
- `vendors`
- `vendor_contacts`
- `services`
- `commodities`
- `vendor_service_coverage`
- `vendor_commodity_capability`
- `pricing_schedules`
- `clients`
- `client_vendor_links`

### Contract & Insurance Entities
- `contracts`
- `contract_terms` (renewal/termination metadata)
- `contract_documents`
- `insurance_certificates`

### Invoicing Entities
- `invoices`
- `invoice_line_items`
- `invoice_status_history`
- `invoice_documents`

### AI/Search Entities
- `search_index_documents`
- `embedding_vectors`
- `nl_query_logs`

### Shared Entities
- `files` (central object metadata)
- `audit_logs`
- `users`, `roles`, `permissions`

---

## 5) AI Query Experience

## 5.1 Example Questions

- “Give me all trash haulers in Los Angeles.”
- “I have cardboard in Kansas, what vendors can take this?”
- “Show me all contracts expiring in the next 6 months.”

## 5.2 AI Architecture Pattern

Use a **hybrid approach**:

1. **Structured query layer (SQL-first):**
   - For known entities/filters (city, commodity, date ranges), generate constrained SQL templates.
2. **Semantic retrieval layer (vector search):**
   - For document-heavy queries (contract clauses, insurance notes), search indexed text chunks.
3. **Response composer:**
   - Merge structured rows + semantic snippets into user-facing answer with links.

## 5.3 Guardrails

- No free-form SQL execution by user prompt.
- Use allow-listed query templates.
- Log query + generated filters for auditability.
- Enforce user-level permissions before returning results.

## 5.4 Suggested Output UX

Return:
- concise answer
- table preview (top records)
- filters used
- confidence/coverage note
- links to underlying records/documents

---

## 6) Recommended Tech Stack (Pragmatic)

- **Frontend:** React + TypeScript + component library (e.g., MUI)
- **Backend API:** Node.js (NestJS or Express) or Python (FastAPI)
- **Database:** PostgreSQL
- **File storage:** S3-compatible object store
- **Search:** PostgreSQL full text + vector extension (pgvector) or external vector DB
- **OCR/Extraction:** Document AI pipeline (e.g., Azure Form Recognizer, AWS Textract, or equivalent)
- **Auth:** SSO-ready identity provider (OIDC/SAML)
- **Background jobs:** Queue (e.g., BullMQ/Celery)

---

## 7) MVP Scope (Phase 1)

1. Vendor profiles, services, commodities, pricing CRUD.
2. Client-vendor linking with contract metadata and file uploads.
3. COI uploads and expiration tracking.
4. Invoice upload + status tracking + manual review screen.
5. AI query beta for:
   - vendor by service + location
   - vendor by commodity + location
   - contracts expiring within date range

**Out of scope for MVP:** full AP payment integration, advanced forecasting, custom BI builder.

---

## 8) Implementation Plan

## Phase A — Foundation (2–3 weeks)
- Finalize schema + RBAC policy matrix
- Set up file storage and signed URL strategy
- Build core admin screens for vendors/clients

## Phase B — Contracts & Insurance (2–3 weeks)
- Contract metadata + document module
- Renewal/termination rule tracking
- COI management + expiration alerts

## Phase C — Invoice Intake (3–4 weeks)
- Vendor portal authentication and upload UX
- OCR extraction + human review workflow
- Status timeline and notifications

## Phase D — AI Query Beta (2–3 weeks)
- Build NL-to-filter parser with templates
- Add semantic index for contracts/docs
- Ship query UI with saved searches

---

## 9) KPIs & Success Metrics

- % of invoices uploaded directly by vendors
- Invoice processing time (upload to approval)
- Contract renewal misses (target: 0)
- COI expiration compliance rate
- Query success rate (AI answer accepted without manual correction)

---

## 10) Risks & Mitigations

1. **Unstructured document quality (contracts/invoices).**
   - Mitigation: human-in-the-loop review, confidence thresholds.
2. **Data quality inconsistency (locations, commodity naming).**
   - Mitigation: controlled vocabularies and master data governance.
3. **AI hallucinations or unsafe query generation.**
   - Mitigation: template-driven query engine + strict authorization checks.
4. **Vendor adoption friction.**
   - Mitigation: simple upload UX and clear status transparency.

---

## 11) Next Decisions Needed

1. Confirm preferred stack (Node vs Python backend).
2. Define canonical commodity taxonomy.
3. Decide if pricing visibility is vendor-editable or internal-only.
4. Identify contract alert windows (e.g., 180/120/90 days).
5. Choose OCR provider based on sample invoice quality.

