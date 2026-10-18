# SourceAlign

**Semantic document comparison and AI-assisted change detection**

SourceAlign compares a working document with a Source of Truth (SoT), finds content that may have changed, and prepares suggested revisions for human review. It combines structured document parsing, semantic vector search, and LLM-generated change proposals.

> This repository README describes the current notebook prototype in `Proto.ipynb`. The workflow currently runs in Google Colab and is designed primarily for DOCX documents.

## What it does

- Parses document structure such as paragraphs, headings, bullets, tables, and callouts.
- Splits long prose into sentence-aware chunks while retaining source and location metadata.
- Generates normalized vector embeddings for each chunk.
- Stores chunk text, metadata, and vectors in an OpenSearch k-NN index.
- Searches between the working document and SoT in both directions to identify likely edits and unmatched content.
- Uses an LLM to build a persona profile and draft proposed changes. Uncertain matches can be classified as additions, deletions, or items to ignore.
- Provides a human review flow to approve, reject, or request a re-edit of suggestions.
- Exports approved changes to a new DOCX file and records review results for evaluation.

## Workflow

```text
Working DOCX + Source of Truth DOCX
                  │
                  ▼
       Parse structured document content
                  │
                  ▼
    Chunk content and preserve metadata
                  │
                  ▼
    Embed chunks and index in OpenSearch
                  │
                  ▼
  Bidirectional semantic similarity search
           ┌──────┴──────┐
           ▼             ▼
     Likely edits   Unmatched content
           └──────┬──────┘
                  ▼
       LLM change suggestions
                  │
                  ▼
         Human review and export
```

## Technology

- **Python** in a Google Colab notebook
- **python-docx** for DOCX parsing and document export
- **NLTK** for sentence-aware prose chunking
- **Sentence Transformers** with `sentence-transformers/all-MiniLM-L6-v2` for 384-dimensional embeddings
- **OpenSearch k-NN** for vector indexing and similarity retrieval
- **Gemini** for persona profiling and change suggestion generation
- **ipywidgets** for interactive review

## Running the prototype

1. Open `Proto.ipynb` in Google Colab.
2. Configure the OpenSearch endpoint and credentials, plus the Gemini API key, using Colab Secrets or environment variables.
3. Run the notebook cells in order.
4. Upload the working DOCX and its Source of Truth DOCX when prompted.
5. Review generated suggestions, then export the approved changes.

Do not commit API keys, database credentials, uploaded source documents, or generated embeddings to version control. Keep secrets in Colab Secrets or another secure configuration store.

## Current prototype notes

- The notebook is Colab-oriented and uses interactive upload and review widgets.
- Parsing and export are DOCX-focused. Although the upload cell recognizes `.doc` files, the parsing implementation uses `python-docx`, which is intended for `.docx` files.
- New additions are appended to the end of the exported document; they may need to be moved into the appropriate section.
- Similarity thresholds and top-k retrieval settings are configurable in the notebook and may need tuning for each document set.
- Evaluation cells include reviewer-based metrics and manual ground-truth labeling. Recall requires labels for changes the pipeline did not surface.

## Project goal

Make document maintenance more reliable by surfacing semantic changes against an authoritative reference, reducing manual comparison work, and keeping a human in control of proposed revisions.
