# Automated Backup System in Azure

## Project Summary

This project builds an automated backup system in Microsoft Azure using Terraform.

The goal of this project was to create a practical backup and recovery solution that protects business data, supports file versioning, manages storage costs, monitors backup activity, and sends a daily confirmation email when the backup container is checked.

This project demonstrates how Azure Blob Storage, lifecycle management, monitoring, alerting, and Logic Apps can work together to solve a real business problem.

## Business Problem

Many small businesses still rely on manual backup processes such as copying files to an external drive, manually uploading files, or depending on one person to remember the backup task.

This creates several risks:

- Backups may be missed
- Files may be accidentally overwritten
- Deleted files may not be recoverable
- Storage costs may grow without controls
- Business owners may not know whether backups are working

This project solves that problem by creating an automated Azure-based backup system with recovery, monitoring, and cost-control features.

## Solution Overview

The solution uses Azure Blob Storage as the backup destination. Blob versioning and soft delete protect files from accidental changes or deletion. Lifecycle management policies move older files to cheaper storage tiers and delete old versions after a defined retention period.

A Logic App checks the backup container and sends a daily confirmation email. Azure Monitor and Log Analytics collect backup activity logs and support alerting if backup writes stop.

## Azure Services Used

- Azure Blob Storage
- Azure Storage Containers
- Blob Versioning
- Blob Soft Delete
- Container Soft Delete
- Lifecycle Management Policies
- Log Analytics Workspace
- Azure Monitor Diagnostic Settings
- Azure Monitor Action Group
- Azure Monitor Alert Rule
- Azure Logic Apps
- Outlook.com Email Connector
- Terraform
- Azure CLI
- GitHub

## Final Resource Names Used

| Resource Type | Name |
|---|---|
| Resource Group | `rg-backup-kylon` |
| Storage Account | `stbackupkylon` |
| Documents Container | `documents` |
| Database Exports Container | `database-exports` |
| Application Files Container | `application-files` |
| Log Analytics Workspace | `law-backup-kylon` |
| Diagnostic Setting | `diag-storage-to-law` |
| Action Group | `ag-backup-kylon` |
| Alert Rule | `alert-no-backup-writes` |
| Logic App | `la-backup-confirm-kylon` |

## Architecture

```text
rg-backup-kylon
├── Storage Account: stbackupkylon
│   ├── Container: documents
│   ├── Container: database-exports
│   ├── Container: application-files
│   ├── Blob versioning enabled
│   └── Soft delete enabled
├── Lifecycle Management Policy
│   ├── Move base blobs to Cool after 30 days
│   ├── Move base blobs to Archive after 90 days
│   ├── Delete base blobs after 365 days
│   └── Delete older versions after 30 days
├── Log Analytics Workspace
├── Storage Diagnostic Settings
├── Azure Monitor Action Group
├── Azure Monitor Alert Rule
└── Logic App daily confirmation workflow
```

## Repository Structure

```text
backup-system-001/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars.example
├── README.md
├── .gitignore
└── screenshots/
    ├── 01-terraform-apply-success.png
    ├── 02-resource-group-overview.png
    ├── 03-storage-account-overview.png
    ├── 04-storage-containers-created.png
    ├── 05-blob-versioning-enabled.png
    ├── 06-lifecycle-management-policy-base-blobs.png
    ├── 07-lifecycle-management-policy-versions.png
    ├── 08-storage-diagnostic-settings-overview.png
    ├── 09-storage-diagnostic-settings-details.png
    ├── 10-action-group-email-notification.png
    ├── 11-blob-versioning-test-output.png
    ├── 12-logic-app-workflow.png
    ├── 13-logic-app-run-success.png
    ├── 14-backup-confirmation-email.png
    └── 15-terraform-destroy-success.png
```

## Terraform Deployment Commands

### Initialize Terraform

```zsh
terraform init
```

### Format and Validate

```zsh
terraform fmt
terraform validate
```

### Review the Deployment Plan

```zsh
terraform plan
```

### Deploy the Infrastructure

```zsh
terraform apply
```

Type `yes` when prompted.

## Screenshot Evidence

### 1. Terraform Apply Success

![Terraform Apply Success](screenshots/01-terraform-apply-success.png)

Terraform successfully deployed the Azure resources for the backup system.

### 2. Resource Group Overview

![Resource Group Overview](screenshots/02-resource-group-overview.png)

All project resources were deployed into a dedicated resource group.

### 3. Storage Account Overview

![Storage Account Overview](screenshots/03-storage-account-overview.png)

The storage account was configured with geo-redundant storage, TLS 1.2, blob versioning, and soft delete.

### 4. Storage Containers

![Storage Containers Created](screenshots/04-storage-containers-created.png)

Three private containers were created to separate backup data by type.

### 5. Blob Versioning and Soft Delete

![Blob Versioning Enabled](screenshots/05-blob-versioning-enabled.png)

Blob versioning, blob soft delete, and container soft delete were enabled to support recovery from accidental deletion or overwrites.

### 6. Lifecycle Management - Base Blobs

![Lifecycle Management Policy - Base Blobs](screenshots/06-lifecycle-management-policy-base-blobs.png)

Base blobs were configured to move to Cool storage after 30 days, Archive storage after 90 days, and delete after 365 days.

### 7. Lifecycle Management - Versions

![Lifecycle Management Policy - Versions](screenshots/07-lifecycle-management-policy-versions.png)

Older blob versions were configured for deletion after 30 days to control storage growth.

### 8. Diagnostic Settings Overview

![Diagnostic Settings Overview](screenshots/08-storage-diagnostic-settings-overview.png)

Diagnostic settings were enabled for the Blob service.

### 9. Diagnostic Settings Details

![Diagnostic Settings Details](screenshots/09-storage-diagnostic-settings-details.png)

Storage Read, Storage Write, Storage Delete, and Transaction logs were routed to Log Analytics.

### 10. Action Group Email Notification

![Action Group Email Notification](screenshots/10-action-group-email-notification.png)

An Azure Monitor Action Group was configured for email notifications.

### 11. Blob Versioning Test Output

![Blob Versioning Test Output](screenshots/11-blob-versioning-test-output.png)

A test file was uploaded, overwritten, and listed with versions included. The output confirmed multiple versions of the same blob existed.

### 12. Logic App Workflow

![Logic App Workflow](screenshots/12-logic-app-workflow.png)

The Logic App workflow checks the backup container and sends a confirmation email.

### 13. Logic App Run Success

![Logic App Run Success](screenshots/13-logic-app-run-success.png)

The Logic App was manually tested and completed successfully.

### 14. Backup Confirmation Email

![Backup Confirmation Email](screenshots/14-backup-confirmation-email.png)

The confirmation email verified that the backup system was active and the documents container was checked.

### 15. Terraform Destroy Success

![Terraform Destroy Success](screenshots/15-terraform-destroy-success.png)

After documentation was completed, the Azure resources were destroyed to avoid unnecessary costs.

## Implementation Steps

### 1. Project Folder Setup

```zsh
mkdir ~/backup-system-001
cd ~/backup-system-001
touch main.tf variables.tf outputs.tf terraform.tfvars
```

### 2. Terraform Configuration

Terraform was used to deploy the core Azure infrastructure, including the resource group, storage account, containers, lifecycle management policy, Log Analytics workspace, diagnostic settings, action group, Logic App, and monitor alert.

### 3. Storage Account Configuration

The storage account was configured with:

- Standard performance tier
- Geo-redundant storage
- TLS 1.2 minimum version
- Blob versioning
- Blob soft delete
- Container soft delete

### 4. Storage Container Design

Three private containers were created:

- `documents`
- `database-exports`
- `application-files`

This structure separates backup data by type and makes recovery easier during an incident.

### 5. Lifecycle Management

Lifecycle rules were configured to reduce long-term storage costs:

- Move current blobs to Cool storage after 30 days
- Move current blobs to Archive storage after 90 days
- Delete current blobs after 365 days
- Delete old versions after 30 days

### 6. Monitoring and Diagnostic Logging

Diagnostic settings were configured to send storage logs and transaction metrics to Log Analytics.

This provides visibility into read, write, and delete activity on backup data.

### 7. Logic App Confirmation Workflow

A Logic App was configured with the following flow:

```text
Recurrence trigger
↓
List blobs in documents container
↓
Send daily backup confirmation email
```

The Logic App confirms that the backup container can be checked successfully.

### 8. Blob Versioning Test

A test file was uploaded to the `documents` container, overwritten, and then listed using Azure CLI with versions included.

This verified that blob versioning was working correctly.

## Verification Checklist

- Storage account deployed successfully
- Three private containers created
- Blob versioning enabled
- Blob soft delete enabled for 30 days
- Container soft delete enabled for 30 days
- Lifecycle policy created
- Diagnostic settings routed to Log Analytics
- Azure Monitor Action Group created
- Alert rule created for missing backup writes
- Logic App workflow published
- Backup confirmation email received
- Test blob uploaded and overwritten
- Multiple blob versions confirmed
- Terraform destroy completed successfully

## Troubleshooting and Fixes

### Azure CLI Blob Upload Permission Error

When uploading a test file with `--auth-mode login`, Azure returned a permissions error because the signed-in identity did not have the required Storage Blob Data Contributor role.

To continue the lab, the storage account key was extracted from the Terraform output:

```zsh
ACCOUNT_KEY=$(terraform output -raw storage_account_connection_string | sed -n 's/.*AccountKey=\([^;]*\).*/\1/p')
```

The upload command was then rerun using:

```zsh
--auth-mode key --account-key "$ACCOUNT_KEY"
```

### Gmail Connector Policy Error

The Gmail connector could not be used with the Azure Blob connector because Azure Logic Apps blocked the connector combination.

The workflow was completed using an Outlook.com connector instead.

### Terraform Destroy Resource Group Error

During cleanup, Terraform could not delete the resource group because manually created Logic App API connections still existed.

The leftover `Microsoft.Web/connections` resources were deleted manually, then `terraform destroy` was rerun successfully.

## Security Notes

Sensitive Terraform files were excluded from GitHub using `.gitignore`.

The following files and folders were not committed:

```text
.terraform/
terraform.tfvars
terraform.tfstate
terraform.tfstate.backup
*.tfplan
```

This helps prevent secrets, connection strings, state data, and environment-specific values from being exposed publicly.

## Cleanup

Resources were destroyed after the project was completed and documented.

```zsh
terraform destroy
```

This prevents unnecessary Azure charges.

## Lessons Learned

This project reinforced the importance of designing backups with both recovery and cost control in mind.

Key lessons learned:

- Blob versioning protects against accidental overwrites
- Soft delete provides a recovery window for deleted data
- Lifecycle management helps control long-term storage costs
- Monitoring and alerts are important for validating backup activity
- Logic Apps can automate operational confirmation emails
- Terraform state and variable files should never be pushed to public repositories
- Manually created portal resources may require extra cleanup during teardown

## Skills Demonstrated

- Infrastructure as Code with Terraform
- Azure Blob Storage backup design
- Blob versioning and retention
- Storage lifecycle cost management
- Azure Monitor alerting
- Log Analytics diagnostic collection
- Logic Apps workflow automation
- Azure CLI troubleshooting
- Terraform cleanup and resource teardown
- Cloud documentation and GitHub portfolio presentation
