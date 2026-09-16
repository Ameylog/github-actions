# GitHub Actions Notes

This repository is for learning GitHub Actions, CI/CD, and workflow automation.

## What Is CI/CD?

**CI means Continuous Integration.**  
It automatically checks code whenever developers push changes. CI usually runs tests, builds the project, checks formatting, and finds bugs early.

**CD means Continuous Delivery or Continuous Deployment.**  
It automatically prepares or releases the application after CI passes. Continuous Delivery needs manual approval, while Continuous Deployment releases automatically.

## What Is GitHub Actions?

GitHub Actions is GitHub's automation tool.  
It runs workflows when events happen in a repository, such as push, pull request, issue creation, or manual trigger.

It is commonly used for testing, building, deployment, linting, publishing packages, sending notifications, and running scripts.

## Workflow File

A workflow is written in a YAML file.  
Workflow files are stored inside:

```text
.github/workflows/
```

Example:

```text
.github/workflows/hello-world.yml
```

## Basic Workflow Structure

```yaml
name: GitHub Actions Demo

on:
  push:
    branches: ["main"]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Check out repository
        uses: actions/checkout@v5

      - name: Run script
        run: echo "Hello, world!"
```

## Main Concepts

### `name`

`name` gives a readable title to the workflow.  
This name appears in the GitHub Actions tab.

```yaml
name: CI Pipeline
```

### `on`

`on` defines when the workflow should run.  
It can run on events like `push`, `pull_request`, `workflow_dispatch`, `schedule`, `issues`, and more.

```yaml
on: push
```

### `jobs`

`jobs` contains one or more tasks that GitHub Actions will execute.  
Each job runs on a runner and can have its own steps.

```yaml
jobs:
  build:
```

### Job ID

The job ID is the key under `jobs`, like `build`, `test`, or `deploy`.  
It is used internally to identify the job.

```yaml
jobs:
  build-and-test:
```

### `runs-on`

`runs-on` tells GitHub which machine should run the job.  
Common runners are `ubuntu-latest`, `windows-latest`, and `macos-latest`.

```yaml
runs-on: ubuntu-latest
```

### `steps`

`steps` are the individual tasks inside a job.  
Steps run one by one in order.

```yaml
steps:
  - name: Say hello
    run: echo "Hello"
```

### Step `name`

A step `name` gives a readable label to a step.  
It helps you understand logs clearly in the GitHub Actions UI.

```yaml
- name: Install dependencies
```

### `run`

`run` executes shell commands.  
Use it for commands like installing packages, running tests, building apps, or printing output.

```yaml
run: npm test
```

### `uses`

`uses` runs a reusable action created by GitHub or the community.  
Example: `actions/checkout` downloads your repository code into the runner.

```yaml
uses: actions/checkout@v5
```

### `with`

`with` passes input values to an action.  
Different actions support different input options.

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
```

### `env`

`env` defines environment variables.  
Variables can be used at workflow, job, or step level.

```yaml
env:
  NODE_ENV: production
```

### `needs`

`needs` makes one job wait for another job.  
It is used when jobs must run in a specific order.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
  deploy:
    needs: test
    runs-on: ubuntu-latest
```

### `if`

`if` adds a condition before running a job or step.  
It is useful for running deployment only on a specific branch.

```yaml
if: github.ref == 'refs/heads/main'
```

### `strategy` and `matrix`

`strategy.matrix` runs the same job with multiple values.  
It is useful for testing different Node versions, Python versions, or operating systems.

```yaml
strategy:
  matrix:
    node-version: [18, 20, 22]
```

### `timeout-minutes`

`timeout-minutes` stops a job or step if it runs too long.  
This prevents stuck workflows from running forever.

```yaml
timeout-minutes: 10
```

### `permissions`

`permissions` controls what access the workflow gets.  
Use the least permission required for better security.

```yaml
permissions:
  contents: read
```

### `secrets`

Secrets store sensitive values like API keys, tokens, and passwords.  
They are configured in repository settings and used with `${{ secrets.NAME }}`.

```yaml
run: echo "${{ secrets.API_KEY }}"
```

### `github` Context

The `github` context contains information about the workflow run.  
Examples include branch name, commit SHA, actor, repository, and event data.

```yaml
run: echo "${{ github.actor }}"
```

### `GITHUB_TOKEN`

`GITHUB_TOKEN` is an automatic token created for each workflow run.  
It can be used to interact with the repository based on workflow permissions.

```yaml
github-token: ${{ secrets.GITHUB_TOKEN }}
```

### `outputs`

`outputs` pass values from one step or job to another.  
They are useful when one job generates data needed by another job.

```yaml
outputs:
  build_id: ${{ steps.build.outputs.id }}
```

### `artifacts`

Artifacts are files saved from a workflow run.  
They are useful for storing build files, reports, logs, or screenshots.

```yaml
uses: actions/upload-artifact@v4
```

### `cache`

Cache stores dependencies to make workflows faster.  
It is commonly used for `node_modules`, Maven, Gradle, pip, and other package caches.

```yaml
uses: actions/cache@v4
```

### `workflow_dispatch`

`workflow_dispatch` allows you to run a workflow manually from GitHub.  
It can also accept user inputs.

```yaml
on:
  workflow_dispatch:
```

### `schedule`

`schedule` runs workflows automatically using cron syntax.  
It is useful for daily, weekly, or timed automation.

```yaml
on:
  schedule:
    - cron: "0 0 * * *"
```

## Common Trigger Methods

### `push`

Runs the workflow when code is pushed.  
Usually used for CI on the main branch or feature branches.

```yaml
on:
  push:
```

### `pull_request`

Runs when a pull request is opened, updated, or reopened.  
Usually used to test code before merging.

```yaml
on:
  pull_request:
```

### `workflow_dispatch`

Runs manually from the Actions tab.  
Best for deployments, maintenance, and custom commands.

```yaml
on:
  workflow_dispatch:
```

### `schedule`

Runs on a fixed time using cron.  
Best for repeated tasks like cleanup, reports, or nightly tests.

```yaml
on:
  schedule:
    - cron: "30 5 * * *"
```

### `issues`

Runs when issue events happen.  
Examples include issue opened, closed, edited, or labeled.

```yaml
on:
  issues:
    types: [opened, closed]
```

### `release`

Runs when release events happen.  
Useful for publishing packages or deploying production builds.

```yaml
on:
  release:
    types: [published]
```

## Common Job Methods and Keywords

### `runs-on`

Selects the runner machine.  
Example: Ubuntu, Windows, or macOS.

```yaml
runs-on: ubuntu-latest
```

### `needs`

Defines job dependency.  
The current job starts only after the needed job completes successfully.

```yaml
needs: build
```

### `if`

Runs a job only when a condition is true.  
Useful for branch-based or event-based logic.

```yaml
if: github.event_name == 'push'
```

### `env`

Adds environment variables for a job.  
These values are available to all steps in that job.

```yaml
env:
  APP_ENV: test
```

### `strategy`

Controls advanced job execution.  
Most commonly used with `matrix` to run many versions of the same job.

```yaml
strategy:
  fail-fast: false
```

### `matrix`

Creates multiple job variations.  
Example: test on Node 18, Node 20, and Node 22.

```yaml
matrix:
  node: [18, 20, 22]
```

### `timeout-minutes`

Limits the maximum runtime of a job.  
Good for avoiding wasted minutes on stuck jobs.

```yaml
timeout-minutes: 15
```

### `continue-on-error`

Allows a job or step to fail without failing the full workflow.  
Useful for experimental checks.

```yaml
continue-on-error: true
```

### `permissions`

Sets what the job can access.  
Example: read repository content or write pull request comments.

```yaml
permissions:
  contents: read
  pull-requests: write
```

## Common Step Methods and Keywords

### `name`

Displays a readable step name in logs.  
It makes workflow output easier to understand.

```yaml
- name: Run tests
```

### `run`

Runs a command in the shell.  
Use it for project commands like install, test, build, and deploy.

```yaml
- run: npm run build
```

### `uses`

Runs an existing GitHub Action.  
Use it when a common task already has a trusted action.

```yaml
- uses: actions/checkout@v5
```

### `with`

Provides inputs to an action.  
The available inputs depend on the action being used.

```yaml
with:
  node-version: 20
```

### `env`

Adds variables to a single step.  
Useful when only one command needs a value.

```yaml
env:
  TOKEN: ${{ secrets.TOKEN }}
```

### `working-directory`

Runs a command from a specific folder.  
Useful when your app is inside a subdirectory.

```yaml
working-directory: frontend
```

### `shell`

Chooses which shell runs the command.  
Examples include `bash`, `pwsh`, `python`, and `cmd`.

```yaml
shell: bash
```

### `id`

Gives a step a unique identifier.  
It is required when you want to use that step's outputs later.

```yaml
- id: build
  run: echo "id=123" >> "$GITHUB_OUTPUT"
```

## Useful Built-In Variables

### `GITHUB_WORKSPACE`

The folder where your repository is checked out.  
Commands usually run from this directory.

```yaml
run: echo "$GITHUB_WORKSPACE"
```

### `GITHUB_SHA`

The commit SHA that triggered the workflow.  
Useful for versioning or deployment tracking.

```yaml
run: echo "$GITHUB_SHA"
```

### `GITHUB_REF`

The branch or tag reference that triggered the workflow.  
Example: `refs/heads/main`.

```yaml
run: echo "$GITHUB_REF"
```

## Simple CI Example for Node.js

```yaml
name: Node CI

on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test
```

## Simple CI/CD Flow

1. Developer pushes code to GitHub.
2. GitHub Actions starts the workflow.
3. CI installs dependencies, builds the app, and runs tests.
4. If all checks pass, CD can deploy the app.
5. If any step fails, the workflow stops and shows logs.

## Quick Summary

GitHub Actions helps automate repository tasks.  
CI checks code quality automatically, and CD helps release code faster and safely.

The most important keywords are `name`, `on`, `jobs`, `runs-on`, `steps`, `run`, `uses`, `with`, `env`, `needs`, `if`, `strategy`, `matrix`, `secrets`, and `permissions`.
