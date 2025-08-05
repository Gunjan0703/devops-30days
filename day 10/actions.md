# GitHub Actions
GitHub Actions is a CI/CD platform that allows you to automate software workflows directly in your GitHub repository. You can build, test, and deploy your code right from GitHub.

## Key Concepts
1. Workflow
   A configurable automated process made up of one or more jobs.
2. Event
   A specific activity that triggers a workflow (push, pull request, issue, etc.).
3. Job
   A set of steps that execute on the same runner.
4. Step
   An individual task that can run commands or actions.
5. Action
   A custom application that performs complex but frequently repeated tasks.
6. Runner
    A server that runs your workflows (GitHub-hosted or self-hosted).

## Basic workflow structure
```
name: CI Demo Workflow
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Run a script
      run: echo "Hello, GitHub Actions!"
```
### Trigger Types
- push : On code push to branches
- pull_request: On PR creation or update
- schedule: On a cron schedule (like a timer)
- workflow_dispatch: Manual trigger from GitHub UI

Example:
```
name: Python CI

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'

    - name: Install dependencies
      run: pip install -r requirements.txt

    - name: Run tests
      run: pytest
```
### Popular Actions
- actions/checkout : Clone the repo
- actions/setup-node : Setup Node.js environment
- actions/setup-python : Setup Python environment
- docker/build-push-action : Build & push Docker images
- hashicorp/setup-terraform : Setup Terraform CLI
