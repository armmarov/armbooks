# ArmBooks — Requirements Doc (v1)

**Prepared by:** ba-lead
**Date:** 2026-09-27
**Status:** APPROVED by biz-head (2026-09-27). biz-head is reporting this + bd-lead's
research to ceo next, per the owner/ceo's ask for visibility on this new-scope initiative.
biz-head owns the dev-head handoff from here — **do not send to dev-head.**
**Project ref:** `proj-1a38e23d` in `../../projects.json` (status `idle`, no team — correct
for requirements phase; the entry's `cwd` field is wrong, a repo URL not a local path —
flagged as a dev-head data-quality fix, not touched here)
**Inputs:** bd-lead's competitive/regulatory research (`../../bd/bd-lead/research/armbooks-competitive-analysis.md`),
biz-head's segment steer and UI-outsourcing constraint (2026-09-27)

---

## 1. What ArmBooks is

A cloud-native Malaysian SME accounting product competing with Bukku and SQL Account,
with LHDN e-Invoice/MyInvois compliance as the core differentiator. New project — not a
CR against an existing product.

## 2. Target segment (v1 GTM only — not an architecture constraint)

**v1 positioning and feature prioritization target the RM3m–RM100m annual-turnover band.**
This is a **business/GTM decision by biz-head**, based on bd-lead's research:
- This band is under **active LHDN enforcement today** (cross-referencing e-invoice
  submissions against declared income has already surfaced RM3.5B in unreported income
  and caught 108 non-compliant taxpayers in an April 2026 sweep). The sub-RM3m band was
  just exempted until 2028 (threshold raised to RM3m, effective 1 Sep 2026) and faces no
  comparable urgency yet.
- This band is **SQL Account's stronghold**, and SQL has real, evidenced weaknesses to
  exploit: desktop-with-cloud-veneer architecture (same Firebird DB, not a rearchitected
  multi-tenant SaaS backend), functionality fragmented across 5+ separate SKUs
  (payroll/POS/e-commerce sold separately, synced by file export/import), e-invoice
  compliance *patches* gated behind an opaque annual support-renewal fee, and conflicting
  reseller pricing.

**Constraint this places on engineering (binding):** the compliance engine and core data
model (chart of accounts, ledger, tax rules, e-Invoice submission pipeline) **must not be
architected in any way that excludes or would later need rework to serve sub-RM3m
businesses.** Segment-specific behavior (if any) belongs in configuration/business rules,
not in the schema or service boundaries. The RM3m–RM100m focus governs what ships first
and how it's marketed — not what the system is capable of representing.

## 3. Scoping constraint: UI/UX is outsourced (new, owner-level)

Unlike ArmLyst/ArmGrow, **ArmBooks' UI/UX will be designed by a third-party agency**, not
built in-house via Figma by our own dev team. This reshapes how this doc is written and
must be carried into dev-head's handoff:

- **Backend/business-logic scope** (this doc's actual subject): accounting engine,
  ledger/chart-of-accounts data model, LHDN e-Invoice compliance rules and submission
  flow, SST logic, integrations (banking, e-commerce where in scope), payroll (phase 2),
  API surface.
- **UI/visual-design scope**: explicitly out of this doc. No screens, layouts, wireframes,
  or component specs are defined here.
- **Acceptance criteria are written in functional/system-behavior terms** throughout
  (e.g. "system displays e-Invoice submission status within 5s of receiving the LHDN
  response" rather than describing a screen), so a third-party design agency picking this
  up later has an unambiguous functional contract to design against, without this doc
  presuming our own dev team drives the visual design process.
- biz-head will flag this same constraint to dev-head directly so dev-head doesn't plan
  an in-house design phase for this project — this doc should read cleanly to whoever
  ends up handling that handoff, without depending on that conversation having happened.

## 4. Source confidence — carry this distinction into build, don't flatten it

Per bd-lead's report, not everything below carries equal certainty. Two tiers:

**Primary-confirmed** (from hasil.gov.my / live product pages, safe to build against directly):
- The mandatory rollout schedule by turnover band (§6 table).
- The RM3m exemption threshold change (1 Sep 2026) and Phase 4 relaxation extension to
  31 Dec 2027.
- Core technical format: UBL 2.1, JSON/XML, XAdES digital signature (SHA-256/RSA) via an
  approved Malaysian CA, submission via MyInvois Portal or API, 72-hour cancellation
  window, no formal certification scheme (Intermediary registration is sufficient).
- Both incumbents' core feature/pricing/architecture facts in bd-lead's report (each
  carries its own source URL).

**Secondary-sourced — flagged by bd-lead as "spot-check against hasil.gov.my immediately
before build, not settled fact"**:
- Exact mandatory field count in the e-invoice schema.
- Exact current consolidation-exclusion list (which transaction types can't use
  consolidated/monthly e-invoicing).
- Exact self-billed-invoice scenario list.
- Exact PDF-sourced penalty text (Section 120(1)(d) figures are corroborated by multiple
  secondary sources but not pulled from primary PDF text).
- The anti-fragmentation carve-out on the RM3m exemption (related-company/shareholder/JV
  aggregation rule).
- Whether Bukku/SQL Account's Peppol/PRSP accreditation claims are genuine (MDEC's
  provider list was unreachable during research) — **not relevant to ArmBooks' own scope;
  see §9.2 for the resolved Peppol decision.**

**Action for dev-head/whoever builds the e-Invoice module:** re-verify the secondary-sourced
items against `hasil.gov.my` directly before finalizing the compliance rule engine — LHDN
guidance has changed twice in the last month alone as of this report.

## 5. Phasing

**Phase 1 (MVP — must ship together, product isn't usable without all four):**
1. Core bookkeeping/ledger
2. Invoicing
3. e-Invoice/MyInvois submission, scoped to the RM3m–RM100m band's actual current
   requirements
4. SST filing support

**Phase 2 (differentiators, ranked by bd-lead's gap analysis — build after MVP is live):**
1. Live multi-bank feed automation beyond Maybank
2. Unified real-time payroll (EPF/SOCSO/EIS/PCB, live-synced to the GL)
3. Form C / income-tax (CP204) prep
4. Compliance-status-visibility dashboard
5. Native e-commerce sync (Shopee/Lazada/EasyStore/Shopify/WooCommerce)
6. Review/trust-building — **this is a marketing/BD action item (case studies, review-
   platform presence), not a dev-head engineering item; noted here for completeness only,
   owned by sales-lead/bd-lead going forward, not scoped further in this doc.**

---

## 6. Phase 1 — Feature requirements & acceptance criteria

### 6.1 Core bookkeeping / ledger

**Requirements:**
- Double-entry ledger with a configurable chart of accounts (Malaysian SME defaults,
  editable per tenant).
- Multi-tenant data isolation (each business's ledger fully isolated — this is a
  non-negotiable given both incumbents and prior ArmBot products already build to this
  standard).
- Real-time trial balance, P&L, balance sheet generation from ledger state (no
  batch/nightly recompute dependency).
- Append-only audit trail on every ledger-affecting transaction (who, when, what changed) —
  both incumbents have this; matches the compliance-trust positioning in §2.
- Fixed-asset register (acquisition, depreciation schedule, disposal) — table stakes
  against Bukku.
- Multi-currency support as a **core capability, not a paid add-on** (direct wedge vs.
  Bukku, which gates this below its Prime tier — see §2 positioning).

**Acceptance criteria:**
- [ ] A journal entry posted to the ledger updates trial balance, P&L, and balance sheet
      views without requiring a separate recalculation step or delay.
- [ ] Every ledger-affecting change is recorded in an audit log entry that is immutable
      (no update/delete path exists on audit log rows) and includes actor, timestamp, and
      before/after state.
- [ ] Chart of accounts can be customized per tenant without a code deployment.
- [ ] A transaction can be recorded in a currency other than the tenant's base currency,
      with conversion applied at a defined rate source, at every pricing tier — not gated
      behind a paid add-on.
- [ ] Two tenants' ledger data cannot be queried or returned to each other under any
      authenticated session, verified by an isolation test per tenant boundary.

### 6.2 Invoicing

**Requirements:**
- Standard invoice creation, recurring invoice scheduling, credit/debit notes.
- Invoice status lifecycle (draft → sent → paid/overdue → cancelled) independent of the
  e-Invoice submission lifecycle (§6.3) — these are related but distinct state machines;
  an invoice can exist and be sent to a customer before/independently of its LHDN
  validation state.
- Multi-channel send (email at minimum; WhatsApp send is a Phase 2 nice-to-have, not MVP —
  both incumbents have it, but it's not compliance-critical).

**Acceptance criteria:**
- [ ] An invoice can be created, sent, and marked paid without depending on e-Invoice
      submission having completed (the two flows must not be hard-coupled).
- [ ] Recurring invoices generate automatically on their configured schedule without
      manual re-entry.
- [ ] A credit or debit note can reference and adjust a specific prior invoice.

### 6.3 e-Invoice / MyInvois submission (RM3m–RM100m band scope)

**Requirements** (per bd-lead's report §3, primary-confirmed items only — see §4 above
for what still needs a pre-build spot-check):
- Generate a UBL 2.1-based e-invoice document (JSON or XML) from ledger/invoice data.
- Apply XAdES digital signature (SHA-256/RSA) via an approved Malaysian CA certificate
  before submission.
- Submit via API integration using the LHDN Intermediary-registration model (the same
  integration path both Bukku and SQL Account use — no separate certification scheme
  exists for this path).
- Handle the full validation round-trip: submit → receive LHDN validation (UUID + QR
  code) → notify both transacting parties of validation result.
- Support standard invoices, credit notes, debit notes, refund notes, and self-billed
  invoices as distinct e-invoice sub-types.
- Enforce the 72-hour post-validation cancellation window: after validation, cancellation
  is only available to the issuer (or buyer, for self-billed docs) within 72 hours; after
  that window, the system must route corrections through a credit/debit/refund note
  instead of allowing cancellation.
- Support consolidated (monthly, B2C-leaning) e-invoicing, **except** for the excluded
  transaction-type list and the RM10,000 single-transaction threshold — any single
  transaction over RM10,000 requires an individual e-invoice even during any relaxation
  period. (Exact exclusion list is secondary-sourced — see §4; system must be built so
  this list is configurable, not hardcoded, since it may change again before build.)
- Support self-billed e-invoicing for the required scenarios (payments to agents/dealers/
  distributors, foreign suppliers, specified e-commerce/gaming payouts). (Exact scenario
  list is secondary-sourced — same configurability requirement applies.)
- Compliance rules (exclusion lists, thresholds, penalty references, enforcement-phase
  dates) must be **externally configurable**, not hardcoded — this segment's compliance
  requirements have changed twice in the last month per bd-lead's report and will keep
  moving.

**Explicitly out of scope for v1 (confirmed, §9.2):** MDEC Peppol/PRSP accreditation.
Neither incumbent could be confirmed to actually hold this (see §4), it is not required
for the Intermediary-registration integration path, and pursuing it is a heavier, audited
process. Do not schedule this speculatively — revisit only if a specific customer within
the RM3m–RM100m band explicitly needs Peppol interoperability post-launch (e.g. a large
trading/export account); that's a sales-driven trigger, not a roadmap item.

**Phase 1 groundwork for the Phase 2 compliance-status dashboard (§9.4, §7 item 4):** the
data/API layer for the dashboard — current submission/validation status, cancellation-
window countdown data, audit-trail hooks — is built now, alongside the rest of this
module, even though the surfaced dashboard UI/feature isn't exposed until Phase 2.

**Acceptance criteria:**
- [ ] Given a finalized invoice, the system generates a UBL 2.1-conformant e-invoice
      document and digitally signs it (XAdES, SHA-256/RSA) before submission.
- [ ] Given a signed e-invoice, the system submits it via the LHDN API and receives and
      stores the resulting UUID and QR code on successful validation.
- [ ] The system displays (functionally — via an API/data field the eventual UI can
      surface) the current MyInvois submission/validation status of an invoice, updated
      within 5 seconds of receiving LHDN's response.
- [ ] Given a validated e-invoice within 72 hours of validation, the issuer (or buyer, for
      self-billed) can cancel it; given a validated e-invoice past 72 hours, cancellation
      is rejected and the system requires a credit/debit/refund note instead.
- [ ] Given a single transaction over RM10,000, the system rejects/blocks consolidated
      e-invoice inclusion and requires an individual e-invoice, regardless of relaxation-
      period status.
- [ ] Given a transaction type on the (configurable) consolidated-exclusion list, the
      system requires an individual e-invoice rather than allowing consolidation.
- [ ] Given one of the (configurable) self-billed scenarios, the system generates a
      self-billed e-invoice rather than a standard one.
- [ ] All exclusion lists, thresholds, and enforcement-phase dates referenced above are
      stored as configuration data editable without a code deployment.
- [ ] The current MyInvois submission/validation status, remaining time in the 72-hour
      cancellation window, and relevant audit-trail events for a given e-invoice are all
      retrievable via API (data/API groundwork for the Phase 2 compliance-status
      dashboard, per §9.4 — no dashboard UI is built in Phase 1).

### 6.4 SST filing support

**Requirements:**
- SST-liable transactions mapped to SST-02 return fields.
- **Resolved (§9.1): MVP ships a reconciled report only, not direct-submission API
  integration.** Reasoning: unlike LHDN, bd-lead's report never established whether RMCD
  offers a comparable API/e-filing path for SST at all — committing to "submission-ready"
  for MVP would be scoping against an unverified capability, not a confirmed one. Whether
  to build direct submission is deferred to a Phase 2 investigation (§7 item 6).

**Acceptance criteria:**
- [ ] SST-registrable transactions are automatically flagged and mapped to their
      corresponding SST-02 return field.
- [ ] A bimonthly SST-02 report can be generated for a given filing period, reconciled
      against the ledger for that period, in a form ready for manual filing into RMCD's
      portal.
- [ ] No SST-02 direct-submission/API integration is built in Phase 1.

---

## 7. Phase 2 — Differentiator requirements (scope only, not detailed acceptance criteria yet)

To be detailed in a follow-up requirements pass once Phase 1 is underway. Scope notes for
planning purposes:

1. **Live multi-bank feeds beyond Maybank** — CIMB, Public Bank, RHB, Hong Leong, AmBank
   at minimum (the banks bd-lead's research found both incumbents lack live feeds for).
2. **Unified real-time payroll** — EPF/SOCSO/EIS/PCB statutory filing, live-synced to the
   GL as a native module, not a separate product with file-based sync (the gap in both
   incumbents).
3. **Form C / CP204 prep** — neither incumbent produces a submission-ready tax
   computation package; this would be a genuine step beyond both.
4. **Compliance-status-visibility dashboard** — real-time MyInvois submission/validation
   status surfaced proactively, alerts before the 72-hour cancellation window closes,
   audit-trail highlighting. **The surfaced UI/feature ships Phase 2, but per biz-head's
   decision (§9.4) the underlying data/API layer — submission/validation status,
   cancellation-window countdown data, audit-trail hooks — is built in Phase 1 alongside
   §6.3's e-Invoice module** (cheap now, expensive to retrofit; consistent with the
   API-first NFR in §8). See §6.3 for the Phase 1 acceptance criteria this adds.
5. **Native e-commerce sync** — Shopee/Lazada/EasyStore/Shopify/WooCommerce, without a
   paid middleware dependency (unlike Bukku's PayRecon requirement).
6. **RMCD SST direct-submission investigation** — per biz-head's decision (§9.1), research
   whether RMCD offers an API/e-filing path for SST-02 analogous to LHDN's MyInvois before
   committing to build direct submission. MVP (§6.4) ships a reconciled report only; this
   item decides whether a future phase adds direct submission.

---

## 8. Non-functional requirements

- **Architecture:** genuinely cloud-native, multi-tenant SaaS (explicit contrast with
  SQL Account's desktop-database-with-cloud-veneer approach) — no local server/NAS
  dependency for any customer.
- **Segment-agnostic data model:** per §2, nothing in the schema, ledger model, or
  compliance engine may assume or require RM3m+ turnover. Segment targeting lives in
  GTM/config, not architecture.
- **Compliance configurability:** per §6.3, all LHDN-derived thresholds, exclusion lists,
  and enforcement dates must be configuration, not code, given how fast this regulatory
  area has moved (two changes in one month per bd-lead's report).
- **API-first:** given UI is outsourced (§3), backend functionality must be fully
  exercisable via API independent of any first-party UI — this is also required for the
  third-party design agency's eventual frontend to consume.

## 9. Resolved decisions (biz-head, 2026-09-27)

These were raised as open questions in the reviewed draft; biz-head resolved all four
before approving this doc. Baked into the relevant sections above; recorded here as the
decision log.

1. **SST-02 output (§6.4):** MVP ships a reconciled report only, not a direct-submission
   API integration. bd-lead's report never established whether RMCD has an API/e-filing
   path analogous to MyInvois — committing to "submission-ready" would scope against an
   unverified capability. Added as a Phase 2 investigation item (§7 item 6): research
   whether RMCD offers such a path, then decide on direct submission.
2. **Peppol accreditation (§6.3):** confirmed out of v1, as already defaulted. Do not
   pursue MDEC Peppol/PRSP accreditation now or schedule it speculatively. Revisit only if
   a specific customer within the RM3m–RM100m band explicitly needs Peppol
   interoperability post-launch (e.g. a large trading/export account) — a sales-driven
   trigger, not a roadmap item.
3. **Payroll timing (§5, §7 item 2):** confirmed stays Phase 2, not pulled forward. The
   acute compliance surface is e-Invoice — that's what LHDN's enforcement sweeps actually
   cross-reference, not payroll. Pulling payroll into MVP would delay the ledger/e-Invoice
   core for a compliance angle that isn't the urgent one.
4. **Compliance-status dashboard groundwork (§6.3, §7 item 4):** build the data/API layer
   (submission/validation status, cancellation-window countdown data, audit-trail hooks)
   alongside Phase 1's e-Invoice module — cheap now, expensive to retrofit, consistent
   with the API-first NFR (§8). No dashboard UI work is implied or built in Phase 1.

---

## 10. Explicitly out of scope (v1)

- Sub-RM3m micro-SME feature prioritization or positioning (architecture stays
  compatible per §2, but nothing is built specifically for this segment in v1).
- Any UI/visual design work — owned by the third-party agency once engaged (§3).
- Payroll, Form C/CP204 prep, live multi-bank feeds beyond Maybank, e-commerce sync,
  compliance-status dashboard UI (all Phase 2, §7). Only the dashboard's underlying
  data/API layer is built in Phase 1 (§9.4) — not the surfaced feature.
- SST-02 direct-submission/API integration (§6.4) — reconciled report only in v1; direct
  submission is gated on the Phase 2 RMCD-API investigation (§7 item 6, §9.1).
- MDEC Peppol/PRSP accreditation (§6.3, §9.2) — confirmed out of v1, revisit only on a
  sales-driven trigger.
- Review/trust-building activities (marketing-owned, not an engineering scope item).

---

## Sources

Full citations and per-claim confidence notes: `../../bd/bd-lead/research/armbooks-competitive-analysis.md`
