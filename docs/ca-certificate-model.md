# CA certificate model for e-Invoice signing

**Question:** does the Malaysian CA certificate required for XAdES-signing e-invoices
before MyInvois submission need to be procured per tenant (each SME customer), or once
at the vendor/platform level?

**Finding: vendor-level, not per-tenant. High confidence.**

LHDN's own MyInvois SDK FAQ states this directly: *"Service providers can use their own
certificate to submit document for all their customers."*
(https://sdk.myinvois.hasil.gov.my/faq/)

This is confirmed by a technical split LHDN enforces at the signature-validation layer,
documented in Pos Digicert's e-Invoice Document Signature Creation Guideline (v1.2):

- **DS308**: "Incompatible certificate for the Submission Channel. Organizational
  certificate cannot be used for Portal/Mobile submission."
- **DS309**: "Incompatible certificate for the Submission Channel. Personal certificate
  cannot be used for ERP submission."

In other words: the MyInvois Portal's manual-upload path requires a **personal**
certificate tied to the individual taxpayer's own MyTax login. The **ERP/API/Intermediary
integration path** — the one ArmBooks uses — requires an **Organizational** certificate
instead, and that certificate is explicitly designed to be reused across every taxpayer
the organization represents as Intermediary. The SDK's own validation rules confirm this
is the expected shape, not an edge case: an intermediary's certificate TIN/BRN is
expected to differ from the invoice's own taxpayer TIN.

Circumstantial market confirmation: neither Bukku's nor SQL Account's public onboarding
documentation mentions a customer-facing certificate-acquisition step at all — consistent
with both handling signing centrally on a vendor-held certificate rather than routing
each customer to a CA.

**Architecture implication:** ArmBooks procures a single Organizational certificate
(e.g. Pos Digicert's "Roaming Certificate" product, HSM-backed and marketed specifically
for Appointed Tax Agents/Intermediaries, or an equivalent from MSC Trustgate) once, at
the platform level. Tenant onboarding is limited to Intermediary registration — TIN/BRN
entry plus the taxpayer's own MyInvois Portal "Add Intermediary" consent step — with no
CA-shopping or certificate-acquisition flow required from individual customers.

**Still open — needs direct confirmation before procurement is finalized:** exact
commercial terms and pricing for an organizational certificate covering many represented
taxpayers were not found on public CA pricing pages. Validate directly with the chosen
CA's (Pos Digicert or MSC Trustgate) sales team before committing to a specific product
or vendor.

## Sources

- MyInvois SDK — Digital Signature: https://sdk.myinvois.hasil.gov.my/signature/
- MyInvois SDK — FAQ: https://sdk.myinvois.hasil.gov.my/faq/
- MyInvois SDK — Digital Signature User Guide (PDF): https://sdk.myinvois.hasil.gov.my/files/Digital_Signature_User_Guide.pdf
- Pos Digicert — e-Invoice Document Signature Creation Guideline v1.2: https://www.posdigicert.com.my/public/uploads/files/PosDigicert-Document_Signature_Creation_Guideline_Rev1.pdf
- Complyance — Digital Signature for e-invoicing in Malaysia: https://complyance.io/malaysia-blog/digital-signature-in-malaysia-e-invoicing
- SQL Account onboarding docs (no customer-facing cert step): https://docs.sql.com.my/sqlacc/usage/myinvois/onboarding
- Bukku e-invoicing submission methods (no customer-facing cert step): https://intercom.help/bukku/en/articles/13276337-e-invoicing-submission-methods-and-myinvois-control-in-bukku
