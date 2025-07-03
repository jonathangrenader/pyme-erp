# Import Module Design

This document outlines the design for the data import module, allowing businesses to upload their existing data (products, clients, suppliers) into the pyme-erp system.

## Core Principles
- **Modularity:** Designed as a standalone module, enabling flexible activation based on subscription tiers.
- **User-Friendly:** Guided import process with clear feedback.
- **Robust Validation:** Comprehensive server-side validation to ensure data integrity.
- **Scalability:** Leverages Firebase Functions for scalable processing of import files.

## Supported Data Types (Initial)
- Products
- Clients
- Suppliers

## Supported File Formats
- **Basic Tier:** CSV (Comma Separated Values)
- **Pro Tier & Above:** CSV, Excel (XLSX)

## Import Process Flow

### 1. Frontend (User Interface)
- **File Upload:** A dedicated page/component for uploading import files.
- **Data Type Selection:** User selects the type of data to import (e.g., "Products", "Clients").
- **Template Download:** Provides downloadable CSV/Excel templates with required column headers.
- **File Selection:** User selects the local file to upload.
- **Progress & Feedback:** Displays upload progress, validation results, and import status (success/failure, number of records imported/failed).
- **Error Reporting:** Clearly lists rows that failed validation and the reasons.

### 2. Backend (Firebase Functions)

#### a. File Upload & Storage
- **Trigger:** An HTTP-triggered Firebase Function will receive the uploaded file.
- **Temporary Storage:** The file will be temporarily stored in a dedicated Google Cloud Storage bucket (e.g., `gs://pyme-erp-imports-temp`). This is more robust for larger files than direct memory processing.
- **Security:** Rules for the GCS bucket will ensure only authenticated users can upload and access their temporary files.

#### b. Data Processing & Validation
- **Trigger:** Another Firebase Function (e.g., triggered by a Pub/Sub message or directly invoked after upload) will process the file.
- **Parsing:** Parses the CSV/Excel file content.
- **Validation:**
    - **Format Validation:** Checks if the file adheres to the expected structure (column headers, data types).
    - **Business Logic Validation:**
        - Ensures required fields are present.
        - Validates data against existing Firestore data (e.g., unique SKUs for products within a `businessId`).
        - Checks for valid `businessId` for the user performing the import.
    - **Error Handling:** Collects all validation errors per row.
- **Data Transformation:** Maps imported columns to Firestore document fields.

#### c. Firestore Write Operations
- **Batch Writes:** For performance, data will be written to Firestore using batch writes (up to 500 operations per batch).
- **Atomicity:** Ensures that either all records in a batch are written, or none are, maintaining data consistency.
- **Association:** Each imported document will be associated with the `businessId` of the user performing the import.

### 3. Error Handling & Reporting
- **Detailed Errors:** Validation errors will be specific (e.g., "Row 5: Product name is missing", "Row 12: SKU 'XYZ' already exists").
- **User Feedback:** Errors will be displayed in the frontend, allowing the user to correct the file and re-upload.
- **Audit Logging:** Import operations (success/failure, user, business, file name, number of records) will be logged in the `auditLogs` collection.

## Security Considerations
- **Authentication:** Only authenticated users can initiate imports.
- **Authorization:** Firebase Functions will verify that the user has permission to import data for their `businessId` and for the specific data type.
- **Data Isolation:** All imported data will be strictly associated with the `businessId` of the importing user.
- **File Sanitization:** Ensure that uploaded files are properly handled to prevent malicious content.

## Future Enhancements
- **Background Processing:** For very large files, implement a background processing queue (e.g., using Cloud Tasks) to avoid timeouts.
- **Import History:** Store a history of past import operations.
- **Rollback:** Implement a mechanism to revert a recent import.
- **More Formats:** Support for XML, JSON.
