# GitHub  Workflow: ClouudFormation CI Build

<!-- Row 1: Status - Most Important -->
[![Release](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf/actions/workflows/release.yaml/badge.svg)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)&nbsp;[![GitHub Action](https://img.shields.io/badge/GitHub-Action-blue?logo=github)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)&nbsp;[![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf/issues)&nbsp;[![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf/commits)

<!-- Row 2: Code Quality -->
[![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)&nbsp;[![Commits](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf/commits)

<!-- Row 3: Tech Stack -->
[![Terraform](https://img.shields.io/badge/Terraform-IaC-blueviolet?logo=terraform&logoColor=white)](https://www.terraform.io/)&nbsp;[![Built with Claude Code](https://img.shields.io/badge/Built_with-Claude_Code-D97757?logo=anthropic&logoColor=white)](https://claude.ai/)

<!-- Row 4: Repository Info -->
[![Files](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)&nbsp;[![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)&nbsp;[![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf/releases)

<!-- Row 5: Custom Metrics -->
[![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/23c1ca0fe4355707a9aacc88d297d4fe/raw/cfn-ci-build-reusable-wf.json)](https://gist.github.com/bsubhamay/23c1ca0fe4355707a9aacc88d297d4fe)

A reusable GitHub Actions workflow for deploying and cleaning up CloudFormation stacks with random suffix generation for CI environments.

## Workflow: CloudFormation CI Build

### Workflow Description

This reusable workflow automates the deployment and cleanup of AWS CloudFormation stacks. It generates a unique CI suffix to avoid stack name collisions, deploys the CloudFormation stack with specified templates and parameters, and automatically cleans up the stack after successful deployment.

**Workflow Features:**

- Automatic generation of unique 10-character random suffix for stack naming
- Deploys CloudFormation stacks using the [cfn-deploy-wf](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf) workflow
- Cleans up stacks using the [cfn-delete-wf](https://github.com/subhamay-bhattacharyya-gha/cfn-delete-wf) workflow on successful deployment
- AWS OIDC authentication support for secure credential-less deployments
- Support for nested CloudFormation templates via S3 bucket

---

## Inputs

| Name                      | Description                                              | Required | Default |
|---------------------------|----------------------------------------------------------|----------|---------|
| `aws-region`              | AWS region for stack deployment                          | Yes      | —       |
| `aws-account-id`          | AWS account ID for OIDC role assumption                  | Yes      | —       |
| `oidc-role-name`          | AWS IAM role name for OIDC authentication                | Yes      | —       |
| `cfn-templates-s3-bucket` | S3 bucket name for CloudFormation nested templates       | Yes      | —       |
| `stack-name`              | Base CloudFormation stack name (suffix will be appended) | Yes      | —       |
| `template-file`           | Path to CloudFormation template file in repository       | Yes      | —       |
| `parameters-file`         | Path to CloudFormation parameters JSON file              | Yes      | —       |

---

## Example Usage

```yaml
name: Deploy CloudFormation Stack

on:
  push:
    branches:
      - feature/**
    paths:
      - 'templates/**'

jobs:
  deploy:
    uses: subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf/.github/workflows/ci.yaml@main
    with:
      aws-region: us-east-1
      aws-account-id: '123456789012'
      oidc-role-name: github-actions-role
      cfn-templates-s3-bucket: my-cfn-templates-bucket
      stack-name: my-vpc-stack
      template-file: templates/vpc-networking.yaml
      parameters-file: parameters/vpc-params.json
```

## License

MIT
