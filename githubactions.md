# 1. What is GitHub Actions?

GitHub Actions is GitHub’s **CI/CD** (Continuous Integration/Continuous Deployment) tool. It allows you to automate tasks such as:

- **Running tests** on your code
- **Building** your application
- **Deploying** to servers or cloud platforms
- **Automating workflows** on pull requests, issues, or releases

It’s **event-driven**.

## Key Concepts

### Workflow

A workflow is an automated process defined in a **YAML** file.

- **Storage:** Stored in your repo under: `.github/workflows/<workflow_name>.yml`

### Job

A workflow can have one or more jobs.

- Each job runs on a **runner** (virtual machine provided by GitHub).
- Jobs can run sequentially or in parallel.

### Step

Jobs are made of steps, which are individual tasks executed in order.

- Steps can run commands or use **pre-built actions** from GitHub Marketplace.

### Runner

A runner is a machine that executes your workflow jobs.

- **Types:**
  - **GitHub-hosted:** Linux, Windows, macOS (no setup needed).
  - **Self-hosted:** Your own machine (offers more control and custom configurations).

```yaml
# GitHub Actions Example: Node.js CI/CD

name: Node.js CI/CD Workflow #Name of the workflow

on: #Triggers
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest #runner:github hosted linux VM

    steps:
      # Step 1: Checkout code from repository
      - name: Checkout repository
        uses: actions/checkout@v3 #prebuilt

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "18"

      - name: Install dependencies
        run: npm install #run shell command

      - name: Run tests
        run: npm test

  deploy: #deployment job
    runs-on: ubuntu-latest
    needs: build # needs:This job depends on the build job

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Deploy to server
        if: success() # Only run if previous job succeeded
        run: |
          echo "Deploying to ${{ github.event.inputs.environment }}"
          scp -r . user@server:/var/www/myapp
          ssh user@server 'pm2 restart myapp'

      - name: Notify deployment
        run: echo "Deployment completed successfully!"

env: #environment variables
  VAR_NAME: value
```

---
