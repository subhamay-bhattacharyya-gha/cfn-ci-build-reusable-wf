# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **GitHub Actions reusable workflow repository** that automates CloudFormation stack deployment and cleanup in CI environments. It serves as a callable workflow for other GitHub repositories to use via `uses: subhamay-bhattacharyya-gha/cfn-ci-build-reusable-wf/.github/workflows/ci.yaml@main`.

**Key responsibility**: Provide a standardized, reusable CI/CD workflow for deploying and tearing down CloudFormation stacks with automatic suffix generation to prevent naming collisions in CI environments.

## Architecture & Workflow

### Main CI Workflow (`.github/workflows/ci.yaml`)

The primary workflow is a 3-job orchestration:

1. **Generate Stack Suffix Job**: Creates a random 10-character hex suffix using `openssl rand -hex 5 | cut -c1-10`. This ensures unique stack names across concurrent CI runs.

2. **Deploy CloudFormation Stack Job**: Calls the external `cfn-deploy-wf` reusable workflow with:
   - Stack name appended with the generated suffix (e.g., `my-stack-a1b2c3d4e5`)
   - AWS OIDC authentication (no static credentials)
   - Template and parameters from the calling repository

3. **Delete CloudFormation Stack Job**: Calls the external `cfn-delete-wf` reusable workflow to clean up the deployed stack after successful deployment. Runs only if deployment succeeds.

### Workflow Inputs (Required)

- `aws-region`: AWS region for deployment
- `aws-account-id`: AWS account ID for OIDC role assumption
- `oidc-role-name`: IAM role name for authentication
- `cfn-templates-s3-bucket`: S3 bucket for nested CloudFormation templates
- `stack-name`: Base stack name (suffix appended automatically)
- `template-file`: Path to CloudFormation template in the calling repository
- `parameters-file`: Path to CloudFormation parameters JSON file

### Release Workflow (`.github/workflows/release.yaml`)

Handles semantic versioning and GitHub release publishing on pushes to `main` branch:
- Uses `semantic-release` with custom plugins
- Analyzes commit messages to determine version bump (major/minor/patch)
- Updates `CHANGELOG.md`
- Creates GitHub releases and tags
- **Trigger**: Commits to `main` branch automatically trigger this

## Development Commands

```bash
# Install dependencies
npm ci

# Run semantic-release (creates releases based on commit history)
npm run release

# Commit with conventional commit format (optional interactive helper)
npm run commit    # via commitizen
```

## Key Files & Their Purposes

| File | Purpose |
|------|---------|
| `.github/workflows/ci.yaml` | Main reusable workflow definition (what gets consumed by other repos) |
| `.github/workflows/release.yaml` | Automated versioning and release creation |
| `scripts/plugins/` | Custom semantic-release plugins for release automation |
| `.releaserc.json` | Semantic-release configuration (also in `release.config.js`) |
| `package.json` | Node.js dependencies and npm scripts |
| `CHANGELOG.md` | Auto-generated release notes (updated by semantic-release) |

## Important Notes

- **Workflow as Product**: This repository *is* the workflow. The YAML files in `.github/workflows/` are the deliverables, not implementation details. Changes here affect all repositories using this workflow.

- **Semantic Release**: Versioning is automatic based on conventional commits:
  - `feat:` = minor version bump
  - `fix:` = patch version bump
  - `BREAKING CHANGE:` = major version bump

- **External Dependencies**: This workflow calls two other reusable workflows:
  - `cfn-deploy-wf` (deployment logic)
  - `cfn-delete-wf` (cleanup logic)
  
  These are external concerns; implementation lives in separate repositories.

- **Permissions Model**: Uses GitHub OIDC for AWS authentication (no static secrets stored as repository secrets).

- **CI Suffix Strategy**: The 10-character hex suffix prevents stack naming collisions when multiple CI runs deploy the same stack simultaneously.

## Common Tasks

### Update the main CI workflow
Edit `.github/workflows/ci.yaml`, test with PR, merge to trigger documentation/release updates.

### Fix semantic versioning behavior
Modify `scripts/plugins/release.config.js` or `.releaserc.json` to change how versions are determined.

### Test workflow locally (if needed)
Use `act` tool to simulate GitHub Actions runner locally: `act push -b` (acts on main branch)

### Review release automation
Check recent releases on GitHub to verify semantic-release is working correctly. Trigger manually via `npm run release` locally if needed.

## Contributing Guidelines

- Follow **conventional commits** for automatic semantic versioning to work:
  - `feat(scope): description` for features
  - `fix(scope): description` for bug fixes
  - `chore:` for non-code changes
  - `docs:` for documentation
  
- PRs should be merged via merge commit (not squash) to preserve commit history for semantic-release analysis

- The workflow is the product—focus on clarity, robustness, and reusability
