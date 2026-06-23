# Automated Backup System in Azure

## Project Overview

This project builds an automated backup system in Azure using Terraform. The goal is to replace manual file backup processes with a cloud-based solution that supports file versioning, retention, lifecycle cost management, monitoring, alerting, and daily backup confirmation emails.

## Business Problem

Many small businesses rely on manual backups, which creates risk when files are forgotten, overwritten, deleted, or stored without a recovery plan.

This project solves that by creating a backup system that:

- Stores files in Azure Blob Storage
- Keeps previous versions of overwritten files
- Enables soft delete for blob and container recovery
- Moves older files to cheaper storage tiers using lifecycle policies
- Sends a daily backup confirmation email
- Monitors storage activity through Log Analytics and Azure Monitor

## Azure Services Used

- Azure Blob Storage
- Azure Storage Containers
- Blob Versioning
- Soft Delete
- Lifecycle Management Policies
- Log Analytics Workspace
- Azure Monitor Diagnostic Settings
- Azure Monitor Action Group
- Azure Monitor Alert Rule
- Azure Logic Apps
- Outlook.com Email Connector
- Terraform
- Azure CLI

## Architecture

The project deploys the following resources:

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
