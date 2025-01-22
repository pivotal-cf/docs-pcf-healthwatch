# Healthwatch for VMware Tanzu Documentation

This is the source repository for the Healthwatch for VMware Tanzu documentation.

## Branches in this Repo

>**Note**
>Use the version-specific branches to create PRs or make changes to the project. The `master` branch is not updated.

>**Note**
>The docs.pivotal.io site has been decommissioned and the older versions are no longer accessible. Ideally, these will be archived to pdfs at some point.

| Branch Name| Status     | URL |
|------------| -----------|-----|
| master     | DO NOT USE | n/a |
| 2.3        | prod | https://techdocs.broadcom.com/us/en/vmware-tanzu/platform-services/healthwatch-for-vmware-tanzu/2-3/healthwatch/index.html |
| 2.2        | prod | https://techdocs.broadcom.com/us/en/vmware-tanzu/platform-services/healthwatch-for-vmware-tanzu/2-2/healthwatch/index.html |
| 2.1        | EOGS | archived in Broadcom Box folder |
| 2.0        | EOGS | archived |
| 1.8        | EOGS | archived |
| 1.7        | EOGS | archived |
| 1.6        | EOGS | archived |
| 1.5        | EOGS | archived |
| 1.4        | EOGS | archived |
| 1.3        | EOGS | archived |
| 1.2        | EOGS | archived |
| 1.1        | EOGS | archived |
| 1.0        | EOGS | archived |

EOGS = End of General Support


## Publishing 2.1 and later

The 2.1 and later docs have been migrated to https://techdocs.broadcom.com/ and are now published using the following tools:

- [docworks](https://docworks.broadcom.net/) is the main tool for managing docs used by writers.
- Publishing is done by the Broadcom AEM team -- the writer must raise a ticket.

### Prepare Markdown Files

This repo contains the following files:

- Markdown files live in this repo.
- The table of contents is now in this repo in the [healthwatch-toc.md](healthwatch-toc.md) file. Each page requires an entry in the TOC.

### In DocWorks

1. Run a build of the "Healthwatch for VMware Tanzu" project to upload the docs to Docs Dash.
2. Review your changes on the staging site https://author-techdocs2-prod.adobecqms.net...
