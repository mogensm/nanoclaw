---
name: ocr
description: Extract text from images using Google Cloud Vision OCR
---

# OCR Skill — Google Cloud Vision

Extract text from photos of documents, invoices, score sheets, receipts, etc. using Google Cloud Vision API.

**All commands below must be run using the Bash tool.**

## Commands

### Extract text from an image

Run with the Bash tool:

```bash
node /workspace/extra/skills/ocr/scripts/ocr.js --image <path> [--mode text|document]
```

- `--image` — path to the image file (required)
- `--mode document` — (default) uses DOCUMENT_TEXT_DETECTION, best for handwriting and dense documents
- `--mode text` — uses TEXT_DETECTION, better for simple printed text like signs or labels

## When to use this skill

Use this skill whenever you receive a photo that contains text you need to read accurately:
- Invoices and receipts
- Handwritten score sheets
- Printed documents
- Any image where you need reliable text extraction

**Do NOT rely on your own vision for reading text in images.** Always use this OCR skill first to get accurate text, then structure the data afterwards.

## Usage Guidelines

- For documents, invoices, and score sheets: use the default `--mode document`
- For simple signs or labels with large printed text: use `--mode text`
- The extracted text preserves line breaks and layout as much as possible
- After getting OCR results, follow the image handling instructions in AGENTS.md (verify with user, then structure)
