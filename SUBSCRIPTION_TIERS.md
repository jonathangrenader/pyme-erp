# Subscription Tiers & Monetization Strategy

This document details the different subscription tiers for the pyme-erp application and the monetization strategy.

## Core Principles
- **Value-Based Pricing:** Tiers are designed to offer increasing value and functionality.
- **Scalability:** Features are unlocked as businesses grow and require more advanced tools.
- **Monetization:** A mix of advertising (free tier) and recurring subscriptions.

## Subscription Tiers

### 1. Free Tier
- **Target Audience:** Very small businesses, startups, or those wanting to test the basic functionality.
- **Modules Included:**
    - **Stock:** Basic functionality, limited to a specific number of products (e.g., 50 products).
    - **Clients:** Basic functionality, limited to a specific number of clients (e.g., 20 clients).
- **Monetization:**
    - **Google AdSense:** Advertisements will be displayed within the application interface.
- **Limitations:**
    - No QR/Barcode scanning.
    - Limited data storage.
    - Standard support (community forum).
    - No advanced features.
- **Cost Considerations:** Designed to operate within Firebase's free tier limits. Software limits are set below Firebase's free tier to prevent unexpected costs.

### 2. Basic Tier
- **Target Audience:** Small businesses needing core management tools without advanced features.
- **Modules Included:**
    - All Free Tier features, with increased limits or unlimited access.
    - **Stock:** Unlimited products, basic QR/Barcode scanning.
    - **Clients:** Unlimited clients.
    - **Suppliers:** Basic module.
    - **Price Lists:** Basic module.
    - **Import Module:** Basic CSV import for products and clients.
- **Monetization:** Monthly or annual subscription fee.
- **Limitations:**
    - No advertising.
    - Standard support.
    - No advanced features (e.g., batch scanning, advanced reporting, invoicing).
- **Cost Considerations:** May incur Firebase costs if usage exceeds Firebase's free tier limits.

### 3. Pro Tier
- **Target Audience:** Growing businesses requiring more comprehensive management and automation.
- **Modules Included:**
    - All Basic Tier features.
    - **Suppliers:** Advanced module.
    - **Price Lists:** Advanced module.
    - **Reporting:** Basic reports.
    - **Stock:** Advanced QR/Barcode scanning (e.g., batch scanning).
    - **Import Module:** Advanced (CSV/Excel, flexible mapping, suppliers, price lists).
    - **Integrations:** Basic payment gateway integration (e.g., Stripe for client payments).
- **Monetization:** Higher monthly or annual subscription fee.
- **Limitations:**
    - Priority support.
    - Access to beta features.
- **Cost Considerations:** Higher Firebase usage (Functions, Firestore, Cloud Storage). Third-party service fees (e.g., Stripe transaction fees).

### 4. Enterprise Tier
- **Target Audience:** Larger PYMES or businesses with specific, complex requirements.
- **Modules Included:**
    - All Pro Tier features.
    - **Invoicing:** Full invoicing module.
    - **Accounting:** Basic accounting features.
    - **Multi-user:** Advanced user management with granular permissions.
    - **Custom Integrations:** Tailored integrations with other systems.
    - **Dedicated Support:** Priority and dedicated support.
- **Monetization:** Custom pricing, typically annual contracts.
- **Limitations:**
    - Highest level of service and customization.
- **Cost Considerations:** Significant Firebase usage, potential for custom development costs, and third-party API fees.

## Implementation of Tiers
- **Firebase Authentication Custom Claims:** Used to store high-level user roles (e.g., `SUPERADMIN`, `COMPANY_ADMIN`).
- **Firestore `businesses` Collection:** Stores the `subscriptionTier` for each business and flags for `modulesEnabled` and `featureEnabled` (e.g., `qrScanEnabled: true`).
- **Firebase Functions:** Enforce access control by validating user claims and business configurations before executing operations.
- **Frontend UI:** Dynamically renders or hides UI elements (modules, buttons, menu items) based on the user's `subscriptionTier` and the `modulesEnabled` configuration of their business.
- **Google AdSense Integration:** A dedicated React component will display ads only for users on the Free Tier.
