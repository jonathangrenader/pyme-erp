# Database Backup Strategy (Firestore)

This document outlines the strategy for backing up the Firestore database for the pyme-erp application.

## Importance
Regular backups are crucial for disaster recovery, data integrity, and ensuring business continuity in case of accidental data loss or corruption.

## Mechanism

### 1. Automated Scheduled Exports
- **Service:** Google Cloud Firestore Managed Export and Import Service.
- **Frequency:** Daily exports will be configured. The exact time will be chosen to minimize impact during peak usage hours (e.g., late night/early morning UTC).
- **Scope:** Full database export (all collections).
- **Destination:** A dedicated Google Cloud Storage bucket (e.g., `gs://pyme-erp-firestore-backups`).
- **Configuration:**
    - This will be set up using Google Cloud Console or `gcloud` CLI commands.
    - A **Cloud Scheduler** job will trigger a **Cloud Function** (or directly trigger the Firestore export API) to initiate the export process.
- **Retention Policy:** Google Cloud Storage bucket will be configured with a lifecycle policy to automatically delete older backups (e.g., retain backups for 30 days).

### 2. Access for SUPERADMIN (Developer)
- **Frontend Module:** A dedicated section in the SUPERADMIN dashboard will allow the developer to:
    - View a list of available backup files in the Google Cloud Storage bucket.
    - Generate signed URLs for these backup files, allowing secure, temporary download to a local machine.
- **Security:** Access to this module and the underlying Firebase Function will be strictly limited to the `SUPERADMIN` role via Firebase Authentication Custom Claims and Firebase Functions security rules.

### 3. Manual On-Demand Exports (Optional, Future)
- The SUPERADMIN dashboard could include an option to trigger a manual full database export immediately. This would also be handled by a Firebase Function.

## Restoration Process
- **Manual Process:** Database restoration is a manual process performed by the developer (SUPERADMIN).
- **Steps:**
    1.  Download the desired backup file from Google Cloud Storage.
    2.  Use the `gcloud firestore import` command-line tool to import the data into a new or existing Firestore database.
    3.  **Important:** Restoration should ideally be done to a *new* or *staging* database first to verify data integrity before considering a full production overwrite (which is generally not recommended for live systems).

## Cost Considerations
- **Firestore Export Operations:** Incur costs based on the amount of data exported.
- **Google Cloud Storage:** Incur costs based on the amount of data stored and network egress (downloading backups).
- These costs are typically low for standard usage but will be managed by the developer and are part of the operational overhead of the platform. They are not directly passed to the end-users of the ERP.
