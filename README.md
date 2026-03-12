# Genre Annotation Project

This repository contains the code and analysis for the paper:

> **A Dataset of Historical Medical Periodicals Annotated with Textual Genre**
> Vera Danilova<sup>1</sup> and Sara Stymne<sup>2</sup>
> <sup>1</sup>Department of History of Science and Ideas, Uppsala University, Sweden
> <sup>2</sup>Department of Linguistics and Philology, Uppsala University, Sweden
> vera.danilova@idehist.uu.se, sara.stymne@lingfil.uu.se
> LREC 2026

## Overview

This project presents a dataset of historical medical periodicals (in German and Swedish) annotated with textual genre labels. The repository provides tools for exploring annotation data, calculating inter-annotator agreement, and analyzing patterns of agreement and disagreement between annotators.

## Resources

- **Genre Annotation Guidelines**: [Notion Documentation](https://www.notion.so/Genre-annotation-project-b3ae28bae3e8406db00484871f3a0e97)
- **Dataset on HuggingFace**: [historical-magazines-genre-annotation](https://huggingface.co/datasets/actdisease/historical-magazines-genre-annotation) (available upon request)

## Repository Structure

```
.
├── explore_annotations.ipynb          # Main analysis notebook
├── medical-magazines-genre-annotation.csv  # Exported annotation data
├── merged_annotations.csv             # Annotations merged with re-annotations
├── genre_annotations_with_embeddings.parquet  # Annotations with text embeddings
├── genre_training_data_swe_part1_and_double_voted.parquet  # Training data subset
├── figures/                           # Generated visualizations
│   ├── genre_distribution.png
│   ├── Agreement_Metrics_Heatmap.png
│   ├── confusion_matrix_*.png
│   ├── HeatmapSemanticSimilarity_*.png
│   ├── HeatmapStructuralSimilarity_*.png
│   ├── WordOverlap_*.png
│   └── TopicOverlap_*.png
└── LICENSE
```

## Notebook: `explore_annotations.ipynb`

The Jupyter notebook provides a comprehensive analysis pipeline with the following sections:

### 1. Data Loading and Exploration
- Loads annotation data from Excel files
- Maps annotator names to anonymized IDs
- Computes basic statistics (paragraph counts, token lengths per language/part)

### 2. Inter-Annotator Agreement (IAA)
- Calculates **Krippendorff's alpha** for overall agreement
- Computes **Cohen's kappa** for pairwise agreement
- Generates agreement heatmaps for each language/part subset

### 3. Genre Distribution Analysis
- Visualizes the distribution of genre labels across the dataset
- Analyzes annotations by language, periodical, and annotator

### 4. OCR Error Analysis
- Examines the prevalence of OCR errors across genres
- Correlates OCR quality with annotation agreement

### 5. Re-annotation Processing
- Integrates re-annotation data from additional annotators
- Merges re-annotations with the main dataset
- Measures agreement after incorporating re-annotations

### 6. Text Embedding
- Generates embeddings for all paragraphs using **Qwen/Qwen3-Embedding-4B**
- Saves embedded data to Parquet format for downstream analysis

### 7. Agreement/Disagreement Feature Analysis
- **Semantic Similarity**: Compares embedding similarity between paragraphs labeled differently by annotators
- **Structural Similarity**: Extracts linguistic features using spaCy and textstat (readability metrics, POS distributions, sentence complexity)
- **Word Overlap**: Analyzes vocabulary overlap between genre categories
- **Disagreement Predictors**: Uses logistic regression and correlation analysis to identify features that predict annotator disagreement

### 8. Topic Modeling
- Applies **BERTopic** with UMAP and HDBSCAN for topic discovery
- Visualizes topic distributions across agreement/disagreement groups

### 9. Analysis Pipeline Class
The `AnnotatorAnalysisPipeline` class orchestrates the full analysis workflow, supporting:
- Semantic similarity computation
- Linguistic feature extraction
- Structural similarity analysis
- Word overlap calculation
- Topic modeling
- Disagreement analysis (Spearman correlation, Chi-square tests)

## Installation

### Requirements

```bash
pip install pandas numpy seaborn matplotlib scikit-learn krippendorff
pip install spacy textstat
pip install sentence-transformers torch
pip install bertopic umap-learn hdbscan
pip install datasets huggingface_hub
pip install python-dotenv openpyxl
```

### spaCy Language Models

```bash
python -m spacy download de_core_news_md  # German
python -m spacy download sv_core_news_md  # Swedish
```

### Environment Variables

Create a `.env` file with the following variables:

```
MAIN_ANNOTS=/path/to/annotation/files
REANNOTS=/path/to/reannotation/files
HF_TOKEN=your_huggingface_token
```

## Usage

1. **Set up environment**: Configure the `.env` file with paths to your annotation data.

2. **Run the notebook**: Open `explore_annotations.ipynb` in Jupyter and execute cells sequentially.

3. **Generate figures**: The notebook outputs visualizations to the `figures/` directory.

4. **Export data**: The notebook can export processed data to CSV and Parquet formats, and upload to HuggingFace Hub.

### Running Specific Analyses

To run the full analysis pipeline on a specific subset:

```python
pipeline = AnnotatorAnalysisPipeline(df_, language='deu', part_no=1)
ratios = pipeline.execute_pipeline(
    do_semantic=True,
    do_linguistic=True,
    do_structural=True,
    do_word_overlap=True,
    do_topic=True,
    do_disagreement_analysis=True,
    do_spearman=True,
    do_chi=True
)
```

## Dataset Description

The dataset consists of paragraphs from historical medical periodicals annotated with genre labels:

- **Languages**: German (`deu`), Swedish (`swe`)
- **Subsets**: Part 1 and Part 2 for each language
- **Annotation scheme**: Multiple annotators per paragraph (single and double-annotated)

### Genre Categories

Genres include: Academic, Administrative, Advertisements, Case reports, Commentary, Correspondence, Editorial, News, Obituaries, Recipes, Reviews, and more. See the [annotation guidelines](https://www.notion.so/Genre-annotation-project-b3ae28bae3e8406db00484871f3a0e97) for full details.

### Data Fields

- `fname`: Source filename
- `periodical`: Name of the periodical
- `year`, `volume`, `nr`, `page`, `paragraph`: Location identifiers
- `genre`: Assigned genre label
- `annotator_id`: Anonymized annotator identifier
- `ocr_errors`: Flag indicating OCR quality issues
- Additional flags: `contents_page`, `publication_info`, `art_author`, `art_title`, `opinionated`, `dialogue`, `caption`

## License

See [LICENSE](LICENSE) for details.

## Citation

If you use this dataset or code, please cite:

```bibtex
@inproceedings{danilova2025dataset,
  title={A Dataset of Historical Medical Periodicals Annotated with Textual Genre},
  author={Danilova, Vera and Stymne, Sara},
  booktitle={...},
  year={2025}
}
```
