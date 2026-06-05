Tasks:

1. Collecting Metrics
2. Trying various outputs
3. Trying different industry documents

# Setup

1. Run `pip install docling` 😄

> optional: run `pip install --upgrade label-studio-sdk` for integration with label studio **experimental: we have to verify the label studio version first**

2. Get and use a HuggingFace token

Once installed, you can experiment with:

## 1. Docling CLI

Run (processing is faster if you provide a Hugging Face token):

```powershell
$env:HF_TOKEN="your_token_here"
docling [--from 'pdf' --to 'output (json, html)'] source_file
```

[Full list of Docling CLI options](https://docling-project.github.io/docling/reference/cli/).

For our tests, we added the `--` options:

- `pdf-backend pypdfium2`: which makes the overall process a lot faster
- (optional) `no-ocr`: forces it to run without OCR

This is better for quick testing with a single document.

## 2. Code Approach

Docling's default config will fail with large PDFs. Follow these steps to make sure it runs smoothly.

We can approach this by:

1. Modifying the pipeline's settings (current approach)
2. Chunking the data before processing it

I would recommend adding a HuggingFace token before starting. This ensures your models get downloaded fast. The below is for powershell.

```powershell
    $env:HF_TOKEN="your_token_here"
```

Then run `converter.py`! Below is some code explanation if you'd like to understand how it works.

```python
# Import Docling's required libraries
from docling.datamodel.base_models import InputFormat
from docling.datamodel.pipeline_options import (
    PdfPipelineOptions,
    TableStructureOptions,
)
from docling.document_converter import DocumentConverter, PdfFormatOption

# Import a custom model - we can change this later on
from docling.backend.pypdfium2_backend import PyPdfiumDocumentBackend
```

To configure the pipeline directly, edit the following

```python
# Edit the pipeline here
pipeline_options = PdfPipelineOptions()
pipeline_options.do_ocr = False # We run out of memory when using OCR
pipeline_options.do_table_structure = True

# --- Not exactly sure about this one right now
pipeline_options.table_structure_options = TableStructureOptions(do_cell_matching=False)

# Instantiate DocumentConverter, and pass pipeline options + PyPdfium2
doc_converter = DocumentConverter(
    format_options={
        InputFormat.PDF: PdfFormatOption (
            pipeline_options=pipeline_options, 
            backend=PyPdfiumDocumentBackend
        )
    }
)
```


---

## Documentation Notes

This section abstracts my learning from the [Docling documentation site](https://docling-project.github.io/docling/).

### Architecture

![[Docling Testing-1779986725313.webp|633]]
[Source](https://docling-project.github.io/docling/concepts/architecture/)

Simply put, the 'document converter' can employ a specific pipeline for use based on specific formats.

It returns a 'Docling document', which can then be used to call export methods in markdown, dictionary, or document tokens. Alternatively, it can be serialised or chunked.
