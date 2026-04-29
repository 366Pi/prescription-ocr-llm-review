# OCR + Open-Source LLM Prescription Review

## Overview

Build a local-first prototype for extracting structured prescription data from scanned or handwritten prescription images using OCR, configurable open-source LLM analysis, and a lightweight reviewer UI.

This scenario should be self-contained and limited in scope. It should use fake sample fixtures only for demo or smoke testing. Real prescription data must not be included in the public repository.

The implementation should be structured so that open-source OCR and model components can later be replaced with managed services such as Azure Document Intelligence or similar extraction services.

## What Problem It Solves

Prescription documents are often handwritten, scanned, incomplete, or difficult to interpret consistently. This scenario explores a practical local pipeline where prescription images are converted into text using OCR, analyzed by a configurable open-source LLM, and reviewed by a human before being accepted.

The goal is not to build a medical product. The goal is to create a limited-scope reference implementation for prescription extraction, structured output, human review, and later comparison against other extraction approaches.

## Scope

The implementation should include:

- local upload or folder-based ingestion of prescription images/PDFs
- basic image preprocessing:
  - rotation correction where practical
  - contrast enhancement
  - denoising
  - resizing
- OCR extraction from images
- configurable open-source LLM analysis layer
- structured prescription output, such as:
  - patient name, if present
  - prescriber name, if present
  - prescription date
  - medication name
  - dosage
  - unit
  - frequency
  - route
  - duration
  - special instructions
  - uncertainty notes
- lightweight reviewer UI showing:
  - original prescription image
  - OCR text
  - extracted structured fields
  - confidence or uncertainty notes
  - approve, edit, reject actions
- local storage of reviewed results
- export of reviewed records as JSON or CSV
- adapter boundaries so managed services can be plugged in later

## Non-Goals

This work packet is not intended to cover:

- real prescription data in the public repository
- production medical decision-making
- diagnosis
- dosage recommendation or correction
- PHI compliance certification
- production EHR integration
- internal Florence, KidCare, Nexus, AWS, or Azure architecture
- hosted multi-tenant deployment
- hard dependency on a specific OCR engine or LLM

## Expected Deliverables

A complete contribution should include:

- working local application or CLI plus UI
- OCR pipeline
- configurable open-source LLM adapter
- prescription extraction schema
- reviewer UI
- local persistence for review status and corrected fields
- export function for reviewed results
- sample fake prescription fixtures for testing only
- setup documentation
- limitations and safety notes
- notes describing where Azure Document Intelligence or similar services could be plugged in later

## Success Criteria

A submission will be considered successful if:

- a reviewer can upload or select a prescription image locally
- OCR output is generated and visible
- an open-source LLM analyzes the OCR text into structured fields
- extracted fields can be reviewed, edited, approved, or rejected
- reviewed output can be exported
- the application runs without private internal APIs
- no real prescription or patient data is included in the repo
- OCR and LLM components can be swapped through configuration or adapter code
- the implementation is structured so a future Azure Document Intelligence adapter could replace the open-source OCR path

## Suggested Stack

Preferred stack:

- Python
- OpenCV / Pillow
- Tesseract, EasyOCR, PaddleOCR, or similar OCR engine
- Ollama, llama.cpp, Hugging Face Transformers, or similar local/open-source LLM path
- Streamlit, Gradio, FastAPI plus simple frontend, or lightweight React UI
- SQLite or JSONL for local persistence
- Pydantic or JSON Schema for structured output validation

## Design Notes

The implementation should use clean adapter boundaries, for example:

- `DocumentIngestionProvider`
- `ImagePreprocessor`
- `OcrProvider`
- `ExtractionModelProvider`
- `ReviewStore`
- `ExportWriter`

The intended flow is:

```text
Prescription image/PDF
→ local ingestion
→ preprocessing
→ OCR
→ open-source LLM extraction
→ structured prescription draft
→ human review
→ approved/corrected export
