# Project Roadmap

This document outlines the long-term vision and planned development phases for the pyme-erp application.

## Vision
To become a comprehensive, modular, and user-friendly ERP solution for small and medium-sized businesses, offering scalable features and flexible subscription plans.

## Phase 1: Minimum Viable Product (MVP) - Current Focus
- **Core Setup:** React (TypeScript) + Firebase initialization, project structure.
- **Authentication Module:** User registration, login, logout, route protection.
- **SUPERADMIN & Multi-company Base:**
    - SUPERADMIN user setup.
    - SUPERADMIN interface to create new businesses.
    - Basic `businesses` collection in Firestore.
- **Basic Stock Module:**
    - CRUD (Create, Read, Update, Delete) for products.
    - Data isolation per `businessId`.
    - Software limits for free tier products.
    - UI preparation for QR/Barcode scanning (component integration).
- **Initial Deployment:** Firebase Hosting setup.

## Phase 2: Core Modules & Basic Monetization
- **Full Stock Module:**
    - QR/Barcode scanning for stock updates (basic functionality).
    - Stock adjustments (in/out).
    - Stock alerts.
- **Clients Module:**
    - CRUD for clients.
    - Basic client management.
- **Suppliers Module:**
    - CRUD for suppliers.
    - Basic supplier management.
- **Price Lists Module:**
    - Creation and management of multiple price lists.
- **User Management (COMPANY_ADMIN):**
    - COMPANY_ADMIN can invite/manage users within their business.
    - Basic role assignment (e.g., employee, manager).
- **Subscription Tier Enforcement:**
    - Implement logic to restrict module access based on `subscriptionTier`.
    - Integrate Google AdSense for Free Tier.

## Phase 3: Advanced Features & Pro Tier
- **Advanced Stock:**
    - Batch QR/Barcode scanning.
    - Inventory counts.
- **Reporting Module:**
    - Basic sales reports.
    - Stock movement reports.
- **Invoicing Module (Basic):**
    - Create simple invoices.
    - Track invoice status.
- **Import Module:**
    - Basic CSV import for products and clients.
- **Payment Gateway Integration:**
    - Setup for a payment gateway (e.g., Stripe) for subscription management.
- **Audit Module (Basic):**
    - Logging of key user actions.
    - SUPERADMIN view of audit logs.

## Phase 4: ERP Expansion & Enterprise Tier
- **Full Invoicing Module:**
    - Advanced invoicing features (discounts, taxes, recurring invoices).
    - Integration with payment gateways for client payments.
- **Accounting Module (Basic):**
    - Expense tracking.
    - Basic financial reports.
- **Advanced Reporting:**
    - Customizable reports.
    - Data visualization dashboards.
- **Advanced Import/Export:**
    - Support for Excel, more complex data structures.
    - Data export functionality.
- **Multi-user Permissions (Granular):**
    - COMPANY_ADMIN can define custom roles and assign granular permissions.
- **Integrations:**
    - API integrations with other business tools.
- **Notifications:**
    - In-app and email notifications for critical events.

## Future Considerations
- Mobile App (Native/Hybrid)
- Point of Sale (POS) integration
- E-commerce integration
- Advanced analytics and AI insights
