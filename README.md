# Check Point Cloudguard ShiftLeft Orb for CircleCI
[![chkp-mibraun](https://circleci.com/gh/chkp-mibraun/shiftleft-orb.svg?branch=0.9.0&style=shield)](https://github.com/chkp-mibraun/shiftleft-orb)
## Introduction

The Cloudguard ShiftLeft orb is a plugin for CircleCI that will scan source code, docker images, and Infrastructure as Code (IAC). ShiftLeft identifies both vulnerabilites and malware in source code & Docker images. You can also use ShiftLeft to check for compliance violations in IAC.

![](images/cloudguard_circleci.png)

<!---
[![CircleCI Build Status](https://circleci.com/gh/<organization>/<project-name>.svg?style=shield "CircleCI Build Status")](https://circleci.com/gh/<organization>/<project-name>) [![CircleCI Orb Version](https://img.shields.io/badge/endpoint.svg?url=https://badges.circleci.io/orb/<namespace>/<orb-name>)](https://circleci.com/orbs/registry/orb/<namespace>/<orb-name>) [![GitHub License](https://img.shields.io/badge/license-MIT-lightgrey.svg)](https://raw.githubusercontent.com/<organization>/<project-name>/master/LICENSE) [![CircleCI Community](https://img.shields.io/badge/community-CircleCI%20Discuss-343434.svg)](https://discuss.circleci.com/c/ecosystem/orbs)
--->

## Prerequisites

- [Cloudguard CSPM Account](https://secure.dome9.com) (w/ API Key) 
- [CircleCI](https://circleci.com)

## Configuration

In order for ShiftLeft to run correctly, it requires that the API key & secret be set as Environment variables. Either create a new Context, or append the following to an existing context. For all of the following examples, the context will be "Cloudguard".

The two environment variables are:

CHKP_CLOUDGUARD_ID<br>
CHKP_CLOUDGUARD_SECRET<br>

![](images/context.PNG)

From there, you can include the orb as a step within your job.

```shiftleft/shiftleft```

## Usage Examples

Example 1:<br>
An example with the default settings (code-scan):

```
version: 2.1

orbs:
  shiftleft: chkp-mibraun/shiftleft

workflows:
  set-context:
    jobs:
      - build:
          context: Cloudguard
jobs:
  build:
     machine:
       image: ubuntu-2004:202010-01
     steps:
       - checkout
       - shiftleft/shiftleft
```
Example 2:<br>
An example of image-scan with some parameters:

```
version: 2.1

orbs:
  shiftleft: chkp-mibraun/shiftleft@dev:alpha

workflows:
  set-context:
    jobs:
      - build:
          context: Cloudguard
jobs:
  build:
     machine:
       image: ubuntu-2004:202010-01
     steps:
       - checkout
       - run: |
           docker build -t test/test .
           docker save test/test  -o ./scantest.tar     
       - shiftleft/shiftleft:
           blade: image-scan
           docker-image: ./scantest.tar
```
Example 3:<br>
An example of iac-assessment:
```
description: |
  The Check Point Cloudguard ShiftLeft utility can be used to scan source code & images for vulnerabilites and malware. It can also scan IAC for compliance.
usage:
  version: 2.1

orbs:
  shiftleft: chkp-mibraun/shiftleft
workflows:
  set-context:
    jobs:
      - build:
      #Context must exist for Environment Variable to pass through to orb
          context: Cloudguard
jobs:
  build:
     machine:
       image: ubuntu-2004:202010-01
     steps:
       - checkout
       - shiftleft/shiftleft:
           blade: iac-assessment
           scan-timeout: 900
           cpath: example/directory/
           exclude-path: example/exclude/path/
           ruleset-id: 201981
```
## Code-Scan Parameters

| Parameter                                                  | Description                                                     | Default                                          |
| ---------------------------------------------------------- | --------------------------------------------------------------- | ------------------------------------------------ |
| `blade`                                                    | ShiftLeft Scan Type (code-scan, iac-assessment, image-scan)     | `code-scan`                                      |
| `directory`                                                | The directory where ShiftLeft does processing. (Default is /tmp). This is NOT for location of source code.| ` `    |
| `environment`                                              | Cloudguard Native ShiftLeft Environment                         | ` `                                              |
| `force-version`                                            | Use a specific blade version                                    | ` `                                              |
| `scan-timeout`                                             | The length of time before a scan will timeout (in seconds)      | `600`                                            |
| `code-source`                                              | Location of source code for code-scan blade                     | `.`                                              |
| `exclude-path`                                             | Path to exclude from scan                                       | ` `                                              |
| `json-output`                                              | JSON Output (True or False). If set to True, artifact will be created and uploaded. (Artifact applies only to code-scan and image-scan)| `false`|
| `no-cache`                                                 | Do not take results from previous scan (True or False)          | `false`                                          |
| `no-proxy`                                                 | Do not use system proxy settings (True or False)                | `false`                                          |
| `no-blame`                                                 | Do not use Git blame on scanned files  (True or False)          | `false`                                          |
| `severity-level`                                           | Unknown (default), Low, Medium, High, Critical                  | `Unknown`                                        |
| `severity-threshold`                                       | Severity-level threshold (default: 0)                           | `0`                                              |


## Image-Scan Parameters

| Parameter                                                  | Description                                                     | Default                                          |
| ---------------------------------------------------------- | --------------------------------------------------------------- | ------------------------------------------------ |
| `blade`                                                    | ShiftLeft Scan Type (code-scan, iac-assessment, image-scan)     | `code-scan`                                      |
| `directory`                                                | The directory where ShiftLeft does processing. (Default is /tmp). This is NOT for location of source code.| ` `    |
| `environment`                                              | Cloudguard Native ShiftLeft Environment                         | ` `                                              |
| `force-version`                                            | Use a specific blade version                                    | ` `                                              |
| `scan-timeout`                                             | The length of time before a scan will timeout (in seconds)      | `600`                                            |
| `docker-image`                                             | Location of the image tar file                                  | `.`                                              |
| `exclude-path`                                             | Path to exclude from scan                                       | ` `                                              |
| `json-output`                                              | JSON Output (True or False). If set to True, artifact will be created and uploaded. (Artifact applies only to code-scan and image-scan)| `false`|
| `no-cache`                                                 | Do not take results from previous scan (True or False)          | `false`                                          |
| `no-proxy`                                                 | Do not use system proxy settings (True or False)                | `false`                                          |
| `no-blame`                                                 | Do not use Git blame on scanned files  (True or False)          | `false`                                          |
| `severity-level`                                           | Unknown (default), Low, Medium, High, Critical                  | `Unknown`                                        |
| `severity-threshold`                                       | Severity-level threshold (default: 0)                           | `0`                                              |

## Iac-Assessment Parameters

| Parameter                                                  | Description                                                     | Default                                          |
| ---------------------------------------------------------- | --------------------------------------------------------------- | ------------------------------------------------ |
| `blade`                                                    | ShiftLeft Scan Type (code-scan, iac-assessment, image-scan)     | `code-scan`                                      |
| `directory`                                                | The directory where ShiftLeft does processing. (Default is /tmp). This is NOT for location of source code.| ` `    |
| `environment`                                              | Cloudguard Native ShiftLeft Environment                         | ` `                                              |
| `force-version`                                            | Use a specific blade version                                    | ` `                                              |
| `scan-timeout`                                             | The length of time before a scan will timeout (in seconds)      | `600`                                            |
| `path`                                                     | Path to terraform project for iac-assessment                    | `.`                                              |
| `ruleset-id`                                               | Check Point CSPM Ruleset ID. Either this or logic-id must be populated if using iac-assessment | ` `               |
| `entry`                                                    | Terraform Entry Point                                           | ` `                                              |
| `logic-id`                                                 | Logic rule string. (Cannot be combined with ruleset ID)         | ` `                                              |
| `findings`                                                 | Display the results in human readable format                    | `True`                                           |
| `exclude-path`                                             | Path to exclude from scan                                       | ` `                                              |
| `json-output`                                              | JSON Output (True or False). If set to True, artifact will be created and uploaded. (Artifact applies only to code-scan and image-scan)| `false`|
| `no-cache`                                                 | Do not take results from previous scan (True or False)          | `false`                                          |
| `no-proxy`                                                 | Do not use system proxy settings (True or False)                | `false`                                          |
| `no-blame`                                                 | Do not use Git blame on scanned files  (True or False)          | `false`                                          |
| `severity-level`                                           | Unknown (default), Low, Medium, High, Critical                  | `Unknown`                                        |
| `severity-threshold`                                       | Severity-level threshold (default: 0)                           | `0`                                              |