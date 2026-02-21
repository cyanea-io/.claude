# Cyanea Platform Vision

> Open infrastructure for biological computation — where researchers share, analyze, and learn from living data.

---

## North Star

Cyanea is a unified platform where biological data, computational tools, and scientific knowledge converge. Every dataset is explorable. Every algorithm is runnable in the browser. Every analysis is reproducible and shareable. We bring bioinformatics from the command line to the web without sacrificing depth or rigor.

**One-line mission:** Make biological computation accessible, reproducible, and collaborative — from student to enterprise.

---

## Platform Identity

Cyanea occupies the intersection of three established product categories:

1. **Hugging Face for biology** — versioned repositories of datasets, protocols, pipelines, and results with global discovery, model cards, and an API for programmatic access
2. **Observable/Jupyter for life sciences** — interactive notebooks and inline analysis powered by Rust compiled to WASM, executing entirely client-side with zero infrastructure
3. **Codecademy for bioinformatics** — structured learning tracks with hands-on exercises that run real algorithms in the browser, not toy simulations

No existing platform combines all three. Benchling focuses on lab workflows. Galaxy is pipeline execution. Hugging Face doesn't serve biology well. Cyanea fills the gap with a vertically integrated stack — from Rust computational kernels through WASM/Python/Elixir bindings to a Phoenix LiveView web application — all under a single open-source umbrella.

---

## User Personas & Tiers

### Personas

| Persona | Description | Primary Need |
|---------|-------------|--------------|
| **Graduate Student** | Learning bioinformatics, needs to analyze thesis data | Guided tutorials + real analysis tools |
| **Core Facility Scientist** | Runs standard pipelines for collaborators | Reproducible workflows, file format support |
| **PI / Lab Head** | Manages team, publishes data alongside papers | Collaboration, citation, data sharing |
| **Bioinformatics Engineer** | Builds custom pipelines, integrates tools | API access, extensibility, performance |
| **Pharma/Biotech Team** | Internal analysis with compliance requirements | Self-hosted federation, audit trail, SSO |

### Tiers

| Tier | Target | Capabilities |
|------|--------|-------------|
| **Free** | Students, individual researchers | Public repos, 5 GB storage, full WASM tooling, Academy access, community support |
| **Pro** | Active researchers, small labs | Private repos, 100 GB storage, priority compute, API access, advanced analytics |
| **Team** | Labs and departments | Shared namespaces, role-based access, org management, usage analytics |
| **Enterprise** | Institutions and companies | Self-hosted federation, SSO/SAML, audit logging, SLA, dedicated support, custom integrations |

---

## Unified Web Presence

### Architecture: Two Repos, One Experience

The marketing site (`www/`, Zola) and the application (`cyanea/`, Phoenix) remain separate codebases but present as a single, seamless product to users.

**Why separate:**
- The marketing site is static, deployed to Cloudflare Pages, and optimized for SEO and page speed
- The application is a stateful Phoenix LiveView app requiring PostgreSQL, S3, Meilisearch, and server-side compute
- Independent deploy cycles — marketing copy changes don't require app deploys and vice versa

**How they unify:**

| Layer | Mechanism |
|-------|-----------|
| **Design tokens** | Single `tokens.json` in `design/` generates `_tokens.scss` (Zola) and `tailwind-preset.cjs` + `tokens.css` (Phoenix). Same colors, typography, spacing, shadows everywhere. |
| **Typography** | Epilogue for display/headings, Inter for body, JetBrains Mono for code — consistent across both. |
| **Color palette** | Cyan primary (50–950), indigo accent, violet gradient endpoint, slate grays, semantic colors (success/warning/error). |
| **Navigation** | Shared header structure. Marketing nav links to app routes (`/explore`, `/login`). App nav links back to docs/blog. |
| **Transitions** | Handoff from `cyanea.bio/*` (Zola) to `cyanea.bio/app/*` or `app.cyanea.bio/*` (Phoenix) is visually seamless — same header, same footer, same feel. |

**What changes:**
- When a user clicks "Sign In" or "Get Started" on the marketing site, they transition to Phoenix LiveView
- The app uses Tailwind (via the shared preset) while the site uses SCSS (via generated variables) — both consuming the same token values
- Component patterns differ (Jinja2 templates vs LiveView function components) but visual output is identical

---

## Bioinformatics Component Library

A comprehensive set of Phoenix LiveView components and JavaScript hooks for biological data visualization and interaction. These components are the primary interface between users and the computational backend (Rust NIFs server-side, WASM client-side).

### Component Architecture

```
Phoenix Function Components (HEEx templates)
    ↓ renders
HTML + Tailwind (design tokens)
    ↓ enhanced by
JS Hooks (Canvas, WebGL, WASM interop)
    ↓ calls
@cyanea/bio WASM module (160+ functions)
    ↓ or
Rust NIFs via LiveView server events
```

**Interaction model:** Components start as server-rendered LiveView markup. For computationally intensive visualizations (structure viewing, large alignments), a JS hook takes over, calling WASM functions directly in the browser. Results flow back to LiveView via `pushEvent` for coordination with server state.

### Component Catalog

#### Sequence Components
| Component | Description | Data Source |
|-----------|-------------|-------------|
| `sequence_viewer` | Color-coded nucleotide/protein display with position numbering, selection, and search | FASTA/FASTQ text |
| `quality_plot` | Per-base quality score visualization (Phred scale) with trimming threshold overlay | FASTQ quality strings |
| `gc_content_plot` | Sliding-window GC% line chart with configurable window size | Sequence text |
| `kmer_frequency` | K-mer distribution histogram with frequency table | WASM k-mer counting |
| `orf_viewer` | Six-frame translation display with ORF highlighting and start/stop codon annotation | WASM ORF finder |
| `codon_usage_table` | Heatmap of codon frequencies with CAI calculation | WASM codon analysis |
| `dotplot` | Self-vs-self or pairwise sequence dotplot with configurable word size | WASM computation |
| `motif_logo` | Sequence logo (information-content) from PSSM/frequency matrix | WASM PSSM |
| `restriction_map` | Linear or circular map with enzyme cut sites | Pattern matching |

#### Alignment Components
| Component | Description | Data Source |
|-----------|-------------|-------------|
| `alignment_browser` | Scrollable pairwise/MSA viewer with CIGAR-aware rendering, identity coloring, consensus row | WASM NW/SW/MSA |
| `alignment_summary` | Score, identity%, gaps, CIGAR string, substitution matrix used | Alignment result |
| `dotplot_alignment` | Seed-and-extend visualization showing anchors and alignment path | WASM seed-extend |
| `cigar_diagram` | Visual CIGAR string with color-coded operations (M/I/D/N/S/H) | CIGAR parsing |
| `scoring_matrix_editor` | Interactive substitution matrix selector/editor (BLOSUM, PAM, custom) | Static data |

#### Structure Components
| Component | Description | Data Source |
|-----------|-------------|-------------|
| `structure_viewer_3d` | WebGL-based 3D protein/nucleic acid viewer (cartoon, surface, ball-and-stick) | PDB/mmCIF via WASM |
| `ramachandran_plot` | Phi/psi angle scatter plot with allowed region overlays | WASM Ramachandran |
| `contact_map` | Residue-residue distance matrix heatmap with threshold control | WASM contact maps |
| `secondary_structure` | Linear annotation track showing helix/sheet/coil from DSSP assignment | WASM DSSP |
| `structure_superposition` | Overlay two structures after Kabsch alignment, display RMSD | WASM Kabsch |

#### Genomics & Omics Components
| Component | Description | Data Source |
|-----------|-------------|-------------|
| `genome_browser` | Interval-based track viewer for BED/GFF3/VCF with zoom and pan | Parsed interval data |
| `variant_table` | Sortable/filterable variant display with consequence annotation | VCF via NIF or WASM |
| `expression_heatmap` | Clustered expression matrix with dendrograms and metadata tracks | Matrix data |
| `volcano_plot` | log2FC vs -log10(p) scatter with significance thresholds | DE results |
| `manhattan_plot` | Chromosome-wide association plot with significance lines | GWAS summary stats |
| `cnv_plot` | Log-ratio segmentation plot with CBS changepoints | WASM CNV/CBS |
| `methylation_track` | CpG methylation levels along genomic coordinates | WASM methylation |
| `spatial_plot` | Spatial transcriptomics scatter with gene expression overlay | Spatial coords + counts |

#### Statistics & ML Components
| Component | Description | Data Source |
|-----------|-------------|-------------|
| `summary_stats_card` | Mean, median, SD, quartiles, skewness, kurtosis in a compact card | WASM descriptive |
| `distribution_plot` | Histogram + fitted distribution overlay | WASM distributions |
| `correlation_matrix` | Heatmap of Pearson/Spearman/Kendall correlations | WASM correlation |
| `pca_biplot` | 2D/3D PCA scatter with loadings and variance explained | WASM PCA |
| `umap_scatter` | 2D UMAP embedding with cluster coloring and metadata overlay | WASM UMAP |
| `tsne_scatter` | 2D t-SNE embedding with perplexity control | WASM t-SNE |
| `kaplan_meier_curve` | Survival curves with confidence intervals and log-rank test | WASM survival |
| `roc_curve` | ROC curve with AUC and optimal threshold | WASM classification |
| `confusion_matrix` | Annotated confusion matrix heatmap with precision/recall/F1 | WASM metrics |
| `diversity_rarefaction` | Rarefaction curves for alpha diversity estimation | WASM diversity |

#### Chemistry Components
| Component | Description | Data Source |
|-----------|-------------|-------------|
| `molecule_viewer_2d` | 2D structure rendering from SMILES with atom labels | SMILES parsing |
| `molecule_viewer_3d` | WebGL 3D conformer display with ball-and-stick/space-filling | WASM 3D conformer |
| `property_card` | Molecular weight, formula, logP, HBA/HBD, rotatable bonds, TPSA | WASM properties |
| `fingerprint_similarity` | Tanimoto/Dice similarity heatmap for compound sets | WASM fingerprints |
| `reaction_viewer` | SMIRKS reaction display with reactant/product mapping | WASM reactions |

#### Phylogenetics Components
| Component | Description | Data Source |
|-----------|-------------|-------------|
| `tree_viewer` | Interactive phylogenetic tree (rectangular, circular, unrooted) with branch length display | Newick/NEXUS via WASM |
| `tree_comparison` | Side-by-side tree topology comparison with RF distance | WASM tree distance |
| `bootstrap_heatmap` | Branch support values with color gradient | Tree building results |
| `ancestral_states` | Character states mapped onto tree nodes | Fitch/Sankoff results |

#### Data & File Components
| Component | Description | Data Source |
|-----------|-------------|-------------|
| `file_preview` | Format-aware preview for FASTA, VCF, BED, GFF3, PDB, Newick, SMILES, SAM | WASM parsers |
| `format_converter` | Convert between supported formats with validation | WASM I/O |
| `data_table` | Sortable, filterable, paginated table for tabular data (CSV, BED, VCF) | Parsed records |
| `upload_zone` | Drag-and-drop with format detection, validation, and inline preview | Client-side detection |

---

## Interactive Learning: Cyanea Academy

### Concept

A structured bioinformatics curriculum where every concept is paired with a hands-on exercise that runs real algorithms — not pre-computed results — directly in the browser via WASM. No server infrastructure required for computation. No Docker. No conda. Just open the page and run.

### Execution Model

```
Course Page (LiveView)
    ↓ loads
Exercise Component (JS Hook)
    ↓ initializes
@cyanea/bio WASM module
    ↓ user provides input
Client-side computation (Rust → WASM)
    ↓ returns
Typed result (JSON)
    ↓ rendered by
Visualization Component
    ↓ progress tracked via
LiveView pushEvent → Server → Database
```

**Key properties:**
- Computation is entirely client-side — scales to thousands of concurrent learners with zero compute cost
- Results are deterministic and reproducible
- Exercises work offline after initial page load
- Progress, badges, and completion status are tracked server-side

### Learning Tracks

#### Track 1: Foundations of Bioinformatics
| Module | Topics | WASM Functions Used |
|--------|--------|-------------------|
| 1.1 Sequences | DNA/RNA/protein alphabets, complement, transcription, translation | `validate_dna`, `reverse_complement`, `transcribe`, `translate` |
| 1.2 File Formats | FASTA, FASTQ, quality scores, parsing | `parse_fasta`, `fastq_stats`, `fasta_stats` |
| 1.3 Sequence Statistics | GC content, k-mer frequencies, nucleotide composition | `gc_content`, `count_kmers`, `sequence_composition` |
| 1.4 Pattern Matching | Exact and approximate matching, motifs | `find_pattern`, `find_motifs`, `scan_pssm` |
| 1.5 Open Reading Frames | Start/stop codons, codon tables, ORF finding | `find_orfs`, `codon_usage`, `codon_adaptation_index` |

#### Track 2: Sequence Alignment
| Module | Topics | WASM Functions Used |
|--------|--------|-------------------|
| 2.1 Scoring | Substitution matrices, gap penalties, scoring models | `align_dna`, `align_protein` (with custom matrices) |
| 2.2 Global Alignment | Needleman-Wunsch, traceback, CIGAR | `align_dna` (NW mode), `parse_cigar` |
| 2.3 Local Alignment | Smith-Waterman, high-scoring segments | `align_dna` (SW mode), `cigar_stats` |
| 2.4 Multiple Alignment | Progressive MSA, guide trees, consensus | `msa`, `poa_consensus` |
| 2.5 Advanced | Banded, seed-and-extend, minimizers | `banded_align`, `seed_and_extend` |

#### Track 3: Genomics & Variants
| Module | Topics | WASM Functions Used |
|--------|--------|-------------------|
| 3.1 Genomic Coordinates | Intervals, BED format, overlap operations | `parse_bed`, `interval_jaccard`, `closest_intervals` |
| 3.2 Gene Annotations | GFF3/GTF, feature extraction | `parse_gff3` |
| 3.3 Variant Calling | VCF format, variant types, consequence prediction | `parse_vcf`, `annotate_variant` |
| 3.4 Population Genetics | Allele frequencies, Fst, Tajima's D, Hardy-Weinberg | `fst`, `tajimas_d` |
| 3.5 Copy Number | Segmentation, CBS algorithm | `cbs_segment` |

#### Track 4: Statistics for Biology
| Module | Topics | WASM Functions Used |
|--------|--------|-------------------|
| 4.1 Descriptive Statistics | Central tendency, spread, distributions | `descriptive_stats`, `histogram` |
| 4.2 Hypothesis Testing | t-tests, chi-square, multiple testing correction | `t_test`, `chi_square_test`, `mann_whitney_u` |
| 4.3 Correlation | Pearson, Spearman, Kendall | `correlation`, `correlation_matrix` |
| 4.4 Dimensionality Reduction | PCA, t-SNE, UMAP | `pca`, `tsne`, `umap` |
| 4.5 Survival Analysis | Kaplan-Meier, Cox regression | `kaplan_meier`, `cox_proportional_hazards` |

#### Track 5: Phylogenetics
| Module | Topics | WASM Functions Used |
|--------|--------|-------------------|
| 5.1 Distance Methods | p-distance, JC, K2P, distance matrices | `p_distance`, `jukes_cantor`, `kimura_2p` |
| 5.2 Tree Building | UPGMA, Neighbor-Joining | `upgma`, `neighbor_joining` |
| 5.3 Tree Formats | Newick, NEXUS, reading and writing | `parse_newick`, `parse_nexus`, `to_newick` |
| 5.4 Parsimony | Fitch algorithm, tree scores | `fitch_parsimony` |
| 5.5 Evaluation | Bootstrap, Robinson-Foulds distance | `bootstrap_trees`, `robinson_foulds` |

#### Track 6: Structural Biology
| Module | Topics | WASM Functions Used |
|--------|--------|-------------------|
| 6.1 Protein Structure | PDB format, coordinates, B-factors | `parse_pdb`, `parse_mmcif` |
| 6.2 Secondary Structure | DSSP assignment, helix/sheet/coil | `assign_secondary_structure` |
| 6.3 Geometry | Bond angles, Ramachandran plots | `ramachandran_analysis`, `phi_psi_angles` |
| 6.4 Comparison | RMSD, Kabsch superposition | `kabsch_rmsd`, `superpose` |
| 6.5 Contacts | Contact maps, distance matrices | `contact_map`, `distance_matrix` |

#### Track 7: Cheminformatics
| Module | Topics | WASM Functions Used |
|--------|--------|-------------------|
| 7.1 Molecular Representation | SMILES, properties, canonical forms | `parse_smiles`, `molecular_properties`, `canonical_smiles` |
| 7.2 Fingerprints | Morgan, MACCS, similarity searching | `morgan_fingerprint`, `maccs_fingerprint`, `tanimoto_similarity` |
| 7.3 Substructure | Pattern matching, pharmacophores | `substructure_search` |
| 7.4 3D Structure | Conformer generation, force fields | 3D conformer functions |
| 7.5 Reactions | SMIRKS, retrosynthesis | Reaction functions |

#### Track 8: Single-Cell Analysis
| Module | Topics | WASM Functions Used |
|--------|--------|-------------------|
| 8.1 Preprocessing | Normalization, HVG selection | NIF: `normalize`, `highly_variable_genes` |
| 8.2 Dimensionality Reduction | PCA, UMAP for single-cell | NIF + WASM: `pca`, `umap` |
| 8.3 Clustering | Leiden, Louvain, community detection | NIF: `leiden`, `louvain` |
| 8.4 Markers | Differential expression, marker genes | NIF: `rank_genes_groups` |
| 8.5 Trajectories | Diffusion maps, pseudotime, PAGA | NIF: `diffusion_map`, `dpt`, `paga` |

---

## Data Platform

### Repository Model

Repositories are versioned containers for scientific work, modeled after Git but designed for biological data. Each repository belongs to a user or organization and contains typed artifacts.

```
cyanea://cyanea.bio/zhang-lab/sars-cov-2-variants
├── dataset/spike-mutations@v2.1        (VCF, 340 MB)
├── protocol/variant-calling@v1.0       (Markdown + parameters)
├── pipeline/lineage-assignment@v3.2    (Pipeline definition)
├── result/phylogenetic-tree@v1.0       (Newick + metadata)
├── notebook/analysis-2024@v1.0         (Interactive notebook)
└── sample/wastewater-ny-2024@v1.0      (Sample metadata)
```

**Artifact types** (defined in the platform schema):
- **Dataset** — raw or processed data files (FASTA, VCF, BAM, CSV, h5ad, etc.)
- **Protocol** — documented experimental or computational methods
- **Pipeline** — reproducible analysis workflows with parameters
- **Result** — outputs from analysis (figures, tables, trees, statistics)
- **Notebook** — interactive analysis documents with embedded computation
- **Sample** — biological sample metadata with ontology terms

### Artifact Cards

Every artifact gets a card page (like Hugging Face model cards) with:

- **Header** — name, version, author, creation date, license, visibility badge, tags
- **Description** — Markdown with LaTeX math support
- **File browser** — S3-backed file listing with size, type, and inline preview
- **Inline preview** — format-aware rendering using the component library (FASTA → sequence viewer, VCF → variant table, PDB → 3D viewer, Newick → tree viewer)
- **Metadata** — structured key-value pairs, ontology terms, organism, genome build
- **Lineage** — parent artifact chain showing provenance (which dataset + pipeline produced this result)
- **Versions** — version history with diffs and changelogs
- **API access** — code snippets for programmatic access (Python, Rust, CLI, HTTP)
- **Citation** — auto-generated BibTeX/RIS with DOI (when registered)

### Inline Analysis

Users can run analysis directly on artifact data without downloading:

1. **Quick analysis** — one-click operations surfaced contextually (e.g., "Compute GC content" on a FASTA, "Run PCA" on an expression matrix)
2. **Notebook mode** — open an interactive notebook pre-loaded with the artifact data, write analysis code that executes via WASM
3. **Pipeline execution** — run a saved pipeline on the artifact via server-side Rust NIFs or dispatched compute

### API

RESTful JSON API for programmatic access:

```
GET    /api/v1/repos/:owner/:repo                    # Repository metadata
GET    /api/v1/repos/:owner/:repo/artifacts           # List artifacts
GET    /api/v1/repos/:owner/:repo/artifacts/:slug     # Artifact metadata
GET    /api/v1/repos/:owner/:repo/artifacts/:slug/files  # File listing
GET    /api/v1/repos/:owner/:repo/artifacts/:slug/download/:path  # File download
POST   /api/v1/repos/:owner/:repo/artifacts           # Create artifact
PUT    /api/v1/repos/:owner/:repo/artifacts/:slug     # Update artifact
DELETE /api/v1/repos/:owner/:repo/artifacts/:slug     # Delete artifact

POST   /api/v1/compute/align                          # Run alignment
POST   /api/v1/compute/stats                          # Run statistical analysis
POST   /api/v1/compute/ml                             # Run ML pipeline

GET    /api/v1/search?q=...&type=...&organism=...     # Full-text search
```

---

## Federation

### Model

Federation enables institutions to run private Cyanea instances while participating in a global discovery network. It is opt-in, per-artifact, and cryptographically signed.

### Architecture

```
┌─────────────────────┐       ┌─────────────────────┐
│  cyanea.bio          │       │  bio.university.edu  │
│  (public hub)        │◄─────►│  (institutional)     │
│                      │       │                      │
│  - Global discovery  │       │  - Private repos     │
│  - Public artifacts  │       │  - SSO/SAML auth     │
│  - Academy content   │       │  - Compliance/audit  │
│  - Community         │       │  - Selective publish  │
└──────────┬───────────┘       └──────────┬───────────┘
           │                              │
           │       ┌──────────────────┐   │
           └──────►│  pharma.internal  │◄──┘
                   │  (enterprise)     │
                   │                   │
                   │  - Air-gapped OK  │
                   │  - No federation  │
                   │  - Full feature   │
                   └──────────────────┘
```

### Protocol

**Identity:**
- Each node has a unique URL, display name, and Ed25519 key pair
- Node status: `pending → active → inactive/revoked`
- Global artifact IDs: `cyanea://<host>/<owner>/<repo>/<artifact>@<version>`

**Publishing flow:**
1. Author marks artifact as "federated" on their instance
2. Instance creates a signed manifest: `{global_id, content_hash (SHA-256), metadata, signature}`
3. Manifest is pushed to configured peer nodes
4. Peer nodes index the manifest for discovery (metadata only — not the full data)
5. When a user on a peer node requests the artifact, data is fetched on-demand from the origin

**Sync:**
- Manifests sync on a configurable interval
- Sync entries track state: `pending → completed | failed` with retry logic
- Content integrity verified via SHA-256 content hash
- Conflict resolution: origin node is authoritative for its artifacts

**Discovery:**
- Global search aggregates manifests from all federated nodes
- Users see unified results with source attribution
- Clicking a federated artifact either redirects to origin or renders a cached preview

**Security:**
- All inter-node communication over mTLS
- Manifest signatures verified against node's registered public key
- Nodes can revoke federation at any time (removes manifests from peers)
- Enterprise instances can operate without federation (fully self-contained)

---

## Technical Architecture

### Repository Structure

```
cyanea-io/
├── .claude/          # Shared Claude configurations, rules, this document
├── design/           # Design token source of truth
│   ├── tokens.json   # Color, typography, spacing, shadow, transition tokens
│   └── build.ts      # Deno script → SCSS + Tailwind preset + CSS vars
├── www/              # Marketing site (Zola)
│   ├── content/      # Markdown pages and blog posts
│   ├── templates/    # Jinja2 templates
│   ├── sass/         # SCSS (consumes generated _tokens.scss)
│   └── static/       # Images, fonts, favicons
├── labs/             # Rust bioinformatics library (Cargo workspace)
│   ├── cyanea-core/  # Foundation: errors, compression, crypto, data structures
│   ├── cyanea-seq/   # Sequences, formats, indexing, k-mers
│   ├── cyanea-io/    # File format I/O (VCF, BAM, GFF3, Parquet, etc.)
│   ├── cyanea-align/ # Pairwise, MSA, seed-extend, SIMD, GPU dispatch
│   ├── cyanea-omics/ # Genomic coords, variants, single-cell, spatial
│   ├── cyanea-stats/ # Statistics, hypothesis tests, survival, popgen
│   ├── cyanea-ml/    # Clustering, embeddings, classification, HMM
│   ├── cyanea-chem/  # SMILES, fingerprints, conformers, reactions
│   ├── cyanea-struct/# PDB, mmCIF, DSSP, Kabsch, contacts
│   ├── cyanea-phylo/ # Trees, distances, inference, dating
│   ├── cyanea-gpu/   # GPU backends (CUDA, Metal, WebGPU)
│   ├── cyanea-wasm/  # WASM bindings → @cyanea/bio npm package
│   └── cyanea-py/    # Python bindings → cyanea PyPI package
└── cyanea/           # Phoenix web application
    ├── lib/cyanea/         # Business logic contexts
    │   ├── accounts/       # Users, auth, ORCID
    │   ├── organizations/  # Teams, memberships
    │   ├── repositories/   # Versioned repos
    │   ├── artifacts/      # Typed scientific objects
    │   ├── federation/     # Nodes, manifests, sync
    │   ├── files/          # S3-backed storage
    │   ├── compute/        # NIF dispatch
    │   └── search/         # Meilisearch integration
    ├── lib/cyanea_web/     # Web layer
    │   ├── live/           # LiveView modules
    │   ├── components/     # Function components (Core, UI, Data, Science)
    │   └── router.ex       # Route definitions
    ├── native/cyanea_native/  # Rustler NIF crate
    └── assets/             # Tailwind preset, JS hooks, CSS tokens
```

### Data Flow

```
Browser                          Server                         Storage
───────                          ──────                         ───────

LiveView (HEEx)          ◄──── Phoenix Endpoint          ◄──── PostgreSQL
    │                              │                              (metadata,
    │ pushEvent                    │ Context modules               users,
    ▼                              ▼                               repos)
JS Hooks ──────────────── LiveView Handlers
    │                              │                        ◄──── S3
    │ @cyanea/bio                  │ Rust NIFs                    (files,
    ▼ (client-side)                ▼ (server-side)                 artifacts)
WASM computation           NIF computation
    │                              │                        ◄──── Meilisearch
    │                              │                              (search
    ▼                              ▼                               index)
JSON result ──────────►  pushEvent to LiveView
    │
    ▼
Visualization Component
```

**When computation happens where:**
- **Client (WASM):** Interactive exploration, Academy exercises, small datasets (<10 MB), real-time parameter tweaking, offline-capable analysis
- **Server (NIF):** Large datasets, file format conversion, batch operations, single-cell pipelines, anything touching S3-stored data
- **GPU (when available):** Smith-Waterman on large sequence sets, k-mer counting at scale, pairwise distance matrices, MinHash sketching

### Design System Flow

```
tokens.json (single source of truth)
    │
    ├──► _tokens.scss ──► www/ (Zola SCSS)
    │        $color-primary-700, $font-display, $radius-md, ...
    │
    ├──► tailwind-preset.cjs ──► cyanea/ (Phoenix Tailwind)
    │        colors.primary.700, fontFamily.display, borderRadius.md, ...
    │
    └──► tokens.css ──► cyanea/ (CSS custom properties)
             --color-primary-700, --font-display, --radius-md, ...
```

### WASM Integration Pattern

```javascript
// JS Hook lifecycle in Phoenix LiveView
const AlignmentHook = {
  mounted() {
    // Load WASM module once
    import("@cyanea/bio").then(bio => {
      this.bio = bio;
      // Listen for LiveView events
      this.handleEvent("run_alignment", ({ seq1, seq2, mode }) => {
        const result = this.bio.align_dna(seq1, seq2, JSON.stringify({
          mode,
          match_score: 2,
          mismatch_penalty: -1,
          gap_open: -5,
          gap_extend: -1
        }));
        // Send result back to LiveView
        this.pushEvent("alignment_result", JSON.parse(result));
      });
    });
  }
};
```

---

## Phased Roadmap

### Phase 1: Foundation (Current)

**Status:** Core infrastructure built, not yet publicly deployed.

What exists:
- [x] 13 Rust crates with 3000+ tests covering all major bioinformatics domains
- [x] WASM bindings with TypeScript types (`@cyanea/bio`, 160+ functions)
- [x] Python bindings via PyO3 with NumPy interop
- [x] Elixir NIF bindings (100+ functions)
- [x] Phoenix app with auth (ORCID), repos, artifacts, orgs, search
- [x] Federation schema (nodes, manifests, sync entries)
- [x] Component library (Core, UI, Data, Science components)
- [x] Design token system with multi-consumer generation
- [x] Marketing site (Zola) with shared design tokens
- [x] CI pipelines (check, test, clippy, fmt, doc, WASM, Python, benchmarks)

What remains:
- [ ] Publish Rust crates to crates.io
- [ ] Publish `@cyanea/bio` to npm
- [ ] Publish Python package to PyPI
- [ ] Deploy marketing site to Cloudflare Pages
- [ ] Deploy Phoenix app to production (Fly.io or similar)
- [ ] Set up production PostgreSQL, S3, Meilisearch

### Phase 2: Public Launch

**Goal:** Live platform with core data and analysis features.

- [ ] Production deployment with monitoring, logging, error tracking
- [ ] Public user registration and ORCID authentication
- [ ] Repository creation and artifact upload workflow
- [ ] File preview for all supported formats (FASTA, VCF, BED, GFF3, PDB, Newick, SMILES)
- [ ] Inline WASM analysis (one-click operations on uploaded data)
- [ ] Search with faceted filtering (type, organism, format, license)
- [ ] REST API (v1) for programmatic access
- [ ] Documentation site (guides, API reference, tutorials)
- [ ] JS hooks for WASM-powered components (sequence viewer, alignment browser, tree viewer)

### Phase 3: Academy & Interactive Analysis

**Goal:** Learning platform and notebook-style analysis.

- [ ] Academy course framework (tracks, modules, exercises, progress tracking)
- [ ] Launch Track 1 (Foundations) and Track 2 (Alignment) as initial content
- [ ] Interactive notebook component (code cells + WASM execution + visualization)
- [ ] Notebook artifacts (save, version, share analysis notebooks)
- [ ] Quick analysis actions on artifact pages
- [ ] Expanded component library (3D viewers, genome browser, volcano/Manhattan plots)
- [ ] Community features (comments, discussions, stars, forks)

### Phase 4: Federation & Enterprise

**Goal:** Decentralized network and institutional features.

- [ ] Federation protocol implementation (manifest exchange, discovery sync)
- [ ] Self-hosted deployment package (Docker Compose + Helm chart)
- [ ] SSO/SAML integration for enterprise instances
- [ ] Audit logging and compliance features
- [ ] Node management UI (register peers, monitor sync, revoke access)
- [ ] Global search across federated instances
- [ ] Enterprise tier with SLA and dedicated support

### Phase 5: Advanced Platform

**Goal:** Full-featured scientific data platform.

- [ ] DOI registration for artifacts (DataCite integration)
- [ ] Pipeline execution engine (server-side Rust NIF dispatch with job queue via Oban)
- [ ] GPU-accelerated compute for registered users (WebGPU client-side, CUDA/Metal server-side)
- [ ] Advanced single-cell analysis UI (full Scanpy-equivalent workflow in browser)
- [ ] Spatial transcriptomics visualization
- [ ] Remaining Academy tracks (Genomics, Statistics, Phylogenetics, Structural, Cheminformatics, Single-Cell)
- [ ] Mobile-responsive component library
- [ ] Plugin/extension system for community-contributed tools
- [ ] Webhook and integration framework (Slack, email, CI/CD triggers)

---

## Key Design Principles

1. **Computation at the edge** — Use WASM for client-side analysis wherever possible. The browser is the compute platform. Server resources are for storage, coordination, and heavy workloads.

2. **Format-native** — Don't force users to convert data. Support every major bioinformatics format natively. Preview, parse, and analyze VCF, BAM, FASTA, PDB, Newick, SMILES, h5ad, and more without conversion steps.

3. **Progressive complexity** — A student uploading their first FASTA file and a bioinformatics engineer building a population genetics pipeline should both feel at home. Surface simple actions by default, reveal advanced options on demand.

4. **Reproducibility by default** — Every analysis records its inputs, parameters, algorithm version, and outputs. Artifact lineage is first-class. Federation ensures results can be independently verified.

5. **Open core** — All computational libraries (labs/), design tokens (design/), and the marketing site (www/) are open source under Apache-2.0. The platform application (cyanea/) is source-available. Enterprise features (SSO, audit, federation management) are commercially licensed.

6. **One design system** — A single `tokens.json` governs every pixel across marketing site, web application, documentation, and component library. Visual consistency is not aspirational — it is mechanically enforced.
