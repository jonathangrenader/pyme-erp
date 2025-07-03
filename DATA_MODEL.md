# Data Model (Firestore)

This document outlines the initial data model for the pyme-erp application in Firestore. All business-specific data will be associated with a `businessId` to ensure multi-tenancy.

## Core Collections

### 1. `users` (Collection)
- Stores user profiles, linked to Firebase Authentication UIDs.
- **Document ID:** Firebase Authentication `uid`
- **Fields:**
    - `uid`: String (Firebase Auth User ID)
    - `email`: String
    - `displayName`: String
    - `businessId`: String (ID of the business the user belongs to)
    - `role`: String (e.g., 'company_admin', 'employee', 'manager' - primary role within the business)
    - `createdAt`: Timestamp
    - `updatedAt`: Timestamp

### 2. `businesses` (Collection)
- Stores configuration and metadata for each registered company.
- **Document ID:** Auto-generated (or custom, if preferred)
- **Fields:**
    - `businessId`: String (Unique ID for the business)
    - `name`: String (Company name)
    - `contactEmail`: String
    - `contactPhone`: String
    - `address`: String
    - `modulesEnabled`: Map (e.g., `{ stock: true, clients: true, invoicing: false }`)
    - `subscriptionTier`: String (e.g., 'free', 'basic', 'pro', 'enterprise')
    - `productLimit`: Number (for free tier)
    - `clientLimit`: Number (for free tier)
    - `createdAt`: Timestamp
    - `updatedAt`: Timestamp

### 3. `products` (Collection)
- Stores product information for each business.
- **Document ID:** Auto-generated
- **Fields:**
    - `businessId`: String (ID of the business this product belongs to)
    - `name`: String
    - `description`: String (Optional)
    - `sku`: String (Stock Keeping Unit, unique per business)
    - `barcode`: String (Optional, for QR/barcode scanning)
    - `purchasePrice`: Number
    - `salePrice`: Number
    - `currentStock`: Number
    - `minStockAlert`: Number (Optional)
    - `category`: String (Optional)
    - `supplierId`: String (Optional, reference to `suppliers` collection)
    - `createdAt`: Timestamp
    - `updatedAt`: Timestamp

### 4. `clients` (Collection)
- Stores client information for each business.
- **Document ID:** Auto-generated
- **Fields:**
    - `businessId`: String (ID of the business this client belongs to)
    - `name`: String
    - `contactPerson`: String (Optional)
    - `email`: String (Optional)
    - `phone`: String (Optional)
    - `address`: String (Optional)
    - `createdAt`: Timestamp
    - `updatedAt`: Timestamp

### 5. `suppliers` (Collection)
- Stores supplier information for each business.
- **Document ID:** Auto-generated
- **Fields:**
    - `businessId`: String (ID of the business this supplier belongs to)
    - `name`: String
    - `contactPerson`: String (Optional)
    - `email`: String (Optional)
    - `phone`: String (Optional)
    - `address`: String (Optional)
    - `createdAt`: Timestamp
    - `updatedAt`: Timestamp

### 6. `priceLists` (Collection)
- Stores custom price lists for products (e.g., wholesale prices, special client prices).
- **Document ID:** Auto-generated
- **Fields:**
    - `businessId`: String (ID of the business this price list belongs to)
    - `name`: String (e.g., "Wholesale Prices", "VIP Clients")
    - `products`: Map (e.g., `{ productId1: { price: 10.50 }, productId2: { price: 25.00 } }`)
    - `createdAt`: Timestamp
    - `updatedAt`: Timestamp

### 7. `auditLogs` (Collection)
- Stores system-wide audit events (SUPERADMIN access only).
- **Document ID:** Auto-generated
- **Fields:**
    - `timestamp`: Timestamp
    - `userId`: String (UID of the user who performed the action)
    - `businessId`: String (ID of the business affected, if applicable)
    - `actionType`: String (e.g., 'LOGIN', 'PRODUCT_CREATED', 'MODULE_ACTIVATED', 'PERMISSION_CHANGED')
    - `details`: Map (Specific details of the action, e.g., `{ productId: 'xyz', oldStock: 10, newStock: 12 }`)
    - `ipAddress`: String (Optional)
    - `userAgent`: String (Optional)

## Relationships
- `users` to `businesses`: One-to-one (a user belongs to one business for their primary role).
- `products`, `clients`, `suppliers`, `priceLists` to `businesses`: Many-to-one (many items belong to one business).
- `products` to `suppliers`: Many-to-one (many products can come from one supplier).
