# OCR + Open-Source LLM Prescription Review Scenario

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
-> local ingestion
-> preprocessing
-> OCR
-> open-source LLM extraction
-> structured prescription draft
-> human review
-> approved/corrected export
```

The system should never present extracted prescription information as automatically correct. Human review is part of the core workflow.

## Shared Output Contract

The application should export reviewed results in a shape similar to the following so outcomes can later be compared with other prescription extraction approaches.

```json
{
  "record_id": "string",
  "source_file": "string",
  "method": "ocr_open_source_llm",
  "extracted": {
    "patient_name": "string",
    "prescriber_name": "string",
    "prescription_date": "YYYY-MM-DD",
    "medications": [
      {
        "medication_name": "string",
        "dosage": "string",
        "unit": "string",
        "frequency": "string",
        "route": "string",
        "duration": "string",
        "special_instructions": "string",
        "uncertainty_notes": "string"
      }
    ]
  },
  "review": {
    "status": "pending | approved | edited | rejected",
    "reviewer_notes": "string",
    "reviewed_at": "datetime"
  },
  "run_metadata": {
    "model_name": "string",
    "model_version": "string",
    "runtime": "local | hosted",
    "latency_ms": 0,
    "prompt_template": "string"
  }
}
```

## Safety and Privacy Notes

This scenario must not include real prescription data, real patient data, or real PHI in the public repository.

Outputs are draft extractions for review only. They are not medical advice and must not be treated as clinically valid without qualified human review.

## Submission Guidelines

- Fork the repository and create a feature branch for your contribution.
- Submit your work through a pull request against the main repository. Do not submit code, prescription files, screenshots containing real prescriptions, or datasets through email, chat, or shared drives.
- Open an issue first if your proposed approach changes the scope materially, introduces a major dependency, requires a hosted service by default, or needs a different runtime than the one described in this README.
- Include a short solution approach in the pull request that explains the OCR choice, model choice, extraction strategy, review workflow, design tradeoffs, and known limitations.
- Include architecture documentation that shows the main components, data flow, adapter boundaries, configuration files, local storage, and output artifacts. A simple diagram is preferred where useful.
- Include setup and running instructions that allow a reviewer to run the project from a clean checkout using fake fixtures.
- Include deployment notes, even if the project only runs locally. State the expected runtime, environment variables, model/runtime setup, storage paths, and optional services.
- Include scaling notes that explain what would need to change for larger document batches, queue-based processing, parallel OCR, model serving, cloud storage, or managed orchestration.
- Include integration notes describing how Azure Document Intelligence, Durable Functions, hosted model endpoints, or similar services could later be plugged in through the adapter boundaries.
- Include code documentation for public functions, configuration options, CLI commands, model prompts, data formats, review states, and export formats.
- Include sample inputs and outputs using fake demo fixtures only. Do not include real prescriptions, real patient data, real provider data, or PHI.
- Include tests or validation checks for core behavior, such as schema validation, OCR output handling, extraction parsing, review state updates, export creation, and error handling.
- Include a short quality report or evidence section showing sample runs, known failure cases, and how reviewers should inspect outputs.
- Keep secrets, credentials, API keys, generated caches, local model files, private documents, and local environment files out of the repository.
- Add or update `.gitignore` where needed to prevent accidental submission of local data, model artifacts, generated files, or credentials.
- Use clear commit messages and keep unrelated refactors out of the pull request.
- The pull request should be reviewable as a standalone contribution: reviewers should not need access to internal roadmaps, private prescription datasets, AWS PoCs, Azure architecture, or proprietary platform details to understand or run it.

