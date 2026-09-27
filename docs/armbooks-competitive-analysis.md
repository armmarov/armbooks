# ArmBooks — Competitive & Regulatory Research

**Prepared by:** bd-lead
**Date:** 2026-09-27
**Requested by:** biz-head, on behalf of the owner
**Purpose:** Scope a new Malaysian SME accounting product ("ArmBooks") to compete with Bukku and SQL Account, with LHDN e-Invoice/MyInvois compliance as the priority angle. This is a new-scope initiative, not a CR against an existing product. Next step after this report: hand off to ba-lead to turn into a phased requirements doc/CR for dev-head, with biz-head visibility first.

All findings below are from live web research conducted 2026-09-27 (three parallel research passes: Bukku, SQL Account, official LHDN/MyInvois requirements). Every factual claim in the underlying research carries a source URL — this document summarizes and synthesizes; see the "Confidence notes" under each section for what could not be independently verified, and treat anything not source-linked in the original passes as directional, not fact.

---

## 1. Bukku (bukku.my)

**Company:** Nukleus Ventures Sdn Bhd, KL-based, founded 2019.

**Feature set:** Cloud-only (browser-based, app.bukku.my). Invoicing/sales (recurring invoices, auto late-interest invoices, WhatsApp/email send with open-tracking, online "Pay Now" buttons), full bookkeeping (real-time dashboard, 50+ auto reports, fixed-asset register, audit trail, "Digital Shoebox" AP receipt capture), inventory (perpetual costing, UOMs, bundles, consignment — but single-location below Prime tier, described by third parties as "standardized"/shallow vs. SQL), multi-currency (paid add-on below Prime), multi-company (each company = separate paid subscription), granular role/permission system, public REST API. **No native payroll** — integrates via file-import only (Talenox, PayrollPanda, Kakitangan, MySyarikat). **No full-accounting mobile app** — mobile presence is a separate free app, "Bukku MiniPOS," for micro-retail/POS only. Bank reconciliation is upload-based ("SmartRecon": PDF/CSV per bank) — the only confirmed *live* bank feed is Maybank. E-commerce sync (Shopee/Lazada/EasyStore/WooCommerce/Shopify) requires a **paid third-party middleware, PayRecon** — not native.

**Pricing (effective 1 July 2025, verified live):** Launch RM0 (companies <6mo old) → Seed RM35/mo → Grow RM65/mo → Prime RM95/mo → Elite RM135/mo (all +8% SST, annual ≈10x monthly). Unlimited users on every tier. Storage/email/OCR quotas scale by tier; SST module and multi-currency are paid add-ons (RM12/mo each) below Prime, bundled free at Prime/Elite. Government MADANI Digital Grant gives 50% off. **Caution:** a lot of stale pricing (old RM99/mo flat tiers, dead promo codes) still surfaces in search results/third-party blogs — only the numbers above, pulled directly from the live pricing page, should be trusted.

**LHDN/MyInvois compliance:** Embedded, click-to-submit e-invoicing (not manual export); supports standard invoices, consolidated (B2C monthly) e-invoices, and self-billed invoices. **Unresolved contradiction found:** Bukku's marketing claims Peppol Network compliance, but its own plan-comparison grid lists "Peppol integration" as "(coming)." MDEC's official Peppol provider list could not be fetched (503 errors) to settle this — **flag for direct re-verification before relying on it.**

**Target customer:** Explicitly positions for SME owners "without an accounting background," startups (<6 months old, free tier), micro-retail (MiniPOS), e-commerce sellers, and accounting firms/bookkeepers (claims 5K+ accountant users, 200+ firms, 65K+ companies activated). Skews toward service businesses, freelancers, and lean startups — not inventory-heavy trading/wholesale.

**Other tax compliance:** SST module maps to SST-02 fields (unclear if it produces a literal filable file or just a report for manual re-entry). No Form C / corporate tax computation tool found — appears out of scope.

**Weaknesses/gaps:** No native payroll; bank feed is Maybank-only, all other banks are manual upload; shallow/single-location inventory below top tier; no offline mode; add-on-heavy pricing below Prime; e-commerce sync needs paid middleware; the Peppol contradiction above. **Notably, almost no independent review-site footprint** — no usable G2 or Trustpilot data, and the "Buku" listing on Capterra is a confirmed false match (unrelated Indian product). This is itself a gap in due-diligence signal, not evidence either way on user satisfaction.

---

## 2. SQL Account (sql.com.my, by E Stream MSC / eStream Software)

**Feature set:** Fundamentally a **Windows desktop application built on the Firebird database**, with cloud hosting layered on top of the *same* portable database file rather than a rearchitected multi-tenant SaaS backend — SQL's own marketing confirms "100% database ownership... retrieve your full database and restore it to local SQL software." Full document chain (Quotation→SO→DO→Invoice), strong inventory (multi-warehouse, consignment, van sales, serial/batch/expiry, barcode, reorder automation, separate Manufacturing module), CTOS credit-check integration, WhatsApp invoice sending. **Payroll, HRMS, POS, BI Dashboard, and e-commerce management (X-Store) are all separate SKUs/products**, not integrated modules — data moves between them via file export/import, not a shared live database. Mobile access ("X-Mobile") is a browser-based web app, not a native App Store/Play Store binary. Documented REST API exists (auth model, sample code) but access pricing is undisclosed. Bank reconciliation: manual tick-off by default; **only Maybank has a confirmed live auto-feed** (same partner program Bukku is in).

**Pricing:** Genuinely inconsistent across SQL's reseller/dealer network — two incompatible cloud pricing schemes found (RM79–109/mo vs. RM99–179/mo with an 18-month lock-in), and desktop entry-tier pricing spread RM1,599–2,699 across 5+ dealers for the nominal same tier. No source discloses the post-year-1 annual support/maintenance renewal fee directly; third-party estimate is RM300–600+/year — and **that renewal is reportedly a compliance dependency**: a lapsed support contract means no e-invoice compliance patches or technical support, per independent analysis. This is a materially different risk model from Bukku's straightforward subscription.

**LHDN/MyInvois compliance:** Direct API integration via LHDN's Intermediary registration flow (documented, not manual export). Covers invoice, credit note, debit note, self-billed sub-types, refund notes, and consolidated e-invoice submission. Genuine LHDN validation QR code embedded via report template. Marketing separately claims Peppol/PRSP accreditation, but this doesn't appear in the operational e-invoice documentation (same kind of marketing/product gap seen at Bukku) — **also flagged as unverified.**

**Target customer:** Vendor markets "all business sizes," but independent commentary and featured case studies (hardware trading, bedding manufacturing) consistently skew toward **operationally mature, inventory-heavy trading/distribution/manufacturing SMEs** — not micro or solo-trader businesses. Strong, well-evidenced presence in the **accountant/bookkeeper channel** (dedicated "SQL Accountant Set" product, MIA member privilege listing, large authorized-dealer network).

**Other tax compliance:** Dedicated SST module mapping to SST-02 return fields (again, unclear if submission-ready or manual re-entry). No evidence of Form C / corporate tax prep in SQL Account itself (payroll statutory filing — EPF/SOCSO/EIS/PCB/Borang E — lives in the separate SQL Payroll product).

**Weaknesses/gaps:** Desktop/on-prem-first architecture requiring local server/NAS and IT maintenance discipline for the classic deployment; genuinely confusing/conflicting reseller pricing; compliance-patch access tied to paying ongoing support renewal; full functionality fragmented across five-plus separate SKUs; **essentially zero presence on G2, Capterra, Trustpilot, or Slashdot** despite claiming 250K–320K+ customers — same review-visibility gap as Bukku, so a new entrant can't easily benchmark sentiment against either incumbent from public review data alone.

---

## 3. Official LHDN / MyInvois e-Invoice Requirements (Current State)

**Mandatory rollout (confirmed on hasil.gov.my, timeline page updated 30 Aug 2026):**

| Annual turnover | Mandatory since |
|---|---|
| > RM100 million | 1 Aug 2024 |
| RM25m – RM100m | 1 Jan 2025 |
| RM5m – RM25m | 1 Jul 2025 |
| Up to RM5m | 1 Jan 2026 |

**Two major regulatory changes just landed, both directly relevant to ArmBooks' target segment:**
- The exemption threshold was **raised from RM1m to RM3m turnover**, effective **1 Sep 2026** (PM announcement 30 Aug 2026, codified in e-Invoice General Guideline v4.8) — exempting an estimated 1.1M+ MSMEs. **Caveat (secondary-sourced, not independently confirmed against primary PDF text):** an anti-fragmentation carve-out reportedly still requires e-invoicing if the business has a related company/shareholder/JV at ≥RM3m turnover.
- The **Phase 4 penalty-free "interim relaxation" period was extended from 31 Dec 2026 to 31 Dec 2027** (normal enforcement resumes 1 Jan 2028) for the RM3m–RM5m band. Earlier phases (above RM5m) are already under full enforcement.

**Practical implication for ArmBooks:** most of the smallest Malaysian SMEs are **not currently mandated** to e-invoice and won't face real enforcement pressure until 2028 at the earliest — this is a fast-moving target (the threshold has already moved twice since 2023) and should not be treated as a fixed technical spec. The RM3m–RM100m+ band, however, is under active, real enforcement today, including LHDN cross-referencing e-invoice submission data against declared income (a compliance sweep reportedly surfaced RM3.5 billion in unreported income, and an April 2026 enforcement operation caught 108 non-compliant taxpayers).

**Technical compliance requirements:** UBL 2.1-based e-invoice, JSON or XML, XAdES digital signature (SHA-256/RSA) via an approved Malaysian CA certificate. Submission via MyInvois Portal (manual, low-volume) or API (ERP/software integration via LHDN's SDK). Validation flow: submit → LHDN validates in real time → issues UUID + QR code → notifies both parties. **72-hour cancellation window** after validation (issuer cancels; buyer cancels for self-billed docs); after 72 hours, corrections require a credit/debit/refund note instead. Consolidated (monthly, B2C-leaning) e-invoicing is allowed except for certain excluded transaction types and — new in 2026 — **any single transaction over RM10,000 now requires an individual e-invoice**, even during the relaxation period. Self-billed e-invoicing is required for specific scenarios (payments to agents/dealers/distributors, foreign suppliers, certain e-commerce/gaming payouts, etc.).

**Integration path for a software vendor like ArmBooks:** No formal LHDN certification/approval scheme exists for direct API integration — any conformant system may integrate once the taxpayer registers it as an "Intermediary" on the MyInvois Portal and issues it an access token (this is the path both Bukku and SQL Account use). A heavier alternative is **MDEC Peppol accreditation** (Service Provider or Peppol-Ready Solution Provider status) — a security/compliance-audited process that both incumbents *claim* but neither could be confirmed as *actually* holding when checked against their own product documentation.

**Other relevant statutory obligations (secondary-sourced, not primary-confirmed in this pass):** SST registration threshold RM500k, bimonthly SST-02 filing; CP204 tax estimate (mandatory e-filing only, no manual submission) and Form C annual return — **neither Bukku nor SQL Account was confirmed to produce a submission-ready Form C / tax computation package**, suggesting this sits outside both incumbents' scope today.

**Penalties:** Section 120(1)(d), Income Tax Act 1967 — RM200–RM20,000 fine and/or up to 6 months imprisonment, applied per instance (scales with invoice volume). Currently softened by the relaxation periods above where applicable.

**Important caveat carried over from the research:** Several of the most decision-relevant figures (exact mandatory field count, current consolidation-exclusion list, exact self-billed scenario list, exact PDF-sourced penalty text) came from secondary advisory-firm summaries because the tool couldn't extract text directly from LHDN's official PDFs — the underlying guideline documents move fast (two dot-releases in the last month alone) and should be spot-checked directly against `hasil.gov.my` immediately before ArmBooks' e-invoice module is actually built, not just at scoping time.

---

## 4. Gaps & Differentiation — Where ArmBooks Can Realistically Win

Synthesized across all three research passes. Ranked roughly by how directly each maps to the owner's stated compliance-first priority and to a real, evidenced incumbent weakness (not a guess).

1. **"Compliance never gated behind an upsell or a lapsed renewal."** Both incumbents tie core compliance to friction: Bukku gates its SST module and multi-currency behind paid add-ons below its Prime tier; SQL Account ties ongoing e-invoice compliance *patches* to an active annual support-renewal fee that isn't even transparently priced. A product that bundles e-Invoice + SST as an unconditional baseline at every tier, with pricing that's simple and public (unlike SQL's conflicting reseller price lists), is a direct, evidenced wedge — and matches the owner's stated compliance-first framing exactly.

2. **Live, broad bank-feed automation.** Neither incumbent has live bank feeds beyond Maybank — both fall back to manual PDF/CSV upload reconciliation for every other bank (CIMB, Public Bank, RHB, Hong Leong, AmBank, etc.). This is a well-documented, shared gap and a concrete technical differentiator if ArmBooks can secure broader live bank integrations.

3. **Genuinely unified payroll.** Bukku has no payroll engine at all (file-import only into third-party tools); SQL's payroll is a separate SKU synced by file export/import, not a shared database. A real-time, single-login payroll+accounting product — statutory EPF/SOCSO/EIS/PCB filing included, live-synced to the GL — would out-integrate both, and payroll statutory filing sits on the same LHDN/RMCD compliance surface the owner is already prioritizing.

4. **Beyond e-Invoice: actual income-tax filing prep.** Neither product could be confirmed to produce a submission-ready Form C or tax computation schedule — both stop at journal-entry-level tax expense booking at best. Extending ArmBooks past bookkeeping into real CP204/Form C prep would be a genuine step beyond either incumbent, directly aligned with the "LHDN compliance" positioning the owner wants.

5. **Compliance-status visibility as a trust feature.** Given LHDN is actively auditing via e-invoice data cross-referencing (RM3.5B in unreported income surfaced, active enforcement sweeps), a dashboard that makes a business's compliance posture visible and safe — real-time MyInvois submission/validation status, proactive alerts before the 72-hour cancellation window closes, audit-trail highlighting — is something neither incumbent's documentation shows as a designed feature, not just a byproduct of the submission flow.

6. **Fill the review/trust vacuum.** Both Bukku and SQL Account have almost no public review-platform presence (no usable G2/Capterra/Trustpilot data for either, once false matches are excluded). Being the entrant that actively builds a public trust record (case studies, verifiable reviews, transparent pricing) is a low-cost, high-leverage differentiator precisely because neither incumbent has claimed that ground.

7. **Native e-commerce sync without middleware fees.** Bukku requires a paid third-party (PayRecon) to sync Shopee/Lazada/etc.; SQL's e-commerce management is a separate SKU (X-Store). A native, no-extra-cost e-commerce sync targets Malaysia's fast-growing online SME segment more cleanly than either.

8. **A real cloud-native architecture, not desktop-with-cloud-veneer.** SQL Account's cloud tier is a hosted instance of the same Firebird desktop database, not a rearchitected multi-tenant SaaS backend — this shows up as real friction (local server/NAS requirements for on-prem customers, IT maintenance overhead). A genuinely cloud-native product is a clear architectural edge for any SME that doesn't want IT overhead, aligned with Bukku's own positioning but executed better on breadth (payroll, bank feeds) than Bukku currently offers.

**Segment to prioritize, given the regulatory finding in §3:** because the exemption threshold just moved to RM3m and enforcement is deferred to 2028 for the smallest band, the RM3m–RM100m segment (already under active enforcement today, and squarely SQL Account's traditional stronghold) may be a more urgent near-term wedge than the sub-RM3m micro-SME segment Bukku already dominates on price/simplicity — worth an explicit strategic call with biz-head/ba-lead before requirements are finalized, since it changes which incumbent ArmBooks is really displacing first.

---

## Sources & Confidence

Full source lists, per-claim URLs, and detailed confidence/conflict notes are preserved in the three underlying research passes (not reproduced in full here for length). Headline caveats worth carrying into requirements planning:
- Bukku vs. SQL Peppol/PRSP certification claims are **both unverified** against MDEC's official provider list (site was unreachable during research) — re-check before any claim about "matching" or "exceeding" incumbent Peppol compliance.
- SQL Account pricing figures conflict across its reseller network — treat any single number as directional, not canonical, until confirmed directly with SQL or a customer quote.
- LHDN's own guideline PDFs could not be machine-read directly in this pass; the regulatory figures above are corroborated by multiple independent secondary sources but should be spot-checked against `hasil.gov.my` again immediately before technical build, given how fast this has moved in 2026 alone.
