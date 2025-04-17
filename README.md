# GitHub Actions Go Pipeline Template

This repository contains reusable GitHub Actions workflows for Go applications. These workflows standardize the CI/CD process across all Go applications, making maintenance and updates easier.

## Features

- Code quality checks (golangci-lint)
- Security scanning (gosec)
- Unit and integration testing
- Semantic versioning and release management
- Docker image building and publishing
- Branch protection enforcement

## Usage

To use these shared workflows in your Go application, follow these steps:

### 1. Create workflow files in your repository

Create the following files in your repository's `.github/workflows` directory:

#### analysis.yml

```yaml
name: "Go Combined Analysis"

on:
  pull_request:
    branches:
      - develop
      - main
    types:
      - opened
      - edited
      - synchronize
      - reopened

permissions:
  id-token: write
  contents: read
  pull-requests: write
  actions: read
  security-events: write

jobs:
  go-analysis:
    uses: LerianStudio/github-actions-go-pipeline-template/.github/workflows/analysis.yml@main
    secrets:
      lerian_studio_push_bot_app_id: ${{ secrets.LERIAN_STUDIO_MIDAZ_PUSH_BOT_APP_ID }}
      lerian_studio_push_bot_private_key: ${{ secrets.LERIAN_STUDIO_MIDAZ_PUSH_BOT_PRIVATE_KEY }}
      lerian_ci_cd_user_gpg_key: ${{ secrets.LERIAN_CI_CD_USER_GPG_KEY }}
      lerian_ci_cd_user_gpg_key_password: ${{ secrets.LERIAN_CI_CD_USER_GPG_KEY_PASSWORD }}
      lerian_ci_cd_user_name: ${{ secrets.LERIAN_CI_CD_USER_NAME }}
      lerian_ci_cd_user_email: ${{ secrets.LERIAN_CI_CD_USER_EMAIL }}
```

#### release.yml

```yaml
name: "Release"

on:
  push:
    branches:
      - develop
      - main
      - hotfix/v*
    paths-ignore:
      - '.gitignore'
      - '**/*.env'
      - '*.env'
      - '**/*.md'
      - '*.md'
      - '**/*.txt'
      - '*.txt'
    tags-ignore:
      - '**'

permissions:
  id-token: write
  contents: write
  pull-requests: write

jobs:
  publish-release:
    uses: LerianStudio/github-actions-go-pipeline-template/.github/workflows/release.yml@main
    secrets:
      lerian_studio_push_bot_app_id: ${{ secrets.LERIAN_STUDIO_MIDAZ_PUSH_BOT_APP_ID }}
      lerian_studio_push_bot_private_key: ${{ secrets.LERIAN_STUDIO_MIDAZ_PUSH_BOT_PRIVATE_KEY }}
      lerian_ci_cd_user_gpg_key: ${{ secrets.LERIAN_CI_CD_USER_GPG_KEY }}
      lerian_ci_cd_user_gpg_key_password: ${{ secrets.LERIAN_CI_CD_USER_GPG_KEY_PASSWORD }}
      lerian_ci_cd_user_name: ${{ secrets.LERIAN_CI_CD_USER_NAME }}
      lerian_ci_cd_user_email: ${{ secrets.LERIAN_CI_CD_USER_EMAIL }}
```

#### build.yml

```yaml
name: "Build Pipeline"

on:
  push:
    tags:
      - '**'

permissions:
  id-token: write
  contents: read
  pull-requests: write

jobs:
  build-and-publish:
    uses: LerianStudio/github-actions-go-pipeline-template/.github/workflows/build.yml@main
    with:
      app_name: 'your-app-name'
    secrets:
      docker_username: ${{ secrets.DOCKER_USERNAME }}
      docker_password: ${{ secrets.DOCKER_PASSWORD }}
      docker_access_token: ${{ secrets.DOCKER_ACCESS_TOKEN }}
```

#### check-branch.yml

```yaml
name: "Enforce Branch PR's from Develop"

on:
  pull_request:
    branches:
      - main
    types:
      - opened
      - edited
      - synchronize
      - reopened

jobs:
  check-branch:
    uses: LerianStudio/github-actions-go-pipeline-template/.github/workflows/check-branch.yml@main
    secrets:
      lerian_studio_push_bot_app_id: ${{ secrets.LERIAN_STUDIO_MIDAZ_PUSH_BOT_APP_ID }}
      lerian_studio_push_bot_private_key: ${{ secrets.LERIAN_STUDIO_MIDAZ_PUSH_BOT_PRIVATE_KEY }}
      lerian_ci_cd_user_gpg_key: ${{ secrets.LERIAN_CI_CD_USER_GPG_KEY }}
      lerian_ci_cd_user_gpg_key_password: ${{ secrets.LERIAN_CI_CD_USER_GPG_KEY_PASSWORD }}
      lerian_ci_cd_user_name: ${{ secrets.LERIAN_CI_CD_USER_NAME }}
      lerian_ci_cd_user_email: ${{ secrets.LERIAN_CI_CD_USER_EMAIL }}
```

### 2. Configure your repository

Ensure your repository has the necessary secrets configured:

- `LERIAN_STUDIO_MIDAZ_PUSH_BOT_APP_ID`
- `LERIAN_STUDIO_MIDAZ_PUSH_BOT_PRIVATE_KEY`
- `LERIAN_CI_CD_USER_GPG_KEY`
- `LERIAN_CI_CD_USER_GPG_KEY_PASSWORD`
- `LERIAN_CI_CD_USER_NAME`
- `LERIAN_CI_CD_USER_EMAIL`
- `DOCKER_USERNAME`
- `DOCKER_PASSWORD`
- `DOCKER_ACCESS_TOKEN`

## Updating the Shared Workflow

To update the shared workflow, follow these steps:

1. Create a new branch from `main`:
   ```bash
   git checkout main
   git pull
   git checkout -b feature/your-feature-name
   ```

2. Make your changes to the workflow files.

3. Commit and push your changes:
   ```bash
   git add .
   git commit -m "feat: your descriptive commit message"
   git push origin feature/your-feature-name
   ```

4. Create a Pull Request to the `develop` branch.

5. After testing and approval, the changes will be merged into `develop`.

6. Once the changes in `develop` have been tested and verified, create a Pull Request from `develop` to `main`.

7. After approval, the changes will be merged into `main` and available for use in all repositories.

## Git Flow

This repository follows a standard Git flow:

1. `main` branch contains the stable, production-ready code.
2. `develop` branch is used for integration and testing.
3. Feature branches (`feature/*`) are created from `main` for new features.
4. Bug fix branches (`fix/*`) are created from `main` for bug fixes.
5. Hotfix branches (`hotfix/v*`) are created from `main` for urgent fixes.
6. All changes must be made through Pull Requests.
7. Pull Requests to `main` can only come from `develop` or `hotfix/v*` branches.

## Support

For questions or issues, please contact the Lerian Studio DevOps team.
