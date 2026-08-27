# Invoice Document Extraction Workflow

An automated invoice-handling pipeline implemented with n8n and a FastAPI-based document extraction service.

## Overview

The workflow watches Gmail for PDF invoices, forwards them to the document extraction service, checks the returned data, and records the outcomes in Google Sheets.

When extraction confidence is low or the result is invalid, invoices are directed to a separate review queue. Processing failures are categorized and written to a distinct log.

## Workflow
```text
Gmail
  ↓
PDF Invoice
  ↓
Upload to FastAPI
  ↓
Extract Invoice Data
  ↓
Validate
  ├── Valid → Invoices
  ├── Review → Needs Review
  └── Error → Errors
```

## Validation

An invoice is considered valid if all of the following are true:

- Extraction status is `ok`
- Document type is `invoice`
- Confidence is `>= 0.7`

Any result that fails to satisfy these rules is moved to the `Needs Review` sheet.

## Error Handling

Processing failures are grouped as follows:

- `duplicate` for HTTP 409 errors
- `retryable` for 5xx errors
- `terminal` for other errors

Error information is recorded in the `Errors` sheet.

## Extracted Fields

Once validated, invoices are logged with these fields:

- Date
- Vendor
- Total
- Currency
- Category
- Confidence
- Processed At
- Document ID
- File Name

## Stack

- n8n
- FastAPI
- Gemini
- Gmail
- Google Sheets
