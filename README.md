# Drupal Headless Platform.sh Deployment Repository

This is the deployment repository for the Drupal headless project hosted on Platform.sh.

## Repository Structure

This repository is the umbrella repository that includes both the backend (Drupal) and frontend (Nuxt.js) as submodules:

- `backend/` - Drupal backend (submodule from novicell-php/DPB-drupal.premium11)
- `frontend/` - Nuxt.js frontend (submodule from novicell-php/DPB-drupal.premium11.headless)

## Automated Workflow

The automated workflow is designed to handle the following scenarios:

1. Feature branches are created from main or release branches
2. Feature branches are merged into release branches
3. When PRs are merged to release branches, they trigger a deploy (merge) to the staging branch
4. The main branch represents production

### Workflow Diagram

```
Feature Branch → Release Branch → Staging Branch → Main Branch
                                   ↑               ↑
                                   |               |
                                Staging        Production
```

## GitHub Workflows

The following GitHub workflows are implemented to automate the deployment process:

### Platform Repository (This Repository)

1. **update_submodules.yml** - Updates submodules when triggered by the backend or frontend repositories
2. **auto-merge-to-staging.yml** - Automatically merges changes from release branches and main to the staging branch
3. **notify-submodules.yml** - Notifies the backend and frontend repositories when changes are made to their submodules

### Backend Repository

Templates for the backend repository workflows are provided in the `templates/backend-repo/` directory:

1. **auto-merge-to-staging.yml** - Automatically merges changes from release branches and main to the staging branch
2. **trigger-umbrella.yml** - Notifies the platform repository when changes are pushed to release branches, staging, or main
3. **platform-update-handler.yml** - Handles updates from the platform repository

### Frontend Repository

Templates for the frontend repository workflows are provided in the `templates/frontend-repo/` directory:

1. **auto-merge-to-staging.yml** - Automatically merges changes from release branches and main to the staging branch
2. **trigger-umbrella.yml** - Notifies the platform repository when changes are pushed to release branches, staging, or main
3. **platform-update-handler.yml** - Handles updates from the platform repository

## Setup Instructions

### 1. Platform Repository (This Repository)

The workflows are already set up in this repository. You need to create a GitHub secret:

- `REPO_ACCESS_TOKEN` - A GitHub Personal Access Token with the `repo` scope to allow the workflows to trigger each other

### 2. Backend Repository

1. Copy the workflow templates from `templates/backend-repo/` to `.github/workflows/` in the backend repository
2. Create a GitHub secret:
   - `REPO_ACCESS_TOKEN` - A GitHub Personal Access Token with the `repo` scope

### 3. Frontend Repository

1. Copy the workflow templates from `templates/frontend-repo/` to `.github/workflows/` in the frontend repository
2. Create a GitHub secret:
   - `REPO_ACCESS_TOKEN` - A GitHub Personal Access Token with the `repo` scope

## GitHub Personal Access Token

To create a GitHub Personal Access Token:

1. Go to GitHub Settings > Developer settings > Personal access tokens
2. Click "Generate new token"
3. Give it a descriptive name
4. Select the `repo` scope
5. Click "Generate token"
6. Copy the token and add it as a secret in each repository

## Troubleshooting

If the workflows are not triggering correctly, check the following:

1. Ensure the GitHub secrets are set up correctly in each repository
2. Check the GitHub Actions logs for any errors
3. Verify that the repository names in the workflows match your actual repository names
4. Ensure the branches mentioned in the workflows exist in your repositories

## Deploy Bumper
This has been done 6 times