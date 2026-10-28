# SFS Genomics
## AI-Native Genomic Intelligence Platform

**607,051 SNP calls · 256 persistent EU D1 partitions · OAuth-secured remote MCP · public scientific research federation · reproducible validation**

**[Live mobile application](https://sfs-labs.github.io/lagoon/)** · **[Full engineering case study](https://sfs-labs.gitbook.io/sfs-labs-docs/ai-docs/featured-engineering-project-sfs-genomics/sfs-genomics)** · [Browse source](genomics/) · [GitHub Actions](https://github.com/SFS-Labs/sfs-genomics/actions) · [Scientific protocol](genomics/SCIENTIFIC_VALIDATION_PROTOCOL.md) · [Evidence ledger](genomics/PORTFOLIO.md)

[![Genomics research core CI](https://github.com/SFS-Labs/sfs-genomics/actions/workflows/genomics-ci.yml/badge.svg)](https://github.com/SFS-Labs/sfs-genomics/actions/workflows/genomics-ci.yml)

**Release status:** Operational engineering MVP, with bounded scientific reference-quality controls. **Not** a validated clinical diagnostic or personalized risk-prediction system.

### The engineering challenge

A consumer SNP-array export is a large, imperfect local file—not a research-grade database, a remotely accessible tool service, or an interpretable medical result. The objective was to build an end-to-end system under three hard constraints:

1. **iPhone-first:** parse and import a 609k-line dataset without relying on a desktop computer or repeatedly uploading the file into chats.
2. **Durable, authenticated AI access:** persist the accepted calls in EU Cloudflare D1 and expose controlled retrieval through a standards-compatible **Model Context Protocol (MCP)** service usable from ChatGPT.
3. **Scientifically honest outputs:** combine genotype retrieval with public GWAS and Ensembl data, but explicitly stop whenever build, allele orientation, linkage or endpoint evidence is insufficient.

The result is **not merely an rsID lookup**. It is an integrated **mobile input → verified cloud persistence → OAuth/MCP tool access → scientific source federation → abstention-first evidence** pipeline.

---

## Independently supported engineering outcomes

| System capability | Actual acceptance evidence | Scope |
| --- | --- | --- |
| **Real mobile ingestion** | **609,346 raw lines** inspected → **607,051 accepted SNP calls**, 1,516 no-calls and 779 rejected records | Real consumer-array import; not laboratory confirmation |
| **Durable EU storage** | Direct Cloudflare D1 aggregation confirmed **256/256 distinct shards** summing to 607,051 stored calls | Partitioned payloads, not 607,051 individual SQL rows |
| **Authenticated AI integration** | ChatGPT successfully invoked private status, single-/batch-genotype and micronutrient-panel tools | Recorded real end-to-end acceptance, not continuous service availability |
| **OAuth interoperability** | Independent GitHub-runner checks exercised the **HTTP 401 resource-metadata challenge**, OAuth discovery, **PKCE S256** and **Dynamic Client Registration (DCR)** | Protocol interoperability; no blanket grant of resource access |
| **Scientific source federation** | Public **GWAS Catalog** study results with identifiers/PMIDs and **Ensembl** reference-population linkage-disequilibrium responses | Verified for selected queries, not exhaustive study harmonization |
| **Reference-validation pilot** | Five pinned **GRCh37** loci; coordinate/allele/strand rules and deterministic Python/Node synthetic tests | Bounded QC, not validation of all 607k genotypes |
| **Failure semantics** | Reproduced Cloudflare D1 daily read-quota failure; the service distinguishes **temporarily unavailable** from **variant not observed** | Tested real operational failure state |

**Verification levels are deliberate:** an observed D1 import establishes durable storage; an authenticated MCP result establishes integration; an allele-reference check establishes a bounded consistency check. **None alone establishes clinical pathogenicity, an individual effect size or a treatment recommendation.**

[Read the underlying acceptance record](genomics/PORTFOLIO.md) · [Cloud deployment record](genomics/cloud/DEPLOYMENT_STATUS.md) · [Complete scientific validation protocol](genomics/SCIENTIFIC_VALIDATION_PROTOCOL.md)

## System architecture

~~~text
iPhone / consumer SNP-array export (609,346 lines)
    │
    ▼
Mobile browser parser: shape checks, duplicates, no-calls, malformed records
    │  607,051 accepted calls; deterministic partitioning by rsID
    ▼
256 staged shard uploads → Cloudflare Worker → final commit
    │
    ▼
Cloudflare D1 (EU): genome_shards, metadata, OAuth state
    │  authenticated requests, persistent subsequent lookups
    ▼
OAuth-protected remote MCP / JSON-RPC HTTP interface
    │  401 resource metadata → discovery → DCR → authorization + PKCE
    ▼
ChatGPT bounded typed tools: status · single/batch lookup · panels
    │
    ├── Public GWAS Catalog study-association evidence
    ├── Ensembl reference-population LD
    └── GRCh37 reference, strand, effect-allele and endpoint checks
                     │
                     ▼
        Evidence-linked result or explicit abstention
~~~

The browser, Cloudflare Worker, persistent data plane, OAuth authorization and scientific evaluation code are separate, inspectable components. Remote Cloudflare tools and the additional **local Python MCP** are *different tool catalogs*; a Python research function is not automatically available in ChatGPT.

### 1. Mobile data engineering: one import, persistent queries

The mobile web client performs deterministic parser and input checks, classifies malformed lines and missing/no-call variants, and uploads bounded shards through a staged Cloudflare Worker interface. **256 shards reconcile to the exact accepted call count** in a direct D1 aggregation.

Why partitioning matters: free-tier constraints and a phone-based upload make 607,051 separate SQL inserts an unnecessary cost and reliability bottleneck. Bounded shard uploads lower write overhead while introducing trade-offs in serialization, read granularity and quota consumption.

**Code:** [browser parser and client](genomics/web/) · [Cloudflare Worker / D1](genomics/cloud/worker.mjs) · [ingestion tests](genomics/web/tests/) · [documented storage evidence](genomics/cloud/DEPLOYMENT_STATUS.md)

### 2. AI-native interoperability: real remote MCP, not repeated CSV prompts

The cloud service implements an OAuth-gated MCP endpoint with typed tool inputs and structured results. It supports recorded, authenticated ChatGPT calls for `genome_status`, `lookup_snp`, `lookup_snps`, `micronutrient_panel` and public research queries.

OAuth discovery, PKCE S256, DCR and 401 protected-resource metadata have been exercised from an independent GitHub runner. Access is governed by the application's own authorization logic; public endpoint discovery is not permission to read private records.

A **seventh reference-validation tool** has been deployed and synthetically tested, but its post-quota private end-to-end acceptance remains open; an existing ChatGPT client may need to refresh its tool schema. This is different from the already-verified earlier tools.

**Code:** [remote MCP / OAuth implementation](genomics/cloud/worker.mjs) · [Cloud MCP tests](genomics/cloud/tests/) · [OAuth interoperability workflow](.github/workflows/sfs-genomics-oauth-dcr.yml)

### 3. Research federation and inference controls

GWAS study associations, Ensembl reference-population LD and raw array calls are **different evidence types**. The engineering challenge is to join them without silently upgrading a correlation into a personal clinical claim.

The [scientific validation protocol](genomics/SCIENTIFIC_VALIDATION_PROTOCOL.md) establishes explicit gates for:

- **Genome build and marker identity:** rsID, GRCh37 chromosome/position and reference/alternate alleles must agree.
- **Strand ambiguity:** reverse-complement candidates and palindromic A/T or C/G sites must not be silently resolved by guessing.
- **Study harmonization:** association metadata alone do not prove the study's forward-strand effect allele, cohort applicability or replication.
- **Linkage disequilibrium:** reference-population `r²` is unsigned; correlated markers cannot simply be summed as independent effects.
- **Endpoint separation:** circulating biomarkers, intestinal absorption, disease risk, symptoms and treatment response are not interchangeable outcomes.
- **Abstention:** absent, no-call, mismatched, underpowered or unharmonized evidence produces a bounded status—not an invented clinical estimate.

Five public reference loci were checked in the initial pilot, with deterministic Python and Node tests. Full array-wide genotype/reference harmonization and independently calibrated clinical risk models are **not completed**.

**Code:** [reference validation](genomics/validation.py) · [harmonization](genomics/harmonization.py) · [research adapters](genomics/research.py) · [LD logic](genomics/ld.py) · [synthetic validation tests](genomics/tests/test_validation.py)

## Operational failures and engineering decisions

| Observed constraint or incident | Engineering response | Remaining trade-off |
| --- | --- | --- |
| Mobile-only source data and repeated-file friction | One-time browser intake, 256 deterministic shards and persisted D1 dataset | Upload state and shard-level read granularity |
| Cloudflare Access returned HTML to OAuth discovery | Application-scoped configuration correction; 401 resource-metadata + DCR regression tested externally | OAuth service and access policies require continued maintenance |
| D1 Free daily row-read quota exhausted | Classify quota errors as **service temporarily unavailable**, not a missing genotype | Free-tier availability is not guaranteed |
| Incomplete GWAS effect-allele and clinical evidence | Strict provenance, reference/strand/endpoint controls and explicit abstention | Some questions intentionally remain unanswered |
| Code originated inside an unrelated legacy application repo | Standalone `sfs-genomics` repository with hash-verified source migration | Existing live hostnames retain legacy `lagoon` identifiers for compatibility |

These failure modes are part of the engineering evidence. A project that reports limitations accurately is more useful than a demo that silently returns plausible but unsupported results.

## Source organization and reproducibility

| Directory / file | Responsibility |
| --- | --- |
| [`genomics/web/`](genomics/web/) | Mobile UI, browser parser, local device vault, cloud upload and JavaScript tests |
| [`genomics/cloud/`](genomics/cloud/) | Cloudflare Worker, EU D1, OAuth, remote MCP and Worker regression tests |
| [`genomics/engine.py`](genomics/engine.py), [`genomics/research.py`](genomics/research.py) | Python research processing and public-source adapters |
| [`genomics/validation.py`](genomics/validation.py), [`genomics/harmonization.py`](genomics/harmonization.py), [`genomics/ld.py`](genomics/ld.py) | Scientific reference, effect orientation and LD gates |
| [`genomics/mcp.py`](genomics/mcp.py) | Local Python stdio MCP (not identical to remote MCP) |
| [`genomics/tests/`](genomics/tests/), [`genomics/web/tests/`](genomics/web/tests/) | Deterministic Python and browser tests |
| [`.github/workflows/`](.github/workflows/) | Multi-language CI and public interoperability checks |
| [`genomics/PORTFOLIO.md`](genomics/PORTFOLIO.md) | Evidence-grade engineering profile and acceptance boundary |

Run from this repository's root (Python 3.10+ and Node.js 22+):

~~~bash
python -m unittest discover -s genomics/tests -v
node --test genomics/web/tests/*.test.mjs
node --test genomics/cloud/tests/*.test.mjs
python -m genomics.parity_check
node --check genomics/cloud/worker.mjs
node --check genomics/web/cloud-upload.mjs
python -m compileall -q genomics
~~~

These tests use **synthetic fixtures**, not the operator's private genome. Optional live-source checks depend on public providers and are not a clinical benchmark.

**Verified new-repository CI runs (28 October 2026):**
- [Python 3.10/3.12 + Node core and parity checks — passed](https://github.com/SFS-Labs/sfs-genomics/actions/runs/40624781982)
- [Public scientific-provider smoke — passed](https://github.com/SFS-Labs/sfs-genomics/actions/runs/40624781921)
- [Cloud Worker health + OAuth metadata smoke — passed](https://github.com/SFS-Labs/sfs-genomics/actions/runs/40624950124)

A **successful CI run does not imply that the Worker was freshly deployed from this Git commit**. The running service has its own deployment and operational verification boundary.

## Engineering status and next acceptance gates

**Technical MVP delivered:** mobile ingestion, persistent D1, authenticated remote MCP lookup and selected external research queries have been observed in operation; source and synthetic tests are reproducible. This describes an integrated single-operator research platform, not a production multi-tenant clinical service.

**Still open:** seventh reference-validation tool's authenticated private post-quota acceptance, complete genotype-reference reconciliation, independent primary-study effect-allele harmonization, ancestry-aware LD/risk modeling, clinical calibration and reliable service capacity beyond the observed D1 free-tier quota.

**Not claimed:** whole-genome sequencing, independent clinical validation, individual disease probability, pharmacogenetic dosing guidance, automated diagnosis or guaranteed service uptime.

## Project provenance, ownership and documentation

This standalone public repository contains the Genomics implementation migrated from the historical [Lagoon development PR #1](https://github.com/SFS-Labs/lagoon/pull/1) at source commit `a794853c58f9c4efedf71c7d1856ddb764c35b27`. The **52 source blobs were individually hash-matched** during migration, and the original unrelated lending application was not brought into the new codebase. The Cloud MCP smoke workflow was subsequently renamed to reflect the independent product identity.

The existing [mobile Pages interface](https://sfs-labs.github.io/lagoon/) and Cloudflare Worker/D1/OAuth identifiers still contain `lagoon` for backwards compatibility. The repository migration deliberately did not change the running data plane, issuer or OAuth sessions.

**Engineering contribution:** independently defined scope, architecture and acceptance criteria; AI-assisted implementation coordination; reproducibility, test verification, operational diagnosis and documentation. The presence of code does not imply all modules were written manually without AI assistance.

**[Read the complete GitBook engineering case study →](https://sfs-labs.gitbook.io/sfs-labs-docs/ai-docs/featured-engineering-project-sfs-genomics/sfs-genomics)** · [Repository migration ledger](docs/REPOSITORY_MIGRATION.md) · [Detailed project README](genomics/README.md)
