# CI/CD Workflow Documentation

## Overview

This project demonstrates the use of GitHub Actions for automating continuous integration (CI) and continuous deployment (CD) workflows. The application is deployed to **Render** after successful completion of the CI steps.

## CI Workflow

The CI workflow is triggered on every push to the `main` branch. It includes the following steps:

1. **Checkout code**: Retrieves the latest code from the repository.
2. **Install dependencies**: Uses `npm install` to install required Node.js packages.
3. **Run tests**: Executes test cases to ensure code integrity.
4. **Build project**: Builds the project using the specified commands in the workflow.

### CI Workflow Example

Here’s a simplified version of the workflow file (`.github/workflows/ci.yml`):

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "20"

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

      - name: Build project
        run: npm run build
```

## CD Workflow

The CD workflow automatically deploys the project to **Render** after all CI steps (build and tests) have completed successfully.

### Deployment Setup

1. **Render API key**: The deployment to Render is authenticated using an API key, which is stored in GitHub Secrets as `RENDER_API_KEY`.
2. **Deployment steps**: The application is built as a Docker image and then pushed to Render for deployment.

### Key Configuration

- **GitHub Secrets**:
  - `RENDER_API_KEY`: This secret is used to authenticate the deployment with Render.
- **Render Deployment**: The deployment is triggered via a `curl` command to Render’s API, passing the service ID of the Render web service.

### CD Workflow Example

Here’s the CD section of the workflow file:

```yaml
deploy:
  needs: build
  runs-on: ubuntu-latest

  steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Trigger Render Deployment
      env:
        RENDER_API_KEY: ${{ secrets.RENDER_API_KEY }}
      run: |
        curl -X POST "https://api.render.com/deploy/srv-<your-service-id>" \
        -H "Authorization: Bearer $RENDER_API_KEY" \
        -d ''
```

## Running the Project Locally

To run this project locally:

1. Clone the repository:

   ```bash
   git clone https://github.com/rbrownGithub/github-actions-ci-cd-assignment.git
   cd <https://github.com/rbrownGithub/github-actions-ci-cd-assignment.git>
   ```

2. Install dependencies:

   ```bash
   npm install
   ```

3. Run the application:

   ```bash
   npm start
   ```

4. Run tests:
   ```bash
   npm test
   ```

## Build Process

- **Build Command**: `npm run build` is used to build the project for production.
- **Test Command**: `npm test` is used to run the test suite.
