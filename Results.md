# Testing Results

These are the results of different pipeline testing.

## Pypdfium v2, no OCR, fast table processing

Converted into JSON, took 149.55 seconds.

Pipeline config:

```python
pipeline_options = PdfPipelineOptions()
pipeline_options.do_ocr = False # We run out of memory when using OCR
pipeline_options.do_table_structure = True

# Fast table processing
pipeline_options.table_structure_options =TableStructureOptions(do_cell_matching=False)

# Set PDF backend
doc_converter = DocumentConverter (
	format_options={
		InputFormat.PDF: PdfFormatOption (
			pipeline_options=pipeline_options, backend=PyPdfiumDocumentBackend
		)
	}
)
```

```unix
grep -oP '"label":\s*"\K[^"]+' Funeral_and_Burial_Instructions_Report_for_web.json | sort | uniq -c
```

| Count | Label               |
| ----- | ------------------- |
| 10    | caption             |
| 160   | checkbox_unselected |
| 32    | document_index      |
| 4170  | footnote            |
| 45    | form                |
| 9     | form_area           |
| 5     | key_value_area      |
| 25    | key_value_region    |
| 277   | list                |
| 6778  | list_item           |
| 984   | page_footer         |
| 852   | page_header         |
| 36    | picture             |
| 1698  | section_header      |
| 56    | table               |
| 6640  | text                |
| 2     | unspecified         |
