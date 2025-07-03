# Security Considerations

Security is paramount for an ERP system handling sensitive business data. This document outlines the key security measures implemented in pyme-erp.

## 1. Authentication (Firebase Authentication)
- **User Identity:** All users must authenticate via Firebase Authentication.
- **Secure Login:** Firebase Auth handles password hashing, multi-factor authentication (MFA) options, and secure session management.
- **Role-Based Access Control (RBAC):**
    - **Global Roles:** `SUPERADMIN` (developer) and `COMPANY_ADMIN` (business owner/manager) roles are managed via Firebase Authentication Custom Claims. These claims are immutable by the client and are verified by Firebase Functions.
    - **Business-Specific Roles:** More granular roles (e.g., 'employee', 'manager') are stored in Firestore within the `users` collection, associated with a `businessId`.

## 2. Authorization & Data Isolation (Firestore Security Rules & Firebase Functions)

### a. Firestore Security Rules
- **Primary Defense:** Firestore Security Rules are the first line of defense for data access.
- **`businessId` Enforcement:** All business-specific collections (e.g., `products`, `clients`, `suppliers`) will have rules that ensure:
    - Users can only read/write documents that belong to their `businessId`.
    - Users can only access their own user profile.
    - `SUPERADMIN` can access all data across all businesses.
- **Example Rule (simplified):**
    ```firestore
    rules_version = '2';
    service cloud.firestore {
      match /databases/{database}/documents {
        // Allow read/write if authenticated and user's businessId matches document's businessId
        match /{collection}/{document} {
          allow read, write: if request.auth != null && request.auth.token.businessId == resource.data.businessId;
        }
        // SUPERADMIN can read/write all
        match /{collection}/{document} {
          allow read, write: if request.auth != null && request.auth.token.isSuperAdmin == true;
        }
      }
    }
    ```
- **Function Invocation:** Rules will also ensure that certain sensitive operations can only be performed by authenticated Firebase Functions (e.g., `allow write: if request.auth.uid == 'function_service_account_uid';`).

### b. Firebase Functions
- **Trusted Environment:** Firebase Functions act as a secure backend, executing business logic and data operations in a trusted server environment.
- **Input Validation:** All data received by Functions from the frontend will be rigorously validated (data types, formats, business rules) before processing.
- **Permission Checks:** Functions will perform detailed permission checks based on the user's role (from Custom Claims and Firestore roles) and the `businessId` associated with the request.
- **Data Integrity:** Functions ensure that data written to Firestore adheres to the defined schema and business logic.
- **Sensitive Operations:** Operations like stock adjustments, payment processing, and user/role management will only be performed by Functions.

## 3. Data Encryption
- **Data in Transit:** All data transferred between the client, Firebase services, and Google Cloud Storage is encrypted using TLS/SSL.
- **Data at Rest:** Data stored in Firestore and Google Cloud Storage is automatically encrypted at rest by Google.

## 4. Input Validation
- **Frontend Validation:** Basic client-side validation for immediate user feedback.
- **Backend Validation (Firebase Functions):** Comprehensive server-side validation is mandatory for all data submitted to ensure data integrity and prevent malicious input.

## 5. API Key Security
- Firebase client-side SDKs use API keys that are generally safe to expose. However, sensitive operations are always protected by Firebase Authentication and Firestore Security Rules/Firebase Functions.

## 6. Audit Logging
- The `auditLogs` collection will record significant system events, providing a trail for security monitoring and debugging. Only `SUPERADMIN` can access this module.

## 7. Regular Updates
- Keeping all dependencies (React, Firebase SDKs, Node.js for Functions) updated to their latest versions to benefit from security patches.
