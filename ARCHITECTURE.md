# Architecture Overview

This document outlines the high-level architecture of the pyme-erp application.

## Core Principles
- **Modularity:** Each major feature (Stock, Clients, Suppliers, Invoicing) is designed as an independent module.
- **Scalability:** Leveraging Firebase's serverless and scalable services.
- **Multi-tenancy:** Strict data isolation per business.
- **Role-Based Access Control (RBAC):** Granular permissions managed at different levels.
- **Security:** All sensitive operations handled via Firebase Functions.
- **Mobile-First UI/UX:** Responsive and intuitive design.

## High-Level Components

### 1. Frontend (React Application)
- **Technology:** React (TypeScript), React Router, Bootstrap.
- **Purpose:** User interface, navigation, data presentation, and interaction with Firebase services.
- **Key Aspects:**
    - Dynamic UI based on user roles and enabled modules.
    - Mobile-first design principles for responsiveness and agility.
    - Integration of UI components for QR/Barcode scanning.

### 2. Backend (Firebase Services)

#### a. Firestore (NoSQL Database)
- **Purpose:** Stores all application data (users, businesses, products, clients, etc.).
- **Multi-tenancy:** All business-related data documents include a `businessId` field.
- **Collections:**
    - `users`: User profiles, linked to Firebase Authentication UIDs.
    - `businesses`: Configuration for each company, including enabled modules and software limits.
    - `roles` / `permissions`: Definitions of roles and granular permissions within each business.
    - `products`, `clients`, `suppliers`, `priceLists`: Core business data, always associated with a `businessId`.
    - `auditLogs`: System-wide audit trail (SUPERADMIN access only).

#### b. Firebase Authentication
- **Purpose:** User registration, login, session management.
- **Roles:**
    - `SUPERADMIN`: Custom claim (`isSuperAdmin: true`) for developer-level access.
    - `COMPANY_ADMIN`: Custom claim for company-level administrators.
    - Other roles (e.g., `employee`, `manager`) managed within Firestore per `businessId`.

#### c. Firebase Functions (Serverless)
- **Purpose:** Secure backend logic, API endpoints, data validation, integrations, and scheduled tasks.
- **Security:** All sensitive data operations and business logic are routed through Functions to enforce security rules and data isolation.
- **Key Functions:**
    - User authentication and role management.
    - CRUD operations for business data (products, clients, etc.) with `businessId` enforcement.
    - Processing of QR/Barcode scan data.
    - Data import processing and validation.
    - Scheduled database backups.
    - Integration with third-party APIs (e.g., payment gateways, advertising).

#### d. Google Cloud Storage
- **Purpose:** Stores Firestore database backups and temporary files for data import/export.

### 3. Deployment (Firebase Hosting)
- **Purpose:** Hosts the static React application files and serves Firebase Functions endpoints.
- **Integration:** Seamless integration with other Firebase services.

## Data Flow Example (Updating Stock via Scan)
1.  User (e.g., `employee`) scans a QR/Barcode in the Frontend.
2.  Frontend sends the scanned data and `businessId` to a Firebase Function.
3.  Firebase Function:
    a.  Authenticates the user and verifies their permissions for stock updates within their `businessId`.
    b.  Validates the scanned data against product records in Firestore (using `businessId`).
    c.  Updates the product's stock quantity in Firestore.
    d.  Logs the action in the `auditLogs` collection.
4.  Firebase Function returns success/failure to the Frontend.

## Scalability and Modularity
- New modules can be added as separate React components/pages and corresponding Firebase Functions/Firestore collections.
- Role-based access ensures that new features can be restricted by subscription tier.
- Serverless architecture of Firebase Functions scales automatically with demand.
