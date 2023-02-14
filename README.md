## Healthwatch for VMware Tanzu Documentation

This is the source repository for the Healthwatch for VMware Tanzu documentation.

**Note**: Use the version-specific branches to create PRs or make changes to the project. The **MASTER** branch is not updated.

| Branch Name| Use for… | URL |
|------------| ---------|-----|
| master     | DO NOT USE | _n/a_ |
| 2.2        | 2.2 | https://docs.vmware.com/en/Healthwatch-for-VMware-Tanzu/2.2/healthwatch/index.html |
| 2.1        | 2.1 | https://docs.vmware.com/en/Healthwatch-for-VMware-Tanzu/2.1/healthwatch/index.html |
| 2.0        | 2.0 beta | http://docs.pivotal.io/platform/healthwatch/2-0 |
| 1.8        | 1.8 | https://docs.pivotal.io/healthwatch/1-8 |
| 1.7        | 1.7 | https://docs.pivotal.io/healthwatch/1-7 |
| 1.6        | 1.6 | https://docs.pivotal.io/healthwatch/1-6 |
| 1.5        | 1.5 | https://docs.pivotal.io/healthwatch/1-5 |
| 1.4        | 1.4 | https://docs.pivotal.io/healthwatch/1-4 |
| 1.3        | 1.3 | https://docs.pivotal.io/healthwatch/1-3 |
| 1.2        | 1.2 | https://docs.pivotal.io/healthwatch/1-2 |
| 1.1        | 1.1 | https://docs.pivotal.io/healthwatch/1-1 |
| 1.0        | 1.0 | https://docs.pivotal.io/healthwatch/1-0 |

1.8 and earlier is end of general support.


## Publishing 2.1 and later

The 2.1 and later docs have been migrated to docs.vmware.com and are now published using the following tools:

- [docworks](https://docworks.vmware.com/) is the main tool for managing docs used by writers.
- [docsdash](https://docsdash.vmware.com/) is a deployment UI which manages the promotion from
staging to pre-prod to production. The process below describes how to upload our docs to staging,
replacing the publication with the same version.

### Prepare Markdown Files

This repo contains the following files:

- Markdown files live in this repo.
- The table of contents is now in this repo in the [healthwatch-toc.md](healthwatch-toc.md) file. Each page requires an entry in the TOC.

### In DocWorks

1. Run a build of the "Healthwatch for VMware Tanzu" project to upload the docs to Docs Dash.
2. Review your changes on the staging site https://docs-staging.vmware.com/...

### In Docsdash

1. Wait about 1 minute for processing to complete after uploading.
2. Go to https://docsdash.vmware.com/deployment-stage.

### Promoting to Pre-Prod and Prod

**Prerequisite** Needs additional privileges - reach out to a manager on the docs team [#tanzu-docs](https://vmware.slack.com/archives/C055V2M0H) or ask a writer to do this step for you.

1. Go to Staging publications in docsdash  
  https://docsdash.vmware.com/deployment-stage

2. Select a publication (make sure it's the latest version)

3. Click "Deploy selected to Pre-Prod" and wait for the pop to turn green (refresh if necessary after about 10s)

4. Go to Pre-Prod list  
  https://docsdash.vmware.com/deployment-pre-prod

5. Select a publication

6. Click "Sign off for Release"

7. Wait for your username to show up in the "Signed off by" column

8. Select the publication again

9. Click "Deploy selected to Prod"


## For 1.8 and earlier

Book repo: https://github.com/pivotal-cf/docs-book-healthwatch

Production pipeline: https://concourse.run.pivotal.io/teams/cf-docs/pipelines/cf-services?group=healthwatch

### How to run the docs locally
1. Clone the docs layout repository into your workspace:

```
cd ~/workspace
git clone git@github.com:pivotal-cf/docs-layout-repo.git
```

1. Follow the instructions in https://github.com/pivotal-cf-experimental/bookdocker,
   with the following modification:

    1. Before you copy `bookdocker` into your path,
    manually edit the Dockerfile configuration inside the `bookdocker/bookdocker` script
    from `FROM ruby:2.3` to `FROM ruby:2.4`
    so that it works with the gems we have in `docs-pcf-healthwatch`.

1. `cd` into `docs-book-healthwatch` and edit the `config.yml`
    1. Under the `sections:` comment out the docs branch that you are not currently working on.
       This is needed to have `bookdocker watch` work as expected.

       **Note:** Don't commit this file.
1. Run `bookdocker watch` in `docs-book-healthwatch` folder,
   and you will get local preview under: `http://127.0.0.1:4567/platform/healthwatch/<VERSION>/installing.html`
   If it doesn't work, run it again, and again... and if that doesn't work, ask for help in the #pcf-docs Slack channel.
