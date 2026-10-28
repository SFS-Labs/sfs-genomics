# SFS Genomics
### AI-Native Genomic Intelligence Platform

**Mobile-first data engineering · Cloudflare EU D1 · OAuth-secured remote MCP · evidence-aware research tools**

[**Live mobile demo**](https://sfs-labs.github.io/lagoon/) · [**GitBook engineering case study**](https://sfs-labs.gitbook.io/sfs-labs-docs/ai-docs/featured-engineering-project-sfs-genomics/sfs-genomics) · [**Source**](genomics/) · [**CI / test runs**](https://github.com/SFS-Labs/sfs-genomics/actions) · [**Scientific validation protocol**](genomics/SCIENTIFIC_VALIDATION_PROTOCOL.md)

## Problem and engineering solution

Consumer SNP-array exports are not authenticated research APIs and do not provide clinically validated interpretation. **SFS Genomics** integrates an iPhone-first data ingestion path, persistent storage, OAuth-gated AI access, external scientific sources and conservative inference controls.

The workflow connects **mobile ingestion → deterministic validation and storage → authenticated ChatGPT MCP tools → GWAS/Ensembl research → explicit scientific evidence limits**. Implementation was developed with AI assistance and independently checkable tests.

## Documented acceptance evidence

| Engineering area | Observed result |
| --- | --- |
| Mobile ingestion | **609,346** input lines; **607,051** accepted SNP calls; 1,516 no-calls and 779 rejected records |
| Persistent data | Cloudflare EU D1 reconciliation: **256** distinct partitions totaling **607,051** stored calls |
| AI / MCP integration | Recorded authenticated ChatGPT status and lookup calls; OAuth PKCE/DCR interoperability checked in CI |
| Scientific source federation | GWAS Catalog studies and Ensembl reference-population LD responses for selected public queries |
| Validation controls | GRCh37 coordinates, alleles, strand ambiguity and abstention rules; five reviewed public reference loci and synthetic test fixtures |
| Reliability | Explicit D1 daily quota-error handling; public diagnostics and versioned known limitations |

**Interpretation:** These are engineering acceptance checkpoints, not a live SLA, complete genomic reference validation or a clinically validated risk model.

## System architecture

~~~text
Consumer SNP-array export on iPhone
  → mobile parser, malformed/no-call filtering
  → deterministic 256-partition upload
  → authenticated Cloudflare Worker
  → EU-hosted persistent Cloudflare D1
  → OAuth discovery, PKCE and dynamic client registration
  → remote MCP tools in ChatGPT
  → public GWAS Catalog / Ensembl reference queries
  → explicit genomic-reference, evidence and inference gates
~~~

## Codebase and documentation

- [genomics/web/](genomics/web/) — responsive interface, mobile parser, local vault, cloud upload and browser tests
- [genomics/cloud/](genomics/cloud/) — Worker, OAuth, D1 and remote MCP implementation; synthetic Node.js regressions
- [genomics/](genomics/) — Python research, statistics, reference alignment and local MCP modules
- [genomics/tests/](genomics/tests/) — Python regression tests and deterministic fixtures
- [.github/workflows/](.github/workflows/) — GitHub Actions checks, public research probes and OAuth discovery validation
- [genomics/PORTFOLIO.md](genomics/PORTFOLIO.md) — engineering acceptance record
- [genomics/README.md](genomics/README.md) — implementation details and limits
- [Scientific validation protocol](genomics/SCIENTIFIC_VALIDATION_PROTOCOL.md) — evidence, strand, LD, endpoint and abstention criteria

## Reproduce the tests

From the repository root (Python 3.10+, Node.js 22+):

~~~bash
python -m unittest discover -s genomics/tests -v
node --test genomics/web/tests/*.test.mjs
node --test genomics/cloud/tests/*.test.mjs
python -m genomics.parity_check
node --check genomics/cloud/worker.mjs
python -m compileall -q genomics
~~~

Synthetic fixtures require **no personal genomic data**. Some optional live-source CI checks depend on third-party availability and are diagnostic rather than clinical validation.

## Current boundaries

- **Demonstrated:** large-file mobile ingestion, persistent EU D1 storage, authenticated MCP lookup, selected public source adapters and bounded reference-quality gates.
- **Not completed:** full independent harmonization of all 607k calls; complete study effect-allele validation; ancestry-calibrated clinical risk models or treatment recommendations.
- **Operational constraint:** D1 Free daily row-read quotas can temporarily block lookups; errors are surfaced instead of interpreted as missing variants.
- **Data:** source and synthetic tests are public; raw genome data, private lookup outputs and OAuth credentials are not distributed here.

## Independent repository and deployment continuity

This source tree was migrated unchanged from the Genomics implementation in [SFS-Labs/lagoon PR #1](https://github.com/SFS-Labs/lagoon/pull/1), frozen at commit a794853c58f9c4efedf71c7d1856ddb764c35b27. The old Lagoon repository also hosts an unrelated historical application; this repository separates the Genomics project for technical and recruiting clarity.

The live Pages address and Cloudflare Worker/D1/OAuth identities retain legacy names **for compatibility**. Source migration is not a new deployment: the running services and existing OAuth sessions are not moved or reconfigured.

[Full architecture, research methodology, verification and implementation history → GitBook](https://sfs-labs.gitbook.io/sfs-labs-docs/ai-docs/featured-engineering-project-sfs-genomics/sfs-genomics)
