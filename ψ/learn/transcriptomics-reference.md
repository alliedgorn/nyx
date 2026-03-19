# Transcriptomics — Comprehensive Technical Reference

> Compiled: 2026-03-19 | Nyx Recon

---

## 1. What Is Transcriptomics

### Definition

Transcriptomics is the study of the **transcriptome** — the complete set of RNA transcripts (mRNA, non-coding RNA, microRNA, etc.) produced by the genome of a cell, tissue, or organism at a given point in time. The term "transcriptome" was coined by Charles Auffray in 1996.

Unlike the genome, which is largely static, the transcriptome is **dynamic** — it changes with cell type, developmental stage, environmental conditions, and disease state. This makes it a powerful readout of cellular activity.

### The Central Dogma and Where Transcriptomics Fits

The central dogma of molecular biology describes information flow:

```
DNA  →  RNA  →  Protein
     (transcription)  (translation)
```

- **Genomics** studies the DNA blueprint (causative mechanisms, mutations, structural variants)
- **Transcriptomics** captures the RNA layer — which genes are active, how much they're expressed, and which isoforms are produced
- **Proteomics** studies the protein products — the functional effectors
- **Metabolomics** studies the small-molecule metabolites — the downstream biochemical outputs

Transcriptomics sits at the critical junction between genotype and phenotype. It reveals *which* parts of the genome are being used and *how much*, providing a snapshot of cellular state that genomics alone cannot capture.

### Why the Transcriptome Matters

- Gene expression varies dramatically across cell types despite identical genomes
- Alternative splicing generates multiple protein isoforms from single genes (human genes produce ~7 transcripts on average)
- Non-coding RNAs (lncRNAs, miRNAs) regulate gene expression without producing protein
- Disease states (cancer, neurodegeneration, infection) produce distinct transcriptomic signatures
- Drug responses are reflected in transcriptomic changes before phenotypic changes become visible
- Pervasive transcription of the genome has challenged simplistic interpretations of the central dogma

---

## 2. Key Technologies

### 2.1 Microarrays

**Historical significance:** Microarrays were the cornerstone of transcriptome profiling from the late 1990s through the early 2000s. They enabled the first genome-wide expression studies and drove foundational discoveries in cancer classification, developmental biology, and pharmacogenomics.

**How they work:**
1. Known DNA sequences (probes) are fixed to a solid surface (chip) at defined positions
2. mRNA from a sample is extracted, reverse-transcribed to cDNA, and fluorescently labeled
3. Labeled cDNA hybridizes to complementary probes on the chip
4. Fluorescence intensity at each position indicates relative expression level of that gene

**Key platforms:** Affymetrix GeneChip, Agilent, Illumina BeadArray

**Limitations:**
- Can only measure genes for which probes are designed (no novel transcript discovery)
- Background hybridization limits accuracy for low-abundance transcripts
- Signal saturation at the high end compresses dynamic range (~10^3)
- Cross-hybridization between similar sequences causes noise
- Species-specific probe design required

**Current status:** As of 2023, microarray data comprised only ~15% of new submissions to GEO, with RNA-seq at ~85%. Microarrays remain useful for legacy dataset comparisons and cost-effective screening in some contexts.

---

### 2.2 Bulk RNA-Seq

RNA sequencing (RNA-seq) replaced microarrays as the dominant transcriptomic technology by providing digital, unbiased measurement of RNA abundance.

**Library Preparation:**
1. **RNA extraction** — Total RNA isolated from sample; mRNA enriched via poly-A selection or ribosomal RNA depletion
2. **Fragmentation** — RNA or cDNA fragmented to 200–500 bp
3. **Reverse transcription** — RNA converted to cDNA
4. **Adapter ligation** — Sequencing adapters attached to fragment ends
5. **PCR amplification** — Library amplified (introduces potential PCR duplicates)
6. **Size selection** — Fragments selected for optimal sequencing length

**Sequencing platforms:**
- **Illumina** (dominant) — Short reads (50–300 bp), high throughput, low error rate (~0.1%). Platforms: NovaSeq X (up to 16B reads), NextSeq 2000, MiSeq. Sequencing-by-synthesis (SBS) chemistry
- **MGI/BGI** — DNBSEQ platform, competitive pricing, comparable quality
- **Element Biosciences** — AVITI platform, emerging competitor
- **Ultima Genomics** — Silicon-based flow cell, ultra-low-cost sequencing

**Read configurations:**
- Single-end (SE): One end sequenced. Cheaper, sufficient for gene-level quantification
- Paired-end (PE): Both ends sequenced. Better for isoform detection, structural variant calling, alignment accuracy

**Typical experiment:** 20–30 million paired-end 150 bp reads per sample for differential expression; deeper for transcript discovery.

**Advantages over microarrays:**
- No probe design needed — detects novel transcripts, gene fusions, SNVs
- Broader dynamic range (>10^5 vs ~10^3)
- Superior detection of low-abundance transcripts
- Quantifies alternative splicing and isoform usage
- Not species-specific — works for any organism with or without a reference genome

---

### 2.3 Single-Cell RNA-Seq (scRNA-seq)

Bulk RNA-seq measures average expression across millions of cells, masking cellular heterogeneity. scRNA-seq resolves individual cell transcriptomes.

**Why single-cell resolution matters:**
- Tissues contain dozens to hundreds of cell types
- Rare cell populations (stem cells, tumor-initiating cells) are invisible in bulk
- Cell state transitions (differentiation, activation) occur at single-cell level
- Tumor heterogeneity drives drug resistance

**Major platforms:**

**10x Genomics Chromium (droplet-based):**
- Cells encapsulated in nanoliter droplets (GEMs — Gel Beads-in-Emulsion) with barcoded gel beads
- Each gel bead carries oligonucleotides with: (1) PCR handle, (2) 16-bp cell barcode, (3) 12-bp UMI, (4) poly-dT capture sequence
- Captures 500–10,000+ cells per run
- 3' or 5' end counting (not full-length)
- GEM-X technology (latest generation) improves cell capture efficiency and reduces doublets
- Most widely used platform — ~80% of published scRNA-seq studies

**Drop-seq:**
- Open-source predecessor to 10x
- Similar droplet microfluidics approach
- Barcoded beads with UMIs
- Lower per-cell capture efficiency than 10x

**Smart-seq2 / Smart-seq3 (plate-based):**
- Full-length transcript coverage (not 3'/5' biased)
- FACS-sorted individual cells into 96/384-well plates
- Detects more genes per cell, especially low-abundance transcripts
- Better for alternative splicing analysis
- Lower throughput (hundreds of cells vs thousands)
- Higher cost per cell

**Cell barcoding and UMIs:**
- **Cell barcode**: Unique sequence identifying which cell a read came from. All transcripts from one cell share the same barcode
- **UMI (Unique Molecular Identifier)**: Random sequence tagging each original mRNA molecule before amplification. Eliminates PCR duplicate artifacts and enables absolute molecule counting
- Together, barcodes + UMIs allow deconvolution of pooled sequencing data back to single-cell, single-molecule resolution

---

### 2.4 Spatial Transcriptomics

scRNA-seq requires tissue dissociation, destroying spatial context. Spatial transcriptomics preserves the physical location of gene expression within tissue.

**Two major strategies:**

#### Sequencing-based (in situ capture):

**10x Visium / Visium HD:**
- Tissue section placed on a slide with barcoded capture spots
- Original Visium: ~5,000 spots of 55 μm diameter (2–10 cells per spot)
- Visium HD: 2 μm resolution — approaching single-cell
- Whole-transcriptome coverage (unbiased)
- Compatible with fresh frozen and FFPE samples
- Barcoded probes capture mRNA, which is then sequenced

**Slide-seq / Slide-seq V2:**
- 10 μm barcoded beads on a slide surface
- Near-single-cell resolution
- Whole-transcriptome readout
- Academic protocol (Macosko lab)

#### Imaging-based (in situ hybridization):

**MERFISH (Multiplexed Error-Robust FISH):**
- Developed by Xiaowei Zhuang (Harvard)
- Combinatorial FISH with error-correcting barcodes
- Subcellular resolution
- Hundreds to thousands of genes per experiment
- Now commercialized by Vizgen (MERSCOPE platform)

**10x Xenium:**
- In situ hybridization + padlock probe amplification
- Subcellular resolution
- Up to 5,000 genes + multiplexed protein detection
- High sensitivity and specificity for individual RNA molecules
- Rapid adoption in clinical and pharmaceutical research

**Key tradeoffs:**
| Feature | Visium/Slide-seq | MERFISH/Xenium |
|---------|-----------------|----------------|
| Genes detected | Whole transcriptome | Targeted panel (100s–5,000) |
| Resolution | Spot-level to near-single-cell | Subcellular |
| Throughput | High | Moderate |
| Tissue prep | Section-based | Section-based |

---

### 2.5 Long-Read RNA-Seq

Short-read sequencing (Illumina) fragments transcripts, making isoform reconstruction computationally challenging. Long-read platforms sequence full-length or near-full-length transcripts.

**PacBio (Pacific Biosciences):**
- **Iso-Seq / Kinnex (MAS-Seq)**: Full-length transcript sequencing
- HiFi reads: 10–25 kb, >99.9% accuracy (after CCS correction)
- Kinnex concatenates multiple cDNAs per read, increasing throughput ~16x
- Gold standard for isoform cataloguing
- Revio system: high throughput long-read platform

**Oxford Nanopore Technologies (ONT):**
- Direct RNA sequencing (no reverse transcription — detects RNA modifications directly)
- PCR-cDNA sequencing (higher throughput)
- Read lengths: thousands to >100 kb
- Lower per-read accuracy (~95–99%) but rapidly improving
- Real-time sequencing — adaptive sampling possible
- Lower instrument cost, portable (MinION)
- PromethION for high-throughput applications

**Key advantages of long-read RNA-seq:**
- Resolves full-length transcript isoforms without computational assembly
- Detects novel isoforms, fusion transcripts, and complex splicing events
- Direct RNA mode (ONT) detects RNA modifications (m6A, pseudouridine)
- Better representation of repetitive regions and highly similar gene families

**2025 benchmarks:**
- A Nature Methods systematic benchmark (2025) profiled seven human cell lines with five RNA-seq protocols, finding long-read RNA-seq more robustly identifies major isoforms
- LongBench (2025) showed high concordance at gene level across platforms but reduced consistency for transcript-level analyses due to length- and platform-dependent biases
- New tools emerging: IsoQuant for isoform discovery, Longcell for single-cell long-read isoform quantification

---

## 3. Data Analysis Pipeline

### 3.1 Quality Control

**Tools:**
- **FastQC**: Reports per-base quality scores, GC content, adapter contamination, sequence duplication, overrepresented sequences
- **MultiQC**: Aggregates QC reports across samples into a single report

**Trimming:**
- **Trimmomatic**: Removes adapters, low-quality bases, short reads
- **fastp**: All-in-one QC + trimming (faster, modern alternative)
- **Trim Galore**: Wrapper around Cutadapt + FastQC
- **BBDuk**: Part of BBTools suite

**Key QC metrics:**
- Per-base sequence quality (Phred scores >30 ideal)
- Adapter content (<5%)
- GC content distribution (should match species expectation)
- Sequence duplication levels
- Mapping rate (>80% for well-prepared libraries)

---

### 3.2 Alignment

**Genome alignment (splice-aware):**

- **STAR (Spliced Transcripts Alignment to a Reference)**:
  - Most widely used RNA-seq aligner
  - Seed-based, splice-aware alignment
  - Very fast but memory-intensive (~30 GB RAM for human genome)
  - Can output quantification simultaneously (--quantMode GeneCounts)
  - Used by ENCODE, nf-core/rnaseq pipelines

- **HISAT2**:
  - Successor to TopHat/Bowtie
  - Graph-based FM index with hierarchical indexing
  - Lower memory footprint than STAR (~8 GB)
  - Slightly slower but more memory-efficient

- **minimap2**:
  - Primarily for long-read alignment (PacBio, ONT)
  - Also supports short reads
  - Splice-aware mode for RNA-seq (-ax splice)

**Pseudoalignment (transcriptome-based):**

- **Salmon**:
  - Quasi-mapping to transcriptome (not genome)
  - Orders of magnitude faster than genome alignment
  - Directly outputs transcript-level quantification (TPM)
  - Built-in GC bias and sequence-specific bias correction
  - Selective alignment mode improves accuracy

- **kallisto**:
  - k-mer-based pseudoalignment
  - Extremely fast (minutes for a typical experiment)
  - Transcript-level quantification
  - Produces bootstrap estimates for uncertainty quantification

**Genome alignment vs. pseudoalignment:**
- Pseudoaligners (Salmon, kallisto) are faster and produce transcript-level counts directly
- Genome aligners (STAR, HISAT2) are needed for: novel transcript discovery, variant calling, visualization in genome browsers, and some regulatory analyses
- Pseudoaligners show slightly better performance in calling differentially expressed spike-in transcripts
- High concordance within method categories (Salmon vs kallisto: r = 0.98–0.99; HISAT2 vs STAR: r = 0.95–0.96) but lower cross-category agreement (r = 0.68–0.72)

---

### 3.3 Quantification

**From genome alignments:**
- **featureCounts** (Subread package): Assigns aligned reads to genomic features (genes/exons). Fast, widely used
- **HTSeq-count**: Python-based read counting. Slower but flexible
- **RSEM**: Probabilistic quantification at gene and isoform level from genome-aligned reads

**From pseudoalignment:**
- Salmon and kallisto directly output transcript-level abundance estimates
- **tximport** (R/Bioconductor): Converts transcript-level estimates to gene-level counts for use with DESeq2/edgeR

---

### 3.4 Normalization

Raw read counts are biased by sequencing depth and gene length. Normalization corrects these biases.

**Within-sample normalization (for comparing genes within a sample):**

- **RPKM (Reads Per Kilobase per Million mapped reads)**:
  - Normalizes for gene length and sequencing depth
  - Formula: RPKM = (read count × 10^9) / (total mapped reads × gene length)
  - Used for single-end data

- **FPKM (Fragments Per Kilobase per Million)**:
  - Same as RPKM but for paired-end data (counts fragments, not reads)
  - One fragment = one cDNA molecule = two reads

- **TPM (Transcripts Per Million)**:
  - Normalizes by gene length first, then by sequencing depth
  - TPM values sum to 1 million per sample — makes cross-sample comparison more consistent than FPKM
  - Preferred over RPKM/FPKM for most applications

- **CPM (Counts Per Million)**:
  - Normalizes by sequencing depth only (no length correction)
  - Appropriate when comparing the same gene across samples

**For differential expression (between-sample normalization):**

- **DESeq2 median-of-ratios**: Calculates size factors based on the median ratio of each gene's count to its geometric mean across samples. Robust to outliers and differentially expressed genes
- **TMM (Trimmed Mean of M-values)**: Used by edgeR. Trims extreme fold-changes and absolute expression values before computing normalization factors
- **Upper quartile**: Normalizes to the 75th percentile of counts

**Critical note:** DESeq2 and edgeR expect **raw counts** as input — not TPM, FPKM, or other pre-normalized values. These tools perform their own internal normalization.

---

### 3.5 Differential Expression Analysis

The goal: identify genes whose expression differs significantly between conditions (e.g., treatment vs control, tumor vs normal).

**Major tools:**

**DESeq2 (R/Bioconductor):**
- Models count data with negative binomial distribution
- Wald test or likelihood ratio test for hypothesis testing
- Benjamini-Hochberg FDR correction for multiple testing
- apeglm shrinkage for log2 fold-change estimation
- Best for: small sample sizes (n ≥ 3), standard experimental designs

**edgeR (R/Bioconductor):**
- Also uses negative binomial distribution
- Empirical Bayes estimation of dispersion
- TMM normalization
- Quasi-likelihood F-tests for complex designs
- Best for: flexible experimental designs, moderate sample sizes

**limma-voom (R/Bioconductor):**
- Originally designed for microarray data; voom adapts it for RNA-seq
- voom transforms count data by estimating mean-variance relationship
- Precision weights for each observation
- Linear modeling framework — highly flexible for complex designs
- Best for: large sample sizes, complex experimental designs, multi-factor experiments

**Key outputs:**
- Log2 fold-change (effect size)
- p-value (raw significance)
- Adjusted p-value / FDR (corrected for multiple testing)
- Typical thresholds: |log2FC| > 1 and adjusted p < 0.05

---

### 3.6 Pathway and Functional Analysis

**Over-Representation Analysis (ORA):**
- Input: list of significant DE genes
- Tests whether predefined gene sets are enriched beyond chance (Fisher's exact test / hypergeometric test)
- Tools: DAVID, g:Profiler, clusterProfiler (enrichGO, enrichKEGG)
- Fast but requires arbitrary significance threshold

**Gene Set Enrichment Analysis (GSEA):**
- Input: all genes ranked by expression change (no threshold needed)
- Tests whether gene sets are enriched at the top or bottom of the ranked list
- More sensitive for detecting subtle, coordinated changes
- Original implementation: Broad Institute GSEA software
- R implementation: clusterProfiler (gseGO, gseKEGG), fgsea

**Key gene set databases:**
- **GO (Gene Ontology)**: Three domains — Biological Process, Molecular Function, Cellular Component. Hierarchical structure
- **KEGG (Kyoto Encyclopedia of Genes and Genomes)**: Metabolic and signaling pathway maps
- **Reactome**: Curated molecular interaction networks
- **MSigDB**: ~50,000 gene sets including hallmark, positional, motif, computational, GO, oncogenic, immunologic collections
- **WikiPathways**: Community-curated pathway database

**Best practice:** Use multiple enrichment methods (ORA + GSEA) and multiple gene set databases to get comprehensive functional interpretation.

---

### 3.7 Single-Cell Analysis

**Standard scRNA-seq workflow (Seurat / Scanpy):**

1. **Pre-processing (Cell Ranger for 10x data):**
   - Demultiplexing: Assign reads to cells via barcodes
   - Alignment: Map reads to reference genome/transcriptome
   - UMI counting: Generate cell × gene count matrix
   - Output: Filtered feature-barcode matrix

2. **Quality control filtering:**
   - Remove low-quality cells: low gene count, high mitochondrial %, doublets
   - Remove low-information genes: detected in very few cells
   - Doublet detection: Scrublet, DoubletFinder

3. **Normalization:**
   - Log-normalization (Seurat default): normalize total counts per cell, log-transform
   - SCTransform (Seurat): Variance-stabilizing transformation using regularized negative binomial regression
   - scran: Pooling-based normalization

4. **Feature selection:**
   - Identify highly variable genes (HVGs) — typically 2,000–5,000 genes
   - These drive downstream clustering and dimensionality reduction

5. **Dimensionality reduction:**
   - **PCA**: Linear reduction, typically 10–50 principal components retained
   - **t-SNE (t-distributed Stochastic Neighbor Embedding)**: Non-linear; preserves local structure. Good for visualizing clusters. Not distance-preserving at global scale
   - **UMAP (Uniform Manifold Approximation and Projection)**: Non-linear; preserves both local and some global structure. Faster than t-SNE. Most commonly used for scRNA-seq visualization
   - **Diffusion Maps**: Highlight continuous transitions (differentiation trajectories)

6. **Clustering:**
   - Graph-based clustering (Louvain / Leiden algorithms) on k-nearest-neighbor graph in PCA space
   - Resolution parameter controls cluster granularity
   - Leiden algorithm preferred over Louvain (guarantees connected communities)

7. **Cell type annotation:**
   - Marker gene-based: Compare cluster markers to known cell type signatures
   - Automated: SingleR, CellTypist, Azimuth (Seurat reference mapping)
   - Foundation model-based: scGPT, Geneformer (emerging)

8. **Trajectory inference / pseudotime:**
   - **Monocle 3**: UMAP + principal graph for trajectory construction
   - **RNA velocity (velocyto / scVelo)**: Uses spliced/unspliced ratio to infer future cell state
   - **PAGA (Partition-based Graph Abstraction)**: Scanpy tool for trajectory topology
   - **CellRank**: Combines RNA velocity with transcriptomic similarity for fate probability estimation

**Key software ecosystems:**

| Tool | Language | Strengths |
|------|----------|-----------|
| **Seurat** (v5) | R | Most widely used; supports spatial, multiome, CITE-seq. Reference mapping |
| **Scanpy** | Python | Scalable; integrates with scvi-tools, Squidpy (spatial). AnnData format |
| **Cell Ranger** | Command line | 10x Genomics official preprocessing pipeline |
| **scvi-tools** | Python | Probabilistic models for integration, imputation, differential expression |
| **Harmony** | R/Python | Fast batch correction / integration |
| **CellBender** | Python | Ambient RNA removal, denoising |

---

## 4. Applications

### 4.1 Cancer Transcriptomics

**Tumor molecular profiling:**
- Gene expression signatures classify tumor subtypes (e.g., PAM50 for breast cancer: Luminal A/B, HER2-enriched, Basal-like)
- Transcriptomic profiling identifies actionable therapeutic targets
- The WINTHER clinical trial demonstrated that adding transcriptomics to genomics increased the number of targetable molecular alterations in solid tumors
- Single-cell and spatial transcriptomics reveal tumor microenvironment architecture, immune infiltration patterns, and resistance mechanisms

**Liquid biopsy:**
- Cell-free RNA (cfRNA) profiling in blood enables non-invasive cancer detection
- Circulating tumor cells (CTCs) can be transcriptomically profiled for tumor heterogeneity and drug resistance monitoring
- Exai-1: A transformer-based foundation model integrating RNA sequence embeddings with cfRNA abundance data for multi-cancer detection
- Extracellular vesicle (EV) RNA provides tissue-specific transcriptomic signatures

### 4.2 Drug Discovery and Pharmacogenomics

- **Target identification**: DE analysis identifies genes/pathways dysregulated in disease
- **Drug response prediction**: Transcriptomic signatures predict sensitivity/resistance (e.g., Connectivity Map / L1000)
- **Pharmacogenomics**: Individual transcriptomic profiles guide drug selection and dosing
- **Toxicogenomics**: Transcriptomic response to compounds reveals mechanisms of toxicity
- **Clinical trial stratification**: Expression-based biomarkers for patient selection

### 4.3 Developmental Biology

- **Cell fate mapping**: scRNA-seq + trajectory analysis traces differentiation paths from progenitors to mature cell types
- **Lineage tracing**: Combined with CRISPR barcoding (e.g., CARLIN, scGESTALT) to track clonal relationships
- **Organoid profiling**: Compare in vitro organoids to in vivo tissues at single-cell resolution
- **Embryo atlases**: Mouse Organogenesis Cell Atlas (MOCA), Human Developmental Cell Atlas

### 4.4 Infectious Disease

- **Host-pathogen interaction**: Dual RNA-seq captures both host and pathogen transcriptomes simultaneously
- **Immune response profiling**: scRNA-seq reveals immune cell activation states, exhaustion, and memory formation
- **Spatial context**: Spatial transcriptomics maps host-pathogen interactions within intact tissues, revealing infection niches and immune cell recruitment
- **Vaccine development**: Transcriptomic correlates of protection guide vaccine design
- **Pandemic response**: COVID-19 drove massive scRNA-seq studies of infected tissues and immune responses

### 4.5 Precision Medicine

- **Biomarker discovery**: Expression signatures for diagnosis, prognosis, and treatment selection
- **Companion diagnostics**: FDA-approved expression-based tests (Oncotype DX, MammaPrint for breast cancer)
- **Rare disease diagnosis**: RNA-seq identifies splicing defects and expression changes missed by genome sequencing
- **Pharmacogenomic profiling**: Individual drug metabolism variation

### 4.6 Agricultural Genomics

- **Crop improvement**: Identify stress-responsive genes (drought, heat, salinity, pathogen resistance)
- **Plant-pathogen interactions**: Multi-omics approaches reveal resistance genes and pathogenesis hubs
- **Breeding programs**: Expression QTL (eQTL) mapping connects genetic variation to gene expression for marker-assisted selection
- **Livestock genomics**: Production trait optimization through expression profiling

---

## 5. Current State of the Field (2025–2026)

### 5.1 Latest Technologies and Platforms

- **10x Genomics GEM-X**: Next-generation single-cell chemistry with improved capture efficiency
- **10x Visium HD**: 2 μm resolution spatial transcriptomics — near-single-cell
- **10x Xenium**: Up to 5,000-gene panels + protein, subcellular resolution
- **PacBio Revio + Kinnex**: High-throughput long-read isoform sequencing
- **ONT PromethION**: High-throughput nanopore, direct RNA sequencing
- **Parse Biosciences**: Combinatorial barcoding scRNA-seq (no microfluidics needed)
- **Scale Biosciences**: Combinatorial indexing for single-cell multi-omics
- **Vizgen MERSCOPE**: Commercial MERFISH platform

### 5.2 AI/ML Integration

**Foundation models for single-cell biology:**

| Model | Cells Trained | Architecture | Key Feature |
|-------|--------------|--------------|-------------|
| **scGPT** | 33M | Decoder-inspired | Masked generative pretraining; multi-omics |
| **Geneformer** | 30M (v1), 103M (v2) | Encoder-only | Rank-based tokenization; perturbation prediction |
| **scFoundation** | 50M | Asymmetric encoder-decoder | Network inference |
| **GeneCompass** | 126M | Knowledge-informed | Largest training set; cross-species |
| **Nicheformer** | 110M | Multimodal | RNA + spatial transcriptomics |
| **scPRINT** | 54M | Transformer | Gene regulatory network inference |
| **UCE** | Multi-species | ESM2-based | Cross-species (8 species) |

**Applications of foundation models:**
- Cell type annotation (zero-shot and fine-tuned)
- Batch correction and data integration
- Perturbation prediction (predict response to genetic/drug interventions)
- Gene regulatory network inference
- Gene function prediction

**Current limitations of foundation models:**
- Zero-shot performance sometimes outperformed by simpler methods
- Tokenization ignores cell-cell relationships and spatial context
- Training data redundancy causes data leakage concerns
- Rare cell types underrepresented
- Lack of standardized benchmarks (each model uses its own evaluation)
- Fine-tuning requires substantial compute (GPU clusters)
- Must extend beyond transcriptomics to epigenomics, spatial, and protein data

**Other AI/ML applications:**
- Deep learning for cell segmentation in spatial transcriptomics
- Variational autoencoders (scVI) for batch correction and imputation
- Graph neural networks for cell-cell communication inference
- Transformer models for cfRNA-based cancer detection (Exai-1)

### 5.3 Multi-Omics Integration

- **Single-cell multi-omics**: 10x Multiome (RNA + ATAC-seq), CITE-seq (RNA + protein), SHARE-seq, TEA-seq
- **Spatial multi-omics**: Xenium (RNA + protein), spatial ATAC-seq emerging
- **Integration methods**: MOFA+, Seurat WNN (weighted nearest neighbor), scvi-tools (MultiVI), ArchR
- **Multi-omics databases**: Integrating genomics, transcriptomics, proteomics, metabolomics for comprehensive disease profiling
- **Challenges**: Different data modalities have different distributions, noise characteristics, and batch effects; standardized preprocessing protocols lacking

### 5.4 Key Challenges

**Technical:**
- **Batch effects**: Sample processing time, reagent lot, operator, sequencing run all introduce systematic variation. Mitigation: ComBat, Harmony, MNN, Scanorama
- **Dropout in scRNA-seq**: Low mRNA capture efficiency produces excess zeros. Debate: biological signal vs technical artifact. Methods: imputation (MAGIC, scImpute) vs embracing sparsity
- **Cost**: scRNA-seq remains expensive ($2,000–$10,000+ per experiment); spatial transcriptomics even more costly
- **Scalability**: Atlasing projects generate datasets with millions of cells requiring substantial compute
- **Reproducibility**: Preprocessing choices significantly impact results; lack of consensus pipelines

**Analytical:**
- **Cell type annotation consistency**: Different references and methods produce different labels
- **Pseudotime uncertainty**: Trajectory inference methods often disagree
- **Multi-omics integration**: No gold standard for combining disparate data types
- **Foundation model validation**: Need standardized benchmarks and biological validation

### 5.5 Market Size and Growth

- **2025 estimated market**: ~$7.8–8.1 billion (varies by analyst)
- **2026 projected**: ~$8.5–8.6 billion
- **2031 forecast**: ~$10.9 billion (5.05% CAGR)
- **2033–2034 forecast**: $12.3–28.3 billion (depending on scope definition)
- **Spatial transcriptomics** growing faster than bulk: separate market segment expanding rapidly
- **Growth drivers**: Clinical gene expression profiling (oncology, immunology, rare disease), AI integration, spatial sequencing, reimbursement expansion

---

## 6. Key Databases and Resources

### 6.1 Data Repositories

| Database | Description | Scale |
|----------|-------------|-------|
| **GEO (Gene Expression Omnibus)** | NCBI-hosted repository for microarray and sequencing data. Largest public gene expression database | >200,000 datasets |
| **ArrayExpress** | EMBL-EBI repository, now part of BioStudies. European counterpart to GEO | >80,000 experiments |
| **SRA (Sequence Read Archive)** | Raw sequencing data repository (NCBI). Linked to GEO | Petabytes of raw data |
| **TCGA (The Cancer Genome Atlas)** | Multi-omics cancer data across 33 cancer types. Accessed via GDC Data Portal | ~11,000 patients |
| **GTEx (Genotype-Tissue Expression)** | Normal tissue gene expression across 53 tissue sites | ~1,000 donors |
| **Human Cell Atlas** | Global effort to map all human cell types via single-cell methods. Data portal at data.humancellatlas.org | 70.5M cells, 11.2K donors, 527 projects |
| **CZ CELLxGENE** | Chan Zuckerberg Initiative. Largest curated corpus of standardized single-cell datasets | ~100M cells |
| **Human Protein Atlas** | Tissue and cell type expression data with immunohistochemistry validation | Whole-transcriptome, all major tissues |
| **ENCODE** | Encyclopedia of DNA Elements. Standardized RNA-seq pipelines and data | Hundreds of cell lines and tissues |
| **Single Cell Expression Atlas** | EMBL-EBI hosted, curated scRNA-seq studies | Growing collection |

### 6.2 Software Ecosystems

**Bioconductor (R):**
- 2,200+ packages for genomic data analysis
- Core packages: DESeq2, edgeR, limma, GenomicRanges, SummarizedExperiment, SingleCellExperiment
- scRNA-seq: Seurat, scran, scater, SingleR, CuratedAtlasQueryR
- Data access: GEOquery, TCGAbiolinks, recount3, tximport
- Annotation: org.Hs.eg.db, clusterProfiler, AnnotationDbi
- Release cycle: biannual (spring/fall)

**Bioconda (Python/conda):**
- Conda channel for bioinformatics software
- Provides reproducible installation of: STAR, HISAT2, Salmon, kallisto, FastQC, Trimmomatic, samtools, featureCounts
- Enables reproducible environments via conda/mamba

**Python ecosystem:**
- Scanpy + AnnData for scRNA-seq
- scvi-tools for probabilistic modeling
- Squidpy for spatial transcriptomics
- pyDESeq2 for differential expression
- rapids-singlecell for GPU-accelerated analysis

**Workflow managers:**
- **nf-core/rnaseq**: Nextflow pipeline — community gold standard for bulk RNA-seq
- **nf-core/scrnaseq**: Nextflow pipeline for scRNA-seq preprocessing
- **Snakemake**: Python-based workflow manager, popular in academic settings
- **Galaxy**: Web-based platform for accessible bioinformatics

---

## Sources

- [Transcriptomics overview — ScienceDirect Topics](https://www.sciencedirect.com/topics/biochemistry-genetics-and-molecular-biology/transcriptomics)
- [Bioinformatics perspectives on transcriptomics (2025) — Quantitative Biology](https://onlinelibrary.wiley.com/doi/full/10.1002/qub2.78)
- [Bulk RNAseq Methodology — Emory Integrated Computational Core](https://www.cores.emory.edu/eicc/_includes/documents/sections/resources/RNAseq_Methodology.html)
- [ENCODE Bulk RNA-seq Data Standards](https://www.encodeproject.org/data-standards/rna-seq/long-rnas/)
- [nf-core/rnaseq pipeline — GitHub](https://github.com/nf-core/rnaseq)
- [RNA-Seq vs Microarrays — Illumina](https://www.illumina.com/science/technology/next-generation-sequencing/beginners/advantages/rna-seq-vs-arrays.html)
- [10x Genomics Chromium Platform](https://www.10xgenomics.com/platforms/chromium)
- [How does single cell RNA-seq work — 10x Genomics](https://www.10xgenomics.com/blog/how-does-single-cell-rna-seq-work)
- [GEM-X Technology — 10x Genomics](https://www.10xgenomics.com/blog/the-next-generation-of-single-cell-rna-seq-an-introduction-to-gem-x-technology)
- [10X Genomics vs Smart-seq2 comparison — ScienceDirect](https://www.sciencedirect.com/science/article/pii/S1672022921000486)
- [10x Visium Platform](https://www.10xgenomics.com/platforms/visium)
- [10x Xenium Platform](https://www.10xgenomics.com/platforms/xenium)
- [Spatial Transcriptomics — 10x Genomics](https://www.10xgenomics.com/spatial-transcriptomics)
- [Spatial transcriptomics clinical translation challenges — PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC11006413/)
- [Nanopore long-read RNA-seq benchmark (2025) — Nature Methods](https://www.nature.com/articles/s41592-025-02623-4)
- [LongBench cross-platform long-read benchmark (2025) — bioRxiv](https://www.biorxiv.org/content/10.1101/2025.09.11.675724v1.full)
- [Long-read RNA-seq in human diseases — ScienceDirect](https://www.sciencedirect.com/science/article/pii/S1525001624007524)
- [IsoQuant: Accurate isoform discovery — Nature Biotechnology](https://www.nature.com/articles/s41587-022-01565-y)
- [Single-cell long-read sequencing bioinformatics — PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC12696714/)
- [DESeq2, edgeR, limma comparison — PubMed](https://pubmed.ncbi.nlm.nih.gov/34605806/)
- [GSEA and pathway analysis guide — BigOmics](https://bigomics.ch/blog/a-short-guide-to-gene-set-and-pathway-enrichment-analysis/)
- [GSEA with ClusterProfiler — NYU Gencore](https://learn.gencore.bio.nyu.edu/rna-seq-analysis/gene-set-enrichment-analysis/)
- [Salmon & kallisto comparison — NYU Gencore](https://learn.gencore.bio.nyu.edu/rna-seq-analysis/salmon-kallisto-rapid-transcript-quantification-for-rna-seq-data/)
- [Kallisto vs STAR — Elucidata](https://www.elucidata.io/blog/kallisto-vs-star-alignment-and-quantification-of-bulk-rna-seq-data/)
- [Single-cell foundation models — Nature Experimental & Molecular Medicine](https://www.nature.com/articles/s12276-025-01547-5)
- [scGPT — Nature Methods](https://www.nature.com/articles/s41592-024-02201-0)
- [Zero-shot limitations of scFMs — Genome Biology](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-025-03574-x)
- [AI Revolution in Transcriptomics (2026) — Advanced Science](https://advanced.onlinelibrary.wiley.com/doi/10.1002/advs.202518949)
- [Transcriptomics in solid tumors — ScienceDirect](https://www.sciencedirect.com/science/article/pii/S1044579X20301966)
- [Multi-omics in precision oncology (2025) — SAGE Journals](https://journals.sagepub.com/doi/10.1177/11795549251384582)
- [Multimodal cfRNA language model — Nature Machine Intelligence](https://www.nature.com/articles/s42256-025-01148-x)
- [Spatial transcriptomics for infectious disease — MDPI Vaccines](https://www.mdpi.com/2076-393X/14/2/158)
- [Plant-pathogen omics — Springer Nature](https://link.springer.com/article/10.1007/s44372-025-00337-7)
- [scRNA-seq dropout controversy — Genome Biology](https://link.springer.com/article/10.1186/s13059-022-02601-5)
- [Batch effects in omics — Genome Biology](https://link.springer.com/article/10.1186/s13059-024-03401-9)
- [Multi-omics integration review — Briefings in Bioinformatics](https://academic.oup.com/bib/article/26/4/bbaf355/8220754)
- [Transcriptomics market forecast — Mordor Intelligence](https://www.mordorintelligence.com/industry-reports/transcriptomics-market)
- [Transcriptomics market — Fortune Business Insights](https://www.fortunebusinessinsights.com/industry-reports/transcriptomics-market-101227)
- [GTEx Portal](https://gtexportal.org/home/)
- [GEO — NCBI](https://www.ncbi.nlm.nih.gov/geo/)
- [Human Cell Atlas Data Portal](https://data.humancellatlas.org/)
- [Public RNA-seq databases — BigOmics](https://bigomics.ch/blog/ultimate-guide-to-public-rnaseq-and-sc-rna-seq-databases/)
- [CuratedAtlasQueryR — Bioconductor](https://blog.bioconductor.org/posts/2023-02-23-CuratedAtlasQueryR/)
- [Seurat single-cell course](https://www.singlecellcourse.org/single-cell-rna-seq-analysis-using-seurat.html)
- [Scanpy clustering tutorial](https://scanpy.readthedocs.io/en/stable/tutorials/basics/clustering.html)
- [Trajectory inference with PAGA — NBIS Workshop](https://nbisweden.github.io/workshop-scRNAseq/labs/scanpy/scanpy_07_trajectory.html)
