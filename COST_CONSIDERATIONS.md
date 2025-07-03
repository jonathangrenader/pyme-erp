# Cost Considerations

This document outlines the potential costs associated with running the pyme-erp application, primarily focusing on Firebase services and third-party integrations. It also details the strategy to manage and control these costs, especially for lower subscription tiers.

## 1. Firebase Service Costs

Firebase offers a generous free tier for most of its services. However, exceeding these free tier limits will incur charges. Costs are generally usage-based.

### a. Firestore (Database)
- **Free Tier Limits:** 1 GB storage, 50k reads/day, 20k writes/day, 20k deletes/day.
- **Cost Drivers:**
    - **Storage:** Amount of data stored.
    - **Reads/Writes/Deletes:** Number of database operations.
    - **Network Egress:** Data transferred out of Firestore.
- **Strategy:**
    - **Free Tier (App):** Software limits (e.g., 50 products, 20 clients) are implemented to keep Firestore usage well within Firebase's free tier. This ensures the free version of the ERP does not incur Firebase costs.
    - **Paid Tiers (App):** As businesses grow and use more features, their Firestore usage will increase. Costs will be incurred if they exceed Firebase's free tier. These costs are part of the operational overhead covered by the subscription fee.

### b. Firebase Functions (Serverless Backend)
- **Free Tier Limits:** 2M invocations/month, 400k GB-seconds/month, 200k CPU-seconds/month.
- **Cost Drivers:**
    - **Invocations:** Number of times a function is called.
    - **Compute Time:** CPU and memory usage per invocation.
    - **Network Egress:** Data transferred out of Functions.
- **Strategy:**
    - **Free Tier (App):** Functions for basic authentication and core CRUD operations will be optimized for efficiency. Features like QR/Barcode scanning and data import will be **disabled** for the free tier to prevent unexpected Function invocations.
    - **Paid Tiers (App):** Advanced features (e.g., QR/Barcode scanning, data import, complex reports) will utilize Firebase Functions. Costs will be incurred if usage exceeds Firebase's free tier.

### c. Firebase Authentication
- **Free Tier Limits:** 10k monthly active users (MAU) for phone authentication, unlimited for email/password.
- **Cost Drivers:** Primarily phone authentication beyond free limits.
- **Strategy:** Email/password authentication is free. Phone authentication (if implemented) will be a feature for higher tiers or incur a separate charge.

### d. Google Cloud Storage (for Backups & Imports)
- **Free Tier Limits:** 5 GB storage, 5k Class A operations/month, 50k Class B operations/month, 1 GB network egress/month.
- **Cost Drivers:**
    - **Storage:** Amount of data stored (backups, temporary import files).
    - **Operations:** Number of times data is accessed.
    - **Network Egress:** Data downloaded from storage.
- **Strategy:**
    - **Backups:** Automated daily backups will be configured. The `SUPERADMIN` (developer) will manage these costs. They are part of the platform's operational cost and not directly passed to end-users.
    - **Imports:** Temporary storage of import files will be managed. Files will be deleted after processing to minimize storage costs.

### e. Firebase Hosting
- **Free Tier Limits:** 10 GB storage, 360 MB/day data transfer.
- **Cost Drivers:** Storage of static assets, data transfer.
- **Strategy:** Generally very cost-effective for web applications. Costs are minimal unless traffic is extremely high.

## 2. Third-Party Integration Costs

### a. Google AdSense (for Free Tier Monetization)
- **Cost Driver:** No direct cost to the developer for using AdSense. It's a revenue-sharing model.
- **Strategy:** Integrated into the Free Tier UI. Revenue generated helps offset Firebase costs.

### b. Payment Gateways (e.g., Stripe)
- **Cost Driver:** Transaction fees (percentage + fixed fee per transaction).
- **Strategy:** Integration will be a feature for Pro/Enterprise tiers. The ERP will facilitate the integration, but the transaction fees are typically borne by the business using the ERP (the end-user).

### c. SMS/Email Services (for Notifications - Future)
- **Cost Driver:** Per-message/per-email fees.
- **Strategy:** If implemented, these will be features for higher tiers or incur separate charges.

## 3. Cost Management Strategy

### a. Default Disabled for Cost-Generating Features
- All functionalities that can potentially incur significant costs (e.g., advanced QR/Barcode scanning, data import, payment gateway integrations, scheduled backups) will be **disabled by default** in the application's code and Firebase Functions.

### b. SUPERADMIN Control
- The `SUPERADMIN` will have a dedicated interface to enable these cost-generating features for specific businesses based on their subscription tier.
- This control will update flags in the `businesses` collection in Firestore (e.g., `features.advancedQrScan: true`).

### c. Firebase Functions for Enforcement
- Firebase Functions will check these flags before executing any logic related to cost-generating features. If the feature is not enabled for a given `businessId`, the Function will deny the request.

### d. Monitoring
- Regular monitoring of Firebase usage in the Google Cloud Console is essential to track costs and identify unexpected spikes.

## 4. Cost Summary by Tier (Developer Perspective)

- **Free Tier (App):** Minimal to no Firebase costs (aiming for free tier limits). AdSense revenue helps offset.
- **Basic Tier (App):** Low to moderate Firebase costs, depending on usage volume. Covered by subscription fee.
- **Pro Tier (App):** Moderate to high Firebase costs. Covered by subscription fee. Third-party transaction fees (e.g., Stripe) are typically passed to the end-user.
- **Enterprise Tier (App):** High Firebase costs. Covered by custom subscription fee. Potential for custom development costs.
