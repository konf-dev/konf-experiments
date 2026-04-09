# Platform Business Models & Benchmarks

**Date:** 2026-04-09  
**Source:** Web research, public financial data

---

## Developer Infrastructure Platforms

| Company | ARR | Valuation | Team | Model |
|---------|-----|-----------|------|-------|
| Vercel | $200M+ | $9.3B | 823 | Free → Pro ($20/mo) → Enterprise + usage |
| Supabase | $70M | $5-10B | 230 | Open-source + managed cloud |
| Railway | — | $120M raised | Solo founder origin | Deployment platform |
| Render | — | $1.5B | — | Cloud hosting |
| Fly.io | $11.2M | $397M | 60 | Edge compute |

Self-hosted vs cloud split: cloud/managed is 90%+ of revenue for all. Self-hosted is the community flywheel.

## Workflow/Automation Platforms

| Company | ARR | Valuation | Key Metric |
|---------|-----|-----------|------------|
| n8n | $40M | $2.5B | 5x YoY, 75% use AI tools |
| Temporal | Undisclosed (4.4x cloud growth) | $2.5B | 184% NDR, 183K weekly OSS devs |
| Prefect | $13.1M | — | User-based pricing |
| Dagster | — | $47M raised | Credit-based pricing |

Pricing models: per execution, per compute-minute, per seat, hybrid subscription + usage.

## Open-Core Economics

| Company | Revenue | Valuation/Exit | Conversion Rate |
|---------|---------|---------------|-----------------|
| HashiCorp | ~$646M | $6.4B (IBM acquisition, ~10x) | 89% revenue from 19% of customers |
| GitLab | $759M | Public (GTLB) | 30K paying, 50%+ Fortune 100 |

Enterprise features (SSO, SAML, audit, HA, SLAs) go behind paywall. Free tier is marketing.

## AI-Specific Platforms

| Company | Revenue | Valuation | Model |
|---------|---------|-----------|-------|
| Hugging Face | $130M | $4.5B | Free + Pro ($9/mo) + Teams + Enterprise |
| W&B | $13.6M | $1.7B (CoreWeave acquisition) | ML experiment tracking |
| Modal | ~$50M ARR run rate | $1.1-2.5B | Pure usage-based compute |
| Together AI | $50M (projected $120M) | $3.3B | Per-token inference |

Path: community/adoption (1-3yr) → managed cloud → enterprise contracts → compute fees.

## Solo Founder Benchmarks

| Company/Person | Revenue | Notes |
|---------------|---------|-------|
| Plausible Analytics (2 founders) | $3.1M | Bootstrapped, open source |
| Nomad List (Pieter Levels, 1 person) | $5.3M/yr | — |
| Carrd (1 person) | $1.5M | — |
| Bannerbear (1 person) | $991K | — |

Solo founder ceiling bootstrapped: $5-10M ARR. 38% of new startups are solo-founded (2024), but receive only 14.7% of equity capital.

## The n8n Trajectory (Closest Comp to Konf)

n8n is the most relevant benchmark:
- Started as niche workflow automation
- Pivoted into AI agent orchestration
- Fair-code license (similar to BSL)
- Self-hosted free, cloud paid
- $40M ARR with 5x YoY growth
- $2.5B valuation
- 75% of customers use AI tools

Konf differentiators vs n8n: Rust kernel (performance/safety), capability-based security, self-modifying config, AI-native from ground up (not retrofitted).
