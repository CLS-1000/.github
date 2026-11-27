How CLS-1000 & Notitia Civica Work

CLS-1000 is built around a strict separation between automated collection, deterministic verification, and human-approved publishing. No automated script, rule engine, or local language model is ever permitted to publish directly to a public surface. Every record moves through verifiable custody, explicit gating, and an append-only audit trail before a human editor signs off on release.

1. End-to-End Civic Intelligence Pipeline

flowchart LR
    A["Primary Sources<br/>(CISA KEV, SEC, CFTC, Civic Feeds)"] --> B["Custody & Sealing<br/>(SHA-256 Digest + Evidence Pack)"]
    B --> C["Deterministic Triage & Gates<br/>(Explicit Thresholds + Scoring)"]
    C -->|Passed| D["Human Editorial Sign-Off<br/>(5-Check Verification)"]
    C -->|Failed Gate| E["Research Queue<br/>(Needs Primary Source)"]
    D --> F["Public Site & Portal<br/>(notitia-civica-ux / CLS-1000.github.io)"]


How Each Stage Operates

Primary-Source Ingestion

Public feeds are polled directly from authoritative sources — including CISA Known Exploited Vulnerabilities (KEV) and STIX 2.1 threat feeds, SEC EDGAR corporate filings (such as Form 8-K and Form 4), CFTC Commitments of Traders (COT) market reports, and state or federal public-disclosure registries.

Upstream triage engines (spec-1V0.7) and domain adapters normalize incoming records into a standard envelope format while keeping collection completely decoupled from publishing.

Custody & SHA-256 Evidence Sealing

Before any analysis or summarization runs, the raw primary-source document (HTML, XML, JSON, or PDF) is fetched and sealed into an evidence pack.

Authentication headers or API credentials used to retrieve a feed are stripped prior to sealing so no operational secrets enter the evidence store.

Each fetched payload is hashed with SHA-256, binding the exact retrieved bytes, timestamp, and source URL together so downstream reviewers can verify that a published claim matches the original source at the time of capture.

Deterministic Triage & Gate Routing

Incoming envelopes pass through explicit, reproducible thresholds rather than opaque scoring.

Rule-only leads never auto-publish: any signal flagged solely by a heuristic or rule—or any secondary-source summary lacking primary-document corroboration—is automatically diverted into a Research Queue flagged for primary-source verification.

When compiling cross-domain briefings (such as the World State Brief), selection follows a two-pass diversity rule: first guaranteeing balanced representation across distinct civic, economic, and security domains, then filling remaining slots by recency and signal score while stripping restricted or editor-only metadata.

Human Editorial Sign-Off

Advancing a record to public status requires an explicit, five-part editorial sign-off covering:

Sources read: primary documents inspected directly.

Legal review: factual framing and attribution checked.

Quotes verified: any direct quotation matched verbatim to the sealed source.

Fairness review: nonpartisan, descriptive tone confirmed.

Plain-language review: headline, deck, and summary verified for clarity without speculation.

2. Signal Triage & Cryptographic Audit Chain (threat-signal-console & gatepost)

flowchart TD
    S["Incoming Signal<br/>(CISA KEV / STIX 2.1)"] --> G1["Gate 1: Source & Schema<br/>Validate structure and provenance"]
    G1 --> G2["Gate 2: Authority & Corroboration<br/>Require primary-source backing"]
    G2 --> G3["Gate 3: Threshold & Scoring<br/>Apply deterministic triage rules"]
    G3 --> G4["Gate 4: Analyst Verdict<br/>Record WATCHED / DISMISSED / ESCALATED"]
    G4 --> L["Hash-Chained Audit Log<br/>(Append-only provenance)"]
    L --> V["Offline Verification (gatepost)<br/>Verify chain integrity"]


Why Four Gates and Hash-Chained Logs

Transparent Gate Progression (threat-signal-console): Rather than a black-box risk score, every incoming cyber or civic signal is evaluated across four discrete gates—schema validity, primary-source authority, deterministic threshold checks, and an explicit analyst verdict (WATCHED, DISMISSED, or ESCALATED).

Tamper-Evident Audit Trail: Every gate evaluation and verdict appends a new record linked cryptographically to the previous event's hash. Modifying or deleting an earlier decision invalidates every subsequent link in the chain.

Offline Chain Verification (gatepost): Anyone with the exported audit log can run offline verification to confirm that the entire provenance chain is intact and free of gaps or silent edits—independent of the live server.

State & Procedural Tracking (proc_track): Tracks legislative, regulatory, and procedural state changes over time so status transitions remain reproducible.

3. Single-Writer Publishing & CI Verification (notitia-civica-ux & CLS-1000.github.io)

flowchart LR
    E["1. Export & Preflight<br/>Verify 5/5 sign-off checks"] --> B["2. Static Build<br/>Compile Astro site"]
    B --> C["3. Sync & Copy<br/>Update public branch"]
    C --> P["4. Publish & CI Gates<br/>Push + GitHub Actions deploy"]


How Public Releases Are Protected

Single-Writer Architecture: Only one designated publishing desk can export verified stories to notitia-civica-ux. Upstream collection engines have zero write access to public repositories.

Fail-Closed Export Preflight: Before any build starts, the exporter checks every queued public article. If even one required editorial check is incomplete, the deploy halts immediately and leaves the live site untouched.

Static Site Generation: Verified stories, briefs, and structured feeds are compiled into static assets via Astro, eliminating runtime database exposure on the public web tier.

Automated CI Gates: Every push to the public site triggers GitHub Actions checks to validate build integrity, routing, and schema compliance before CLS-1000.github.io and notitia-civica-ux update live.
