# Project: Sharing Files Securely with Azure Blob Storage

## Executive Summary
This project demonstrates how to securely share files with an external partner using **Azure Blob Storage, stored access policies, Shared Access Signatures (SAS), and lifecycle management**. By leveraging fine-grained permission controls, we ensure temporary, secure data sharing without exposing storage accounts publicly or requiring user authentication.

---

## 1. Architecture & Core Concepts
* **Private Blob Containers:** Restricts public or anonymous access, ensuring data remains completely isolated and secure by default.
* **Shared Access Signature (SAS):** A secure URI that grants delegated, time-limited, and permission-scoped access to storage objects without exposing account keys.
* **Stored Access Policies:** Provides a centralized management layer on containers to control, audit, and instantly revoke associated SAS tokens.
* **Lifecycle Management:** Automates asset governance by defining rules to move or delete blobs based on age thresholds (e.g., auto-cleanup after 30 days).

---

## 2. Implementation Workflow

### Phase 1: Storage Infrastructure & Provisioning
1. **Provision Environment:** Create a dedicated Resource Group and General-purpose Storage Account.
2. **Create Private Container:** Navigate to **Data Storage** to set up a private container intended for secure file exchanges.
   * *Screenshot Reference:* ![Create private container](images-task-1/create-private-container.png)
3. **Upload File:** Upload the sample report file into the private container and verify its presence.
   * *Screenshot Reference:* ![Upload file](images-task-1/upload-file.png) | ![Uploaded file](images-task-1/file-listed.png)

### Phase 2: Access Policies & SAS Generation
1. **Configure Access Policy:** Create a stored access policy on the container to define custom permissions and expiration terms.
   * *Screenshot Reference:* ![Add stored access policy](images-task-2/stored-policy-add.png) | ![Stored access policy form](images-task-2/policy-form.png)
2. **Generate SAS Token:** Generate a secure SAS URL mapped to the stored access policy for partner distribution.
   * *Screenshot Reference:* ![Generate SAS](images-task-2/select-policy.png)

### Phase 3: Lifecycle Management Configuration
1. **Navigate to Lifecycle Rules:** Access the storage account's lifecycle management section to automate cleanup routines.
   * *Screenshot Reference:* ![Navigate to lifecycle management](images-task-5/nav-to-life-rules.png)
2. **Configure Expiration Rules:** Set rule parameters specifying blob prefixes (e.g., target container path with a `/`) and age limits (e.g., auto-delete files after 30 days).
   * *Screenshot References:* ![Lifecycle rule configuration](images-task-5/life-rules-form-1.png), ![Lifecycle rule configuration](images-task-5/life-rules-form-2.png), ![Lifecycle rule configuration](images-task-5/life-rules-form-3.png)

---

## 3. Validation & Testing

### Access Verification Matrix
* **Direct Access Check:** Attempting to access the raw blob URL without token parameters results in an access denied error. 
  * *Screenshot Reference:* ![Direct blob URL](images-task-3/access-denied.png)
* **SAS Token Access Check:** Accessing the file using the generated SAS URL successfully authenticates and serves the file in an unauthenticated or private session. 
  * *Screenshot Reference:* ![SAS token access](images-task-3/sas-token-access.png)

### Revocation & Troubleshooting
* **Revoking Access:** Deleting the underlying stored access policy instantly invalidates the associated SAS token while preserving the raw file in storage.
  * *Screenshot References:* ![Delete stored access policy](images-task-4/delete-save-policy.png), ![Verify access was revoked](images-task-4/browser-check.png)
* **Troubleshooting Note:** Ensuring the correct policy is targeted is critical; mismatched policy bindings can leave active SAS tokens functioning until explicitly re-aligned or expired.

---

## 4. Resource Cleanup
To prevent ongoing cloud costs, delete the parent Resource Group, which cascades deletion to all child assets (Storage Account, container files, and policies).
* *Verification:* Confirming that the container endpoints are fully terminated and the resource group no longer exists in the Azure Portal.

---

## 5. Key Takeaways & Learnings
* **Delegated Access Control:** SAS tokens provide granular, time-bound access without sharing account credentials or opening containers to public visibility.
* **Centralized Revocation:** Stored access policies act as a powerful kill-switch, allowing immediate revocation of distributed tokens without modifying underlying file storage.
* **Automated Governance:** Storage lifecycle rules remove the administrative burden of manual data deletion, ensuring compliance with data retention requirements.
* **Security Discipline:** Credentials and raw SAS URLs must be strictly safeguarded and excluded from version control and documentation files.