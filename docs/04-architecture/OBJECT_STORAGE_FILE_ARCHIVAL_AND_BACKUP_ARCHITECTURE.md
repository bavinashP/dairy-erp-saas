# Object Storage, File Archival and Backup Architecture

**Document ID:** ARC-STO-001  
**Status:** Provider-neutral architecture baseline — provider selection and statutory retention remain explicit production approval dependencies  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, ERS-001, NFR-001, ARC-TEN-001, ARC-IAM-001, ARC-EVT-001, ODR-026/028/030/031.  
**Evidence reviewed:** Official Cloudflare R2 and Backblaze B2 pricing, security, lifecycle and object-retention documentation, reviewed 17 November 2026. Vendor allowances and features are time-sensitive and must be revalidated before procurement or deployment.  
**Purpose:** Define a provider-neutral design for invoices/PDFs, business attachments, generated reports, imports/exports, archived files and independently recoverable backups, including cost-conscious use of free-tier quotas without making them architectural dependencies.

## 1. Scope and key distinction

The platform will store several materially different data classes. They MUST NOT be treated as one undifferentiated file bucket:

1. **Operational documents:** invoice PDFs, receipts, delivery proofs, purchase documents, quality certificates and user-uploaded business attachments.
2. **Generated artifacts:** reports, statements, exports, import results and Tally synchronization packages.
3. **Historical archives:** older documents and finalized export packages retained for retrieval under an approved lifecycle policy.
4. **System backups:** database backups, database transaction-log/WAL archives where applicable, application configuration snapshots, object manifests and recovery metadata.
5. **Security/audit evidence:** audit exports and operational evidence subject to separate access and retention controls.

Object storage is not the system of record for transactional business data. The relational database (or approved transactional store) remains authoritative for invoice state, accounting entries, permissions, object ownership, retention metadata and references. A PDF is a rendered representation of a business record, not a substitute for the underlying structured record.

A backup is not an archive; an archive is not a backup; and object versioning alone is not a complete backup strategy.

## 2. Design principles

- Provider-neutral storage abstraction; no business module may depend directly on Cloudflare- or Backblaze-specific SDK semantics.
- Support S3-compatible object APIs where practical, but do not assume all providers implement every S3 feature identically.
- Keep VPS local disk temporary and bounded; it is not the only durable copy of user files or backups.
- Separate operational files, archival objects and backup copies by bucket/account/credential boundary as appropriate.
- Apply least-privilege credentials, encryption in transit and at rest, tenant-scoped authorization and audited administrative access.
- Use immutable object keys or content-addressed/versioned naming; do not silently overwrite a finalized invoice PDF or backup object.
- Store checksums, byte size, media type, object version/generation, provider, bucket, key, encryption metadata and verification status.
- Never expose provider credentials or permanent public bucket URLs to web/mobile clients.
- Use short-lived, narrowly scoped signed download/upload URLs where direct transfer is appropriate; authorize before issuing them.
- Treat free-tier quotas as cost allowances, not guaranteed capacity, SLA, retention, recovery or legal-compliance guarantees.
- Monitor actual usage and alert before quota or budget thresholds are exceeded.
- Make retention and deletion policy explicit per data class; do not apply one global lifecycle rule to every object.
- Support migration between providers without changing business record identifiers or losing audit references.

## 3. Logical storage services

Define application-level interfaces such as:
- **ObjectStore:** put, get, head, list-within-authorized-prefix, copy, delete, checksum and multipart operations.
- **DocumentService:** create document record, bind to business entity, render/replace by new version, authorize access, issue short-lived transfer URL.
- **ArchiveService:** transition eligible finalized objects to archival provider/class and verify the destination before changing authoritative location metadata.
- **BackupService:** create backup set, encrypt, upload, verify, inventory, retain, expire and restore.
- **StoragePolicyService:** resolve tenant plan limits, permitted file types/sizes, retention class, region/provider policy and quotas.
- **StorageReconciliationService:** detect missing objects, orphan objects, checksum mismatches, stale metadata and incomplete transfers.

Provider adapters must implement a capability contract. Capabilities such as object lock, lifecycle, versioning, checksum validation, multipart upload, server-side encryption, region selection and signed URLs must be declared and tested per provider. Unsupported features must fail explicitly; do not silently emulate immutability or retention.

## 4. Tenant isolation and object naming

- Every object metadata record MUST be bound to a tenant. Branch scope is recorded when the source document is branch-specific.
- Object keys MUST include a non-guessable tenant namespace and opaque object identity; do not put customer names, phone numbers, GSTIN, bank details or other personal data in keys.
- A tenant ID in an object key is organizational metadata, not authorization. Every read, list, upload, copy, delete, archive and restore operation requires server-side authorization.
- Do not let clients choose arbitrary bucket names, object keys, storage classes or provider credentials.
- Do not rely on prefix isolation alone as the only control for multi-tenant security.
- Use separate service identities/credentials for normal document access and backup administration. Backup credentials must not be available to ordinary application request handlers.
- Platform support access to tenant documents requires a separate, audited, time-bounded grant.
- Cross-tenant deduplication is not permitted by default because it can leak object existence and complicate deletion/ownership semantics. Any future deduplication requires a separate security review.

## 5. Document lifecycle

A document record should include:
- Stable document ID, tenant ID, optional branch ID and business-owner entity/type/ID.
- Document category and sensitivity classification.
- Original filename (stored as metadata, safely encoded), media type, byte size and checksum.
- Storage provider, bucket/container, opaque object key and provider version/generation.
- Created/uploaded timestamp, actor/service identity and source operation/correlation ID.
- Document/render template version and source business-record version where applicable.
- Retention class, legal-hold state, deletion eligibility and lifecycle status.
- Malware/content scan state and verification state where applicable.

Suggested lifecycle states: initiated, uploading, quarantined, available, archive_pending, archived, restore_pending, deletion_pending, deleted, failed, needs_review. State transitions must be auditable and recoverable.

For generated invoices:
- Persist the structured invoice and accounting/business state first according to the domain transaction boundary.
- Generate PDF asynchronously when appropriate, using the approved invoice/template version and a stable source record reference.
- Upload under an immutable versioned key; verify checksum and metadata before marking the artifact available.
- A corrected/reissued invoice creates a new linked document version and follows the invoice correction/reversal rules; do not overwrite historical evidence.
- PDF generation/upload failure must not silently undo an already committed sale or invoice. Surface a recoverable artifact-generation status.
- Re-downloading must use authorized, short-lived access and be logged according to sensitivity policy.

## 6. Storage classes and provider-neutral placement

Use logical classes rather than hard-coded provider names:
- **HOT:** frequently accessed current documents and operational attachments.
- **WARM:** older but routinely retrievable documents and report artifacts.
- **ARCHIVE:** infrequently accessed finalized documents where restore/retrieval latency and fees are acceptable.
- **BACKUP:** independently recoverable encrypted backup sets, separated from application credentials and ordinary document lifecycle.
- **TEMPORARY:** short-lived imports, exports and intermediate artifacts with explicit expiry.

Placement is decided by policy, access frequency, restore objectives, geography, cost, provider capability and contractual/legal requirements. A provider's low-cost or free tier does not automatically qualify it for every class.

## 7. Provider evaluation: Cloudflare R2 and Backblaze B2

The following are vendor-published allowances observed on 17 November 2026; USD prices exclude applicable taxes, currency conversion and any bank/card fees. They are not guaranteed to remain unchanged.

### 7.1 Cloudflare R2

Official pricing currently lists, for Standard storage, a monthly free allowance of 10 GB-month storage, 1 million Class A requests and 10 million Class B requests, with no direct R2 egress bandwidth charge. Beyond the allowance, the published Standard rates are $0.015/GB-month, $4.50/million Class A and $0.36/million Class B requests. Infrequent Access has separate charges and is not covered by the Standard free tier; retrieval is charged and a 30-day minimum storage duration applies.

Potential fit:
- Current invoice PDFs, receipts and documents accessed by users.
- Report/export artifacts that need repeated downloads.
- Web/mobile document delivery where egress predictability is valuable.
- A low-volume pilot where the monthly allowance may cover some usage.

Cautions:
- 10 GB-month is a small allowance for a growing SaaS archive; it is not a free unlimited backup destination.
- Frequent listing, metadata polling, small-object operations and downloads can consume request allowances.
- Infrequent Access is not automatically cheaper for frequently restored backups; model retrieval and minimum-duration charges.
- R2's bucket-lock feature can enforce age-based or indefinite retention rules, and lifecycle rules can expire/transition objects. Their interaction must be tested and retention configuration protected from unauthorized changes.
- Cloudflare's automatic encryption at rest and TLS in transit are useful provider controls, but sensitive backup payloads should additionally be encrypted by the application/backup process before upload where independent key control is required.

### 7.2 Backblaze B2

Official pricing currently lists the first 10 GB stored as free. The published offer includes free monthly egress up to 3x average monthly stored data and free Class A/B/C transactions for pay-as-you-go customers; Class D transaction allowances/charges are separately defined. Paid storage is currently advertised from $6.95/TB/month. Verify the exact transaction classes, thresholds, billing terms and account eligibility on the provider's live pricing page before use.

Potential fit:
- Encrypted database backup sets and older archival documents.
- Secondary/off-site backup copy separate from the primary VPS.
- A destination for longer-retained backup generations where retrieval is less frequent.
- S3-compatible integration, subject to testing the exact APIs and features used.

Cautions:
- Free egress is limited by the stated allowance/partner conditions; do not assume unlimited free downloads from every route.
- Object Lock must be enabled on the bucket to use retention/legal-hold protection. Enabling it is a consequential bucket configuration; compliance-mode retention is intentionally difficult or impossible to shorten.
- Lifecycle rules can hide/delete older versions. Hidden versions may still consume storage until permanently deleted.
- Object Lock, lifecycle and versioning behavior must be tested together before enabling production retention.
- B2 free storage is not a second independent backup by itself; backup integrity, credentials, encryption, retention and restore tests remain our responsibility.

### 7.3 Initial recommendation (proposal, not provider approval)

Keep the application provider-neutral and evaluate both providers through a small proof of concept:
- R2 as a candidate for frequently accessed PDFs and generated artifacts where no-egress billing is valuable.
- B2 as a candidate for encrypted off-site backup generations and historical archives, subject to region, contract, restore and immutability verification.
- Do not make either provider mandatory for every tenant or assume the free tier is sufficient at scale.
- Allow a paid provider, alternate S3-compatible provider or VPS-independent backup destination to be selected later without changing domain logic.
- A dual-provider design may be appropriate for selected critical data, but the second copy must be in a separately controlled failure/security domain and its extra cost must be justified.

No provider has been selected as the production standard. Record the final choice in an ADR after testing current pricing, region availability, data transfer paths, service terms, security controls, support and restore performance.

## 8. Backup architecture

The operational recovery objectives, backup schedule, restore sequence, tenant-scoped recovery and disaster-recovery exercises are specified in [Backup, Restore and Disaster Recovery Architecture](BACKUP_RESTORE_AND_DISASTER_RECOVERY_ARCHITECTURE.md). This document remains authoritative for document/object storage mechanics; ARC-DR-001 is authoritative for backup and recovery operations.


Backups must cover more than object files. A recoverable backup set may include:
- Transactional database full/incremental backups and transaction-log/WAL/binlog archives as supported by the selected database.
- Object inventory and object version/checksum manifest.
- Application configuration required to rebuild the service, excluding plaintext secrets.
- Infrastructure/deployment definitions and release identifiers.
- Required encryption-key recovery material under a separately controlled key-management procedure.
- Backup metadata: backup ID, tenant/platform scope, creation/cut-off time, database consistency marker, included object ranges, checksum, encryption key ID/version, software/schema version and restore instructions.

Requirements:
1. Define database-consistent backup and point-in-time recovery strategy; a raw copy of a live database directory is not automatically consistent.
2. Coordinate database snapshot/cut-off and object inventory so restored database references can be reconciled with object versions.
3. Encrypt backup data before leaving the trusted backup process when independent encryption is required. Use authenticated encryption and managed key rotation; never store the only decryption key alongside the ciphertext.
4. Use separate credentials/accounts or at least separately scoped keys for backup creation and deletion. The production application must not have permission to erase all historical backups.
5. Keep at least one backup copy outside the VPS provider/account and outside the same administrative credential boundary.
6. Where supported, use immutable retention/Object Lock for selected backup generations. Protect lock policy and credentials from ordinary application operators.
7. Maintain a backup catalogue and verify checksums after upload; an upload success response alone is insufficient.
8. Alert on missing scheduled backup, unexpected size/volume changes, checksum failure, upload failure, retention-policy drift and restore-test failure.
9. Define retention by backup class and business/legal policy; do not assume a universal retention duration.
10. Perform scheduled restore drills into an isolated environment and record actual recovery time and data loss window.
11. A successful backup job is not proof of recoverability until restore has been tested.
12. Document how to recover when the VPS, provider account, application credentials or encryption-key service is unavailable.

## 9. Retention, archival and deletion

Retention policy is a domain/legal decision under ODR-026, not a storage-provider default. Different classes may have different retention periods:
- Tax/accounting source records and their issued invoice representations.
- Customer/supplier/farmer documents.
- Delivery evidence and operational attachments.
- HR/employee documents.
- Temporary exports/imports.
- Audit/security evidence.
- Database and object backup generations.

Requirements:
- Record retention start event, policy version, expiry date/eligibility and legal-hold status.
- Retention rules must be configurable where law/contract/business policy requires it, but minimum security controls cannot be disabled by tenant configuration.
- Legal hold suspends deletion and must be auditable.
- Deletion workflows must consider object versions, replicas, caches, generated derivatives and backups.
- A business record deletion must not silently remove legally or operationally required evidence; conversely, expiry must not leave unnecessary personal data indefinitely.
- Provider lifecycle rules are a cost/cleanup mechanism, not the authoritative retention register.
- Never configure a broad bucket-wide expiration rule without validating prefixes, object classes, locks and newly uploaded objects.
- When moving an object, verify destination checksum and metadata before updating the primary reference; retain source until verification and policy allow removal.

## 10. Security, privacy and India-specific considerations

- TLS for all transfers; provider encryption at rest plus application-side encryption for sensitive backup/archive classes.
- Store key identifiers and versions, not key material, in application metadata.
- Rotate provider keys; support immediate revocation and credential compromise procedures.
- Separate tenant access authorization from storage provider authentication.
- Scan uploads for malware and validate file type/content; do not trust filename or browser MIME type.
- Serve downloads as attachment where appropriate, with safe content-disposition and no public indexing.
- Redact personal data from object keys, logs and metrics.
- Restrict support staff access and log every privileged retrieval/deletion/restore.
- Evaluate provider region, data residency, cross-border transfer, subprocessors, contractual terms and applicable Indian privacy/tax/accounting requirements before production use. Do not claim a provider is India-resident or legally compliant without current evidence.
- Maintain a tenant export/offboarding process that includes structured records and associated documents, subject to approved legal retention/hold rules.

## 11. Quota, cost and noisy-neighbour controls

- Meter storage bytes, object count, API operations, egress/retrieval, backup generations and per-tenant usage.
- Define plan entitlements and hard/soft thresholds separately from provider free quotas.
- Warn at configurable thresholds; block or queue non-critical uploads only under an explicit policy. Never silently drop an invoice, receipt or required audit artifact because a quota was reached.
- Reserve capacity or use a controlled fallback for critical document generation and backup.
- Apply per-tenant upload limits, concurrency controls and fair-use protection.
- Estimate monthly cost using storage growth, average object size, PUT/GET/LIST counts, download volume, backup frequency, retention, restore drills and currency/tax assumptions.
- Reconcile provider invoices/usage reports with internal metering.
- Keep a documented paid-tier transition path; do not require a code change when free quota is exceeded.
- Do not let one tenant consume shared free-tier quota without metering, fair-use controls and plan policy.

## 12. Failure and reconciliation cases

Test and support:
- Upload succeeds but database metadata commit fails.
- Database record commits but upload fails.
- Client retries after upload acknowledgement is lost.
- Object exists but metadata reference is missing.
- Metadata points to a missing or checksum-mismatched object.
- Archive copy completes but acknowledgement is lost.
- Provider outage during invoice PDF generation.
- Signed URL expires during download.
- Tenant access is revoked while a URL is still valid.
- Backup completes but encryption key is unavailable.
- Backup upload succeeds but checksum verification fails.
- Lifecycle rule deletes an object earlier than expected.
- Object Lock prevents an intended cleanup.
- Database restore references object versions not present in restored inventory.
- Provider account/credentials are compromised.
- VPS and primary storage provider are simultaneously unavailable.

## 13. Proof-of-concept acceptance criteria

Build a non-production proof of concept before final provider selection:
- Upload/download/delete/head/list using a least-privilege service identity.
- Signed URL expiry and authorization boundary.
- Large and multipart uploads with interrupted/retried transfer.
- SHA-256 or equivalent checksum verification.
- Encryption-at-rest verification and client-side encrypted backup round trip.
- Object versioning, lifecycle, lock/retention and deletion behavior.
- Tenant namespace isolation and attempted cross-tenant access.
- Provider outage and retry/reconciliation behavior.
- Backup catalogue, full restore and point-in-time database recovery rehearsal.
- Measure upload/download/restore throughput from the intended VPS region.
- Record monthly cost estimates at low/expected/high workload scenarios, including free-tier overages and egress/retrieval.
- Verify India region/data-transfer/legal considerations from current official documentation and contract terms.

## 14. Open decisions

- **ODR-026:** data retention, archival, deletion, export and legal holds.
- **ODR-028:** backup frequency, backup retention, RPO/RTO and disaster recovery.
- **ODR-030:** storage adapter, encryption/key-management and tooling choices.
- **ODR-031:** production topology, provider accounts, regions and failure-domain separation.
- Provider choice, primary/secondary roles, free-tier usage and paid-tier transition remain open until proof-of-concept and cost review.

## 15. Exit criteria

Before production use, approve the data-classification and retention matrix, provider/region and contractual assessment, storage capability matrix, key-management design, credential separation, quota/billing controls, backup schedule and RPO/RTO, restore runbooks, immutable-retention policy where needed, monitoring/alerts and proof-of-concept results. No free-tier allowance is a production availability or recovery commitment.
