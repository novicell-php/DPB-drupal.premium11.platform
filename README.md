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

### Creating a Personal Access Token (REPO_ACCESS_TOKEN)

To create a GitHub Personal Access Token for cross-repository workflows:

1. **Go to GitHub Account Settings**:
   - Click your profile picture in the top-right corner
   - Select "Settings"
   - Scroll down and select "Developer settings" on the left sidebar
   - Click "Personal access tokens" → "Tokens (classic)"

2. **Generate New Token**:
   - Click "Generate new token" → "Generate new token (classic)"
   - Note: Classic tokens are recommended for repository dispatch events

3. **Configure Token Settings**:
   - **Name**: Enter a descriptive name (e.g., "Cross-Repository Automation")
   - **Expiration**: Select an appropriate expiration period (30-90 days recommended)

4. **Select Scopes** (Critical step):
   - **Required**: ✅ `repo` (Full control of private repositories)
     - This includes all repo sub-permissions
   - **Optional but recommended**: ✅ `workflow` (Update GitHub Action workflows)

5. **Generate and Save**:
   - Click "Generate token" at the bottom
   - **IMPORTANT**: Copy the token immediately and store it securely
   - This token will only be shown once

### Adding the Token to Repository Secrets

You must add this token to **ALL THREE repositories**:

1. **Go to Repository Settings**:
   - Navigate to the repository on GitHub
   - Click the "Settings" tab
   - Select "Secrets and variables" from the left sidebar
   - Click "Actions"

2. **Add New Repository Secret**:
   - Click "New repository secret"
   - **Name**: Enter exactly `REPO_ACCESS_TOKEN` (case-sensitive)
   - **Value**: Paste the token you copied
   - Click "Add secret"

3. **Repeat for All Repositories**:
   - novicell-php/DPB-drupal.premium11 (backend)
   - novicell-php/DPB-drupal.premium11.headless (frontend)
   - novicell-php/DPB-drupal.premium11.platform (platform)

### Token Renewal Process

Since GitHub tokens expire, follow these steps when renewing:

1. **Create a new token** following the steps above before the current one expires
2. **Update the token** in all three repositories' secrets
3. **Verify workflows** are still functioning after the update
4. Set a calendar reminder 1 week before the token expiration date

## Troubleshooting

If the workflows are not triggering correctly, check the following:

### Token and Secret Issues

1. **Verify Token Permissions**:
   - Ensure the token has the full `repo` scope
   - Check that the token hasn't expired
   - Confirm the user creating the token has appropriate access to all repositories

2. **Verify Secret Configuration**:
   - Confirm the secret is named exactly `REPO_ACCESS_TOKEN` in all repositories
   - Check that the token is added to all three repositories
   - Verify there are no typos in the workflow files referencing the secret

### Workflow Configuration Issues

1. **Repository Path Configuration**:
   - Check all workflow files for correct repository paths:
     ```yaml
     repository: novicell-php/DPB-drupal.premium11.platform  # Must be exact
     ```

2. **Event Type Configuration**:
   - Ensure event types match exactly between sender and receiver workflows:
     ```yaml
     event-type: update-submodule  # Must match exactly
     ```

3. **Implementation in Submodules**:
   - Verify workflow files are actually copied from templates to each repository's `.github/workflows/` directory
   - Compare contents to make sure they match the templates

4. **Branch Names**:
   - Ensure the branches mentioned in the workflows exist in your repositories
   - Verify you're pushing to the correct branches that trigger the workflows

### Advanced Troubleshooting

1. **Enable Debug Logging**:
   - Add `ACTIONS_RUNNER_DEBUG` and `ACTIONS_STEP_DEBUG` secrets with value `true`

2. **Test Repository Dispatch Manually**:
   - Use GitHub CLI to test the repository dispatch event directly:
     ```bash
     gh api repos/novicell-php/DPB-drupal.premium11.platform/dispatches \
       -X POST \
       -H "Accept: application/vnd.github+json" \
       -H "Authorization: token YOUR_TOKEN" \
       -f event_type="update-submodule" \
       -f client_payload='{"submodule":"frontend","environment":"staging","commit":"YOUR_COMMIT_HASH"}'
     ```

3. **Check Workflow Run History and Webhook Deliveries**:
   - Review workflow runs in the "Actions" tab
   - Inspect webhook deliveries in repository Settings → Webhooks

## Deploy Bumper
This has been done 6 times

This has been done 7 times
