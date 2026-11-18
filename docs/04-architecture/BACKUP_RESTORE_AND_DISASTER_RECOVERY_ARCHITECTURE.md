# Backup, Restore and Disaster Recovery Architecture

**Document ID:** ARC-DR-001  
**Status:** Recommended engineering baseline — measurable production objectives require owner acceptance before launch  
**Product:** Dairy ERP SaaS (India)  
**Authority:** ARC-STO-001, ARC-EVT-001, ARC-SEC-001, ARC-OBS-001, ARC-CONC-001, NFR-001, Project Vision and Scope.  
**Purpose:** Define a practical, provider-independent recovery design for the SaaS platform, with special attention to transactional integrity, tenant isolation, independent backups and tested restoration. This document is separate from document archival and legal record retention.

## 1. Decision summary

Adopt the following as the initial engineering baseline for the first production service tier. These are recommendations for design and capacity planning, not a contractual SLA or a claim that the current development VPS can meet them.

| Control | Initial baseline | Reason |
|---|---|---|
| Database recovery | Database-native consistent full backup plus continuous transaction-log/WAL archiving where supported | Enables point-in-time recovery and reduces data-loss window |
| Transaction-log archive interval | Target no more than 5 minutes between successfully archived segments/positions | Limits exposure between full backups |
| Full backup cadence | At least daily | Simple, predictable baseline |
| Backup destinations | One protected backup copy outside the production VPS/provider account; a second independent copy for production-critical data after cost/risk review | Survives VPS/account failure |
| Backup encryption | Client/process-side authenticated encryption before remote upload for backup payloads | Separates confidentiality from storage-provider account controls |
| Backup identity | Dedicated least-privilege backup identity; production web/API credentials cannot delete backup history | Limits ransomware/operator blast radius |
| Immutable protection | Enable provider-native retention/object lock for selected backup generations after capability and restore tests | Protects against destructive credentials |
| Default operational restore point | Target RPO ≤ 15 minutes for core transactional data, subject to verified log archival | A practical starting target for financial/operational SaaS data |
| Default core-service recovery | Target RTO ≤ 4 hours for a declared major incident, subject to restore exercise | Establishes a measurable planning target |
| Daily backup retention | 14 daily generations | Short-term operational recovery |
| Weekly backup retention | 8 weekly generations | Medium-term rollback coverage |
| Monthly backup retention | 12 monthly generations | Longer operational recovery; not a statutory-record retention policy |
| Restore validation | Automated backup integrity checks plus scheduled isolated restore tests at least monthly | Backup success alone does not prove recoverability |
| Object recovery | Versioned immutable document keys plus periodic object manifest/checksum inventory | Reconciles database references and document versions |
| Recovery order | Identity/secrets and infrastructure → database → object references/files → workers/integrations → tenant traffic | Avoids processing against incomplete state |

If a provider, database engine or selected hosting topology cannot support a stated control, record the gap and choose an equivalent tested method before production; do not silently weaken the objective.

## 2. Recovery objectives and service tiers

RPO is the maximum tolerable data-loss interval. RTO is the target time to restore an agreed service level after a declared incident. Neither is the same as backup frequency, and neither should be advertised as achieved until tested.

Initial planning tiers:

| Tier | Examples | Planning RPO | Planning RTO | Recovery treatment |
|---|---|---:|---:|---|
| Tier 0 — Control plane | Platform identity, tenant registry, plan/entitlement state, security configuration | ≤ 15 min | ≤ 4 h | Prioritized database recovery, protected configuration and secret/key recovery |
| Tier 1 — Core tenant transactions | Sales/POS, subscriptions, delivery fulfilment, procurement, stock ledger, receipts, journals | ≤ 15 min | ≤ 4 h | Consistent DB restore + transaction-log replay + financial/stock reconciliation |
| Tier 2 — Critical documents | Issued invoice PDFs, receipts, farmer/quality evidence and required attachments | ≤ 24 h for object inventory recovery; object durability target is independent | ≤ 8 h for priority retrieval | Restore/version recovery from independent object copy and verify checksums |
| Tier 3 — Derived/rebuildable data | Search indexes, caches, temporary exports, derived dashboards | Rebuild from source; freshness target defined per feature | ≤ 24 h or feature-specific | Rebuild, re-index or regenerate |
| Tier 4 — Analytics/forecast outputs | Materialized aggregates, forecast runs, scenario outputs | Rebuildable from governed source snapshots; last published output may be restored if needed | ≤ 48 h | Recompute with recorded model/configuration version |

These targets are deliberately explicit starting points. Before production, validate them against business impact, transaction volume, hosting/provider capability, staffing, recovery cost and restore-test evidence. If the owner approves different service tiers, update this table and NFR traceability together. Do not leave a blank RPO/RTO field for a production-critical workflow.

## 3. Failure scenarios and recovery boundaries

The recovery plan must cover:
- VPS host, disk, OS or region failure.
- Accidental database deletion, corruption or destructive migration.
- Ransomware or compromised production administrator.
- Compromised cloud/storage provider account or leaked access key.
- Accidental object deletion, overwritten metadata or lifecycle-rule error.
- Database and object-store state becoming inconsistent.
- Failed release, application configuration or secret rotation.
- Queue/outbox backlog, duplicate delivery or stuck background work.
- Loss of encryption keys or secret-management access.
- External provider outage, including payment, messaging, storage and Tally synchronization endpoints.
- Tenant-specific corruption or accidental destructive action.
- Operator error during restore, failover or data repair.

A provider outage is not automatically a disaster requiring database failover. Use a documented degraded mode where safe, and never accept uncertain financial outcomes as confirmed.

## 4. Backup consistency model

### 4.1 Database
- Use database-native backup mechanisms that guarantee a consistent snapshot for the selected engine.
- Archive transaction logs/WAL/binlogs continuously or at a bounded interval; verify that archived sequence/LSN/position is complete and contiguous.
- Record backup ID, database identity, engine/version, schema migration version, snapshot timestamp, recovery position, encryption key ID/version and checksum.
- Test point-in-time restore to a specified timestamp/transaction boundary.
- Do not copy live database files with ordinary filesystem copy unless the database vendor explicitly documents the method as consistent.
- Keep database backups independent from application document storage lifecycle rules.

### 4.2 Object storage
- Every managed document has an authoritative metadata row and immutable object version/key.
- Maintain a periodic complete object manifest with tenant, object ID, provider/bucket/key/version, checksum, byte size, classification and status.
- Enable versioning where supported, but do not treat versioning as the only backup.
- Copy critical objects to an independent backup destination or account according to the approved criticality policy.
- Verify destination checksum before marking a copy complete.
- Keep source and destination copies until the recovery point and retention policy are satisfied.
- Reconcile database references against object manifests after restore.

### 4.3 Configuration and deployment
- Version-control non-secret application configuration, schema migrations, infrastructure/deployment definitions and release artifacts.
- Back up only the configuration needed to rebuild the platform; exclude plaintext credentials.
- Store secrets in a separately controlled secret-management system or protected recovery envelope.
- Maintain a tested mapping of secret names/versions to services and integrations.
- Preserve compatible application artifacts for the retention period needed to restore historical database schema safely.

### 4.4 Queues, outbox and external effects
- The database outbox/inbox and domain state are authoritative for recoverable internal asynchronous work.
- Queue messages may be replayed or reconstructed from durable outbox/job records; do not assume broker persistence is the only copy.
- After restore, pause external side-effect workers until reconciliation establishes which provider calls completed.
- Use stable idempotency keys and provider event IDs to prevent duplicate payments, notifications, Tally vouchers or stock/financial effects.
- Reconcile unknown outcomes before replay.

## 5. Backup pipeline

1. Scheduler creates a unique backup-run record with a deterministic scope and run ID.
2. Backup worker acquires a concurrency-safe lease; overlapping runs for the same scope are prevented.
3. Database-native consistent snapshot and transaction-log archive positions are captured.
4. Object manifest and required object versions are captured with a consistent cutoff strategy.
5. Backup components are packaged with a manifest and schema/release metadata.
6. Payload is encrypted using authenticated encryption with a separately managed key.
7. Encrypted data is uploaded using a dedicated backup identity to an independent destination.
8. Destination object metadata and checksum are verified.
9. Backup catalogue is marked verified only after all required components are present and checks pass.
10. Retention/immutability policy is applied and independently checked.
11. Alerting reports success/failure, duration, size, last successful recovery point and oldest unarchived log position.
12. A restore-validation job periodically selects a backup generation and restores it in an isolated environment.

A partially uploaded or partially verified backup is not a successful backup. It must remain visible as incomplete and be retried or cleaned up under controlled policy.

## 6. Destination and credential separation

- Primary production data and backup copies must not share the same VPS disk or single provider account as their only failure domain.
- Use a separate backup bucket/container and separate credentials from operational document access.
- Prefer a separate provider account/organization or independently controlled account for at least one recovery copy.
- Backup creation credentials may write new backup objects but must not delete or shorten retention of protected historical generations.
- Retention administrators and encryption-key custodians must be separate from normal application runtime identities.
- Keep recovery credentials offline or in a separately protected emergency-access process; test that authorized responders can obtain them.
- Use provider-native object lock/immutability only after testing account prerequisites, retention behavior, versioning, lifecycle interaction and restore procedure.
- Cloudflare R2 and Backblaze B2 remain candidate adapters under ARC-STO-001; neither free tier is assumed sufficient for production capacity or an SLA.
- Keep an exportable backup format and documented restore tooling so the platform is not irreversibly tied to one storage provider.

## 7. Retention, lifecycle and deletion

The backup schedule in Section 1 is an operational recovery baseline only. It does not determine how long invoices, tax records, payroll records, customer data or audit evidence must be retained under law or contract.

- Backup generations have their own retention class and deletion eligibility.
- Legal holds and incident preservation override normal expiry only through an authorized, audited workflow.
- Do not apply a provider lifecycle rule that can delete protected backups before the catalogue says they are eligible.
- Test retention policies with non-production objects before production enablement.
- Include expired versions, multipart remnants, replicas and provider-hidden versions in cost/deletion analysis.
- Deletion from primary storage does not automatically mean immediate deletion from backups; define the restore-time deletion/reconciliation process and communicate the distinction in the data lifecycle policy.
- When restoring a backup containing records subsequently deleted under an approved deletion process, reapply the deletion/tombstone ledger before making the restored service available, unless a legal hold requires preservation.

## 8. Restore and disaster-recovery runbooks

### 8.1 Declare and classify
- Incident commander records incident ID, start time, affected services/tenants, suspected cause and recovery objective.
- Determine whether the event is infrastructure outage, data corruption, credential compromise, malicious deletion, provider outage or tenant-scoped data damage.
- Preserve forensic evidence before destructive remediation.

### 8.2 Contain
- Isolate compromised hosts/accounts/keys and revoke affected credentials.
- Pause schedulers, queue consumers, integrations and external side effects if replay could duplicate or corrupt business effects.
- Prevent automated retention/deletion jobs from removing evidence during the incident.
- Communicate service status through an approved channel without exposing tenant data.

### 8.3 Rebuild trusted foundation
- Provision clean infrastructure from reviewed deployment definitions.
- Apply OS/runtime/database security baseline and restricted network access.
- Recover secrets and encryption keys through the approved independent process.
- Deploy the exact compatible application release and schema migration state.

### 8.4 Restore database
- Select the latest verified full/incremental backup before the target recovery point.
- Verify signature/checksum and decrypt in a controlled environment.
- Replay archived transaction logs to the approved point-in-time.
- Validate schema, migration state, tenant registry, referential integrity and critical table counts.
- Preserve the restored source as read-only until validation passes.

### 8.5 Restore objects
- Restore required object versions and manifests.
- Verify checksums, object metadata, tenant ownership and retention/legal-hold state.
- Reconcile missing, orphaned and duplicate object references.
- Do not make a file downloadable until authorization and integrity checks pass.

### 8.6 Reconcile business state
- Keep payment, notification, Tally and other external side-effect workers paused.
- Reconcile receipts, payment references, invoices, journal entries, stock movements, subscription occurrences, deliveries, procurement settlements and outbox/inbox states for the recovery interval.
- Identify unknown provider outcomes; query provider evidence or require controlled manual resolution.
- Use reversal/adjustment entries for corrections; never silently edit posted financial/stock history.
- Validate tenant and branch isolation before restoring user traffic.

### 8.7 Resume service
- Resume internal read paths and then controlled writes.
- Resume low-risk jobs before external side effects.
- Re-enable integrations with idempotency/reconciliation safeguards.
- Monitor error rate, queue age, audit backlog, database load and tenant reports.
- Record actual RPO/RTO and any objective miss.

### 8.8 Close and learn
- Preserve timeline, evidence, approvals, recovery point, restored release and reconciliation outcomes.
- Complete incident review and root-cause analysis.
- Create corrective actions with owners and due dates.
- Update threat model, runbooks, backup policy and tests.

## 9. Tenant-level recovery and data correction

A single tenant's accidental deletion or corruption should not automatically require a platform-wide restore.

- Prefer logical tenant-scoped recovery from immutable event/history or a point-in-time backup restored into an isolated recovery database.
- Compare candidate records and dependencies against current production state.
- Generate a reviewed recovery package; require approval before applying changes to live tenant data.
- Preserve current records created after the recovery point and merge only where domain invariants permit.
- Never restore a tenant snapshot over live state without conflict analysis.
- Rebuild derived reports/search indexes after recovery.
- Record requester, approver, operator, scope, before/after references and reconciliation evidence.

## 10. Verification and restore exercises

Minimum verification:
- Every backup upload has checksum verification and catalogue completeness checks.
- Automated backup monitoring checks the last successful full backup and last archived transaction-log position.
- Monthly isolated restore exercise covers database + object manifest + key recovery + compatible application release.
- Quarterly disaster-recovery exercise simulates loss of the primary VPS/provider account and recovery from the independent destination.
- At least annually, exercise a compromised-credential/ransomware scenario including immutable backup recovery.
- Test tenant-scoped recovery and post-restore deletion/tombstone reconciliation.
- Test interrupted upload, corrupt archive, missing log segment, unavailable key, expired credentials, provider throttling and incomplete object manifest.
- Record actual RPO/RTO, recovery steps, errors, operator time and corrective actions.
- A failed restore test is a production risk and must trigger an incident/remediation item; do not mark the backup control as passed.

## 11. Monitoring and alert conditions

Alert on:
- Missed full backup or transaction-log archive gap.
- Backup catalogue entry without verified object/checksum.
- Unexpected backup size decrease/increase or object-count anomaly.
- Backup age exceeding the RPO target.
- Retention/object-lock policy change or attempted deletion of protected generations.
- Key access/rotation/recovery anomaly.
- Backup destination credential use outside approved job identity.
- Object manifest mismatch, missing document or checksum failure.
- Restore exercise failure or RTO/RPO target miss.
- Queue/outbox replay backlog and unresolved external side-effect outcomes after recovery.

Each alert must identify the last known good recovery point, affected backup scope, runbook and accountable responder.

## 12. Roles and operational separation

Define separate capabilities for:
- Backup job service: create and verify backup generations.
- Restore operator: initiate restore into isolated environment.
- Recovery approver: authorize production recovery and tenant-scoped data replacement.
- Retention administrator: manage retention under approved policy.
- Key custodian: recover/rotate backup encryption keys.
- Incident commander: coordinate response and service restoration.

One-person operations may combine human duties in practice, but privileged actions still require re-authentication, audit evidence and a second-person approval for destructive/high-impact production actions where feasible. Emergency exceptions must be documented and reviewed after the incident.

## 13. Evidence and approval dependencies

This architecture makes an engineering recommendation for the initial recovery baseline. The following technical contracts and recovery controls are selected engineering baselines and are not reasons to postpone the logical design:
- The database adapter must implement the documented consistent backup and point-in-time recovery contract.
- The storage adapter must implement independent upload, checksum verification, immutable-key/version inventory and restore.
- The deployment model must provide an off-VPS recovery destination and separately protected credentials.
- The release process must preserve compatible application artifacts and migration history.

Only these matters require final owner/provider/legal confirmation before production:
1. Whether the proposed RPO/RTO targets are acceptable as a business commitment.
2. The production provider/account/region and final paid/free-tier mix after a measured cost and restore proof-of-concept.
3. Legal/contractual retention and deletion periods for each record class (handled by the data-lifecycle/legal requirements register, not this backup schedule).
4. Who is the named incident commander, restore approver and key custodian in the operating organization.

Record these as named approval dependencies with an accountable person/function and due date. Do not leave them as generic architecture questions or silently claim approval. The owner must accept the cost/risk trade-off for final service targets and production deployment.

## 14. Exit criteria

Development readiness requires backup interfaces, catalogue schema, restore scripts/runbooks, integrity checks, idempotent recovery/replay behavior and automated tests to be specified. Production readiness requires selected provider/account, protected keys, independent destination, approved RPO/RTO, retention/legal policy, named responders and successful monthly/quarterly restore evidence. No production launch may rely solely on an untested backup job or an object-store free tier.
