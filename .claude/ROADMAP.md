# Cyanea Platform Roadmap

> Prioritized execution plan for the Cyanea platform. See [VISION.md](./VISION.md) for strategic context.

Last updated: 2026-02-21

---

## Prioritization Rationale

The ordering follows one principle: **unblock the next thing**. You can't launch a platform on libraries that aren't published. You can't attract users to a site that isn't deployed. You can't build an academy without live components. Each phase produces a usable artifact and unblocks the next.

```
Publish libraries ──► Deploy sites ──► Core platform ──► Academy ──► Federation
     (weeks)           (days)          (months)         (months)      (months)
```

---

## Phase 0: Publish Libraries

**Priority: Immediate — everything depends on this.**

The 13 Rust crates, npm package, and Python package are the foundation. Nothing else ships until these are public and installable.

### crates.io (labs/)

- [ ] Audit `Cargo.toml` metadata across all 13 crates (description, repository, license, keywords, categories, readme)
- [ ] Resolve publish order (dependency DAG): `cyanea-core` → `cyanea-seq`, `cyanea-align`, `cyanea-stats`, `cyanea-ml`, `cyanea-chem`, `cyanea-struct` → `cyanea-omics`, `cyanea-phylo` → `cyanea-gpu`, `cyanea-io` → `cyanea-wasm`, `cyanea-py`
- [ ] Run `cargo publish --dry-run` for each crate in order
- [ ] Publish to crates.io
- [ ] Set up GitHub Actions release workflow (version bump → tag → publish)

### npm (@cyanea/bio)

- [ ] Verify `package.json` metadata (name, version, description, repository, license, types, files)
- [ ] Build WASM with `wasm-pack build --release`
- [ ] Test TypeScript wrapper in a clean `npm install` environment
- [ ] Publish to npm
- [ ] Add npm publish step to release workflow

### PyPI (cyanea)

- [ ] Verify `pyproject.toml` / maturin config
- [ ] Build wheels for Linux (manylinux), macOS (arm64 + x86_64), Windows
- [ ] Test `pip install` in clean venvs (Python 3.10–3.14)
- [ ] Publish to PyPI
- [ ] Add maturin publish to release workflow

### Release automation

- [ ] Finalize `.github/workflows/release.yml` (already drafted in labs/)
- [ ] Workspace version bumping strategy (workspace-level version or per-crate)
- [ ] Changelog generation (git-cliff or similar)
- [ ] GitHub Releases with auto-generated notes

---

## Phase 1: Deploy Web Presence

**Priority: High — establishes the project publicly. Minimal effort since both sites are already built.**

### Marketing site (www/ → cyanea.bio)

- [ ] Configure Cloudflare Pages project
- [ ] Set up `cyanea.bio` DNS (Cloudflare)
- [ ] Review and finalize homepage copy, features page, pricing page
- [ ] Add documentation section linking to crate docs (docs.rs) and API references
- [ ] Set up blog with initial launch post
- [ ] Configure analytics (Plausible or Cloudflare Web Analytics — no Google)
- [ ] SSL, headers, caching, redirects

### Application (cyanea/ → app.cyanea.bio)

- [ ] Choose hosting (Fly.io recommended: Elixir-native, global edge, easy scaling)
- [ ] Provision production PostgreSQL (Fly Postgres or Neon)
- [ ] Provision S3-compatible storage (Cloudflare R2 — free egress, same edge network)
- [ ] Provision Meilisearch (Meilisearch Cloud or self-hosted on Fly)
- [ ] Configure production `config/runtime.exs` (secrets, endpoints, S3 bucket)
- [ ] Set up ORCID OAuth credentials for production
- [ ] Deploy with `fly launch` / `fly deploy`
- [ ] Configure `app.cyanea.bio` DNS and SSL
- [ ] Set up error tracking (Sentry) and uptime monitoring
- [ ] Smoke test: registration, login, create repo, upload file, search

---

## Phase 2: Core Platform Features

**Priority: High — the minimum viable product for researchers.**

### 2a: File Preview & Inline Analysis (first — this is the differentiator)

- [ ] JS hook infrastructure: WASM module loader, event bridge between hooks and LiveView
- [ ] Sequence viewer component (FASTA/FASTQ → color-coded display with position numbering)
- [ ] Alignment browser component (pairwise/MSA rendering with CIGAR-aware coloring)
- [ ] Variant table component (VCF → sortable/filterable table with consequence column)
- [ ] Tree viewer component (Newick → rectangular/circular phylogenetic tree, JS Canvas or SVG)
- [ ] Structure viewer component (PDB → 3D WebGL viewer, integrate or build on 3Dmol.js/NGL)
- [ ] Molecule viewer component (SMILES → 2D structure rendering)
- [ ] Generic data table component (CSV/BED/GFF3 → paginated table)
- [ ] Format detection on upload (file extension + magic bytes → assign preview component)
- [ ] One-click analysis actions on artifact pages:
  - FASTA: GC content, k-mer frequencies, ORF finding
  - VCF: variant summary statistics, allele frequency distribution
  - Alignment: identity %, gap statistics, CIGAR summary
  - Expression matrix: PCA, basic descriptive stats

### 2b: Repository & Artifact Workflow

- [ ] Repository creation flow (name, description, visibility, license, tags)
- [ ] Artifact upload flow (drag-and-drop, format detection, metadata form)
- [ ] Artifact versioning (semantic versions, version history, diff view)
- [ ] Artifact lineage tracking (link result to source dataset + pipeline)
- [ ] File browser for multi-file artifacts (tree view, click-to-preview)
- [ ] Markdown rendering for artifact descriptions (with LaTeX math via KaTeX)
- [ ] Download endpoints (individual files, full artifact as zip/tar)
- [ ] Visibility controls (public, private, unlisted)

### 2c: Search & Discovery

- [ ] Meilisearch indexing for repos and artifacts (on create/update hooks)
- [ ] Faceted search: type, organism, format, license, tags, author
- [ ] Explore page with trending/recent/featured sections
- [ ] User and organization profile pages
- [ ] Tag system with auto-suggest (ontology-backed where possible)

### 2d: REST API (v1)

- [ ] JSON API controllers for repos, artifacts, files, search
- [ ] Token-based authentication (API keys in user settings)
- [ ] Rate limiting (ExRateLimiter or plug-based)
- [ ] OpenAPI spec generation
- [ ] API documentation page on marketing site
- [ ] Python/Rust SDK examples in docs

---

## Phase 3: Academy & Interactive Learning

**Priority: Medium — growth driver. Requires Phase 2 components as building blocks.**

### Framework

- [ ] Course/track/module/exercise data model (Ecto schemas)
- [ ] Exercise LiveView: description pane + code editor + output/visualization pane
- [ ] WASM execution sandbox in JS hook (load `@cyanea/bio`, run function, return result)
- [ ] Progress tracking (per-user completion state, badges, streaks)
- [ ] Exercise validation (expected output comparison, partial credit for approximate matches)

### Initial Content

- [ ] **Track 1: Foundations of Bioinformatics** (5 modules, ~25 exercises)
  - Sequences, file formats, statistics, pattern matching, ORFs
- [ ] **Track 2: Sequence Alignment** (5 modules, ~25 exercises)
  - Scoring, global, local, multiple, advanced methods
- [ ] **Track 4: Statistics for Biology** (5 modules, ~25 exercises)
  - Descriptive stats, hypothesis testing, correlation, dimensionality reduction, survival

*Tracks 1, 2, and 4 launch first because they use the most mature WASM functions and require no server-side computation.*

### Expansion content (after initial launch)

- [ ] Track 3: Genomics & Variants
- [ ] Track 5: Phylogenetics
- [ ] Track 6: Structural Biology
- [ ] Track 7: Cheminformatics
- [ ] Track 8: Single-Cell Analysis (requires NIF compute, not pure WASM)

---

## Phase 4: Community & Collaboration

**Priority: Medium — retention and network effects.**

- [ ] Comments on artifacts and repos (threaded, Markdown)
- [ ] Stars and forks (fork = clone repo to your namespace)
- [ ] Activity feed (dashboard shows followed users/orgs activity)
- [ ] Notifications (in-app + email digest)
- [ ] Organization roles and permissions (admin, member, viewer)
- [ ] Citation generation (BibTeX/RIS with auto-populated metadata, optional DOI via DataCite)
- [ ] Embeddable widgets (embed a sequence viewer or tree on external sites via iframe/script tag)

---

## Phase 5: Federation & Enterprise

**Priority: Lower — institutional adoption requires a proven public platform first.**

### Federation protocol

- [ ] Node registration API (exchange public keys, establish trust)
- [ ] Manifest publishing (sign artifact metadata, push to peers)
- [ ] Manifest ingestion (verify signature, index for discovery)
- [ ] Sync worker (Oban job: periodic manifest exchange with retry)
- [ ] Global search aggregation (query local index + federated manifest index)
- [ ] On-demand data fetch (pull artifact files from origin node when requested)
- [ ] Node management UI (LiveView: register peers, view sync status, revoke)

### Enterprise features

- [ ] Self-hosted deployment package (Docker Compose for small, Helm chart for k8s)
- [ ] SSO/SAML integration (Ueberauth strategy)
- [ ] Audit logging (structured event log for compliance)
- [ ] Usage analytics dashboard (storage, compute, users, repos)
- [ ] Custom branding (logo, colors via token override)

---

## Phase 6: Advanced Platform

**Priority: Future — depth features for power users.**

- [ ] Pipeline execution engine (define multi-step analysis workflows, dispatch via Oban to NIF workers)
- [ ] GPU compute integration (WebGPU client-side for interactive, CUDA/Metal server-side for batch)
- [ ] Interactive notebooks (code cells with WASM execution, save as notebook artifacts)
- [ ] Advanced single-cell UI (Scanpy-equivalent workflow: load h5ad → normalize → HVG → PCA → UMAP → cluster → markers)
- [ ] Spatial transcriptomics visualization (coordinate scatter + gene overlay)
- [ ] Genome browser component (multi-track interval viewer with zoom/pan, Canvas-based)
- [ ] Manhattan/volcano/MA plot components
- [ ] Plugin system (user-contributed analysis tools, WASM-sandboxed)
- [ ] Webhook framework (trigger on artifact create/update, notify Slack/email/CI)
- [ ] Mobile-responsive component library

---

## Cross-Cutting: Claude Configurations

*Ongoing alongside all phases — improves development velocity.*

- [x] Repository structure and core CLAUDE.md
- [x] Rule files: Elixir, Phoenix, testing, git, security
- [ ] Rust development rules (labs crate patterns, feature flags, error handling)
- [ ] Database/Ecto patterns
- [ ] LiveView component guidelines (especially for science components and JS hooks)
- [ ] API design conventions
- [ ] Integration: submodule or sync script to propagate rules to cyanea/ and labs/

---

## What To Do Right Now

If you're sitting down to work today, here's the stack-ranked list:

1. **Finalize and ship the release workflow** (`labs/.github/workflows/release.yml`) — this is already drafted, finish it
2. **`cargo publish --dry-run` all 13 crates** — find and fix any metadata issues, version conflicts, or missing fields
3. **Publish `cyanea-core` to crates.io** — the first domino; everything else depends on it
4. **Publish remaining crates** in dependency order
5. **Build and publish `@cyanea/bio` to npm** — unlocks all client-side WASM work
6. **Deploy `www/` to Cloudflare Pages** — instant web presence, zero ongoing cost
7. **Write a launch blog post** — announce the libraries, link to docs.rs, npm, PyPI
8. **Set up Fly.io for the Phoenix app** — `fly launch`, configure secrets, provision Postgres + R2
9. **Build the WASM hook infrastructure** in Phoenix — this is the key integration layer between LiveView and `@cyanea/bio`
10. **Build the first three components** — sequence viewer, alignment browser, tree viewer — these demonstrate the platform's core value proposition

---

## Principles

1. **Ship the libraries first** — they have standalone value and validate the entire stack
2. **One deployment at a time** — static site, then app, then federation nodes
3. **Components before pages** — build the science components, then compose them into features
4. **Content follows tooling** — don't write Academy courses until the exercise framework works
5. **Federation is earned** — prove the model on the public hub before enabling decentralization
6. **Keep the design system mechanical** — every visual change flows from `tokens.json`, never from ad-hoc CSS
