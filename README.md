## Pivotal Cloud Foundry Healthwatch Documentation

This is the source repository for the Pivotal Cloud Foundry (PCF) Healthwatch documentation.

**Note**: Use the version-specific branches to create PRs or make changes to the project. The **MASTER** branch is not updated.

| Branch Name| Use for… | URL |
|------------| ---------|-----|
| master     | DO NOT USE | _n/a_ |
| 2.1        | 2.1 edge branch (Beware it is stale, see [network](https://github.com/pivotal-cf/docs-pcf-healthwatch/network). Not all changes have been cherry-picked from 2.0 to 2.1.)| http://docs-pcf-staging.cfapps.io/platform/healthwatch/2-n |
| 2.0        | 2.0 beta | http://docs.pivotal.io/platform/healthwatch/2-0  |
| 1.8        | 1.8 | http://docs.pivotal.io/platform/healthwatch/1-8  |
| 1.7        | 1.7 | http://docs.pivotal.io/platform/healthwatch/1-7  |
| 1.6        | 1.6 | http://docs.pivotal.io/platform/healthwatch/1-6  |
| 1.5        | 1.5 | http://docs.pivotal.io/platform/healthwatch/1-5  |
| 1.4        | 1.4 | http://docs.pivotal.io/platform/healthwatch/1-4  |
| 1.3        | 1.3 | http://docs.pivotal.io/platform/healthwatch/1-3  |
| 1.2        | 1.2 | http://docs.pivotal.io/platform/healthwatch/1-2  |
| 1.1        | 1.1 | http://docs.pivotal.io/platform/healthwatch/1-1  |
| 1.0        | 1.0 | http://docs.pivotal.io/platform/healthwatch/1-0  |

1.5 and earlier is end of general support. 

Book repo: https://github.com/pivotal-cf/docs-book-healthwatch

Production pipeline: https://concourse.run.pivotal.io/teams/cf-docs/pipelines/cf-services?group=healthwatch
Edge pipeline: https://concourse.run.pivotal.io/teams/cf-docs/pipelines/cf-services-edge?group=healthwatch-edge

## How to run the docs locally
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
