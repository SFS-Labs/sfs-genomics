# Repository provenance and deployment boundary

Source: [SFS-Labs/lagoon PR #1](https://github.com/SFS-Labs/lagoon/pull/1), commit a794853c58f9c4efedf71c7d1856ddb764c35b27.
Target: [SFS-Labs/sfs-genomics](https://github.com/SFS-Labs/sfs-genomics).

The initial migration reproduced all 52 source blobs (48 genomics/ files and four GitHub Actions workflows) at matching paths and SHA hashes. Subsequently, the former lagoon-cloud-smoke.yml was renamed to sfs-genomics-cloud-smoke.yml and its workflow title/path trigger updated for clearer standalone branding. No old Lagoon lending-app sources were carried forward.

Existing Cloudflare Worker, D1 data, OAuth issuer, authorization sessions and GitHub Pages URL were left unchanged. GitHub Actions results for this source tree verify code checks, not that the cloud Worker was redeployed from this repository.

Technical limits and project evidence remain in genomics/README.md, genomics/PORTFOLIO.md and genomics/SCIENTIFIC_VALIDATION_PROTOCOL.md.
