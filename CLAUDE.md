# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This is a governance and monitoring repository for the Open edX **Maintenance Working Group**. It contains no application code — only GitHub Actions workflows for org-wide automation and health tracking.

## Repository Architecture

All functionality is implemented as GitHub Actions workflows in [.github/workflows/](.github/workflows/):

| Workflow | Trigger | Purpose |
|---|---|---|
| `repo-health-job.yml` | Daily 04:00 UTC / manual | Scans Open edX repos for health metrics, stores results back here |
| `dependencies-health-job.yaml` | Daily 04:00 UTC | Monitors Python dependency health across the org |
| `add-depr-ticket-to-depr-board.yml` | New issues | Routes DEPR issues to the org-wide deprecation project board |
| `add-remove-label-on-comment.yml` | Issue comments | Adds/removes labels via `label: <name>` / `remove label: <name>` comments |
| `self-assign-issue.yml` | Issue comments | Assigns issues to commenters who write "assign me" |
| `commitlint.yml` | Pull requests | Validates commit messages against org conventions |

### Shared Workflow Pattern

Most workflows delegate to reusable workflows in `openedx/.github` (the org-wide shared workflow library). When modifying a workflow's behavior, check whether the logic lives in the calling workflow here or in the upstream `openedx/.github` repo.

### Repo Health Job

The `repo-health-job.yml` workflow is the primary function of this repo. Key details:
- Requires secrets: `GITHUB_TOKEN` (with write permissions to store reports), `REPO_HEALTH_BOT_EMAIL`, `REPO_HEALTH_GOOGLE_CREDS_FILE`, `READTHEDOCS_API_KEY`
- Accepts manual dispatch inputs to override default parameters
- Writes health report data back to this repository

> **IMPORTANT:** The local `repo-health-job.yml` is only a thin caller — the actual workflow logic lives in the upstream reusable workflow. Whenever analyzing, debugging, or resolving any issue related to `repo-health-job.yml`, you MUST fetch the latest version of the upstream file before proceeding:
> ```
> https://raw.githubusercontent.com/openedx/.github/master/.github/workflows/repo-health-job.yml
> ```
> Use the `WebFetch` tool to retrieve it. Do not rely on assumptions about its contents.

### DEPR Issue Detection

The deprecation routing workflow identifies DEPR issues by any of:
- Issue has a deprecation label
- Issue title contains `[DEPR]`
- Issue body contains "Proposal Date"

## Commit Messages

Commits are linted via `commitlint.yml`. Follow the [Conventional Commits](https://www.conventionalcommits.org/) spec (enforced by the org's commitlint config in `openedx/.github`).