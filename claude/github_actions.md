# GitHub Actions Interview Questions and Answers

## Basic Level

### 1. What is GitHub Actions?
**Answer:** GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. It enables you to create workflows that can build and test every pull request to your repository, or deploy merged pull requests to production.

### 2. What is a workflow in GitHub Actions?
**Answer:** A workflow is a configurable automated process that you can set up in your repository to build, test, package, release, or deploy any project on GitHub. Workflows are defined in YAML files in the `.github/workflows` directory and can be triggered by various GitHub events.

### 3. What is the file extension and location for GitHub Actions workflows?
**Answer:** GitHub Actions workflows are defined in YAML files (`.yml` or `.yaml`) and must be stored in the `.github/workflows` directory of your repository.

### 4. What is a job in GitHub Actions?
**Answer:** A job is a set of steps that execute on the same runner (virtual machine). Jobs can run independently of each other or sequentially if dependencies are defined between them. Each job runs in a fresh instance of the virtual environment.

### 5. What are events in GitHub Actions?
**Answer:** Events are specific activities that trigger a workflow run. Examples include:
- push
- pull_request
- issue_comment
- schedule
- workflow_dispatch (manual trigger)

## Intermediate Level

### 6. How do you share data between steps in a job?
**Answer:** There are several ways to share data between steps:
1. Using outputs:
```yaml
steps:
  - id: step1
    run: echo "output1=hello" >> $GITHUB_OUTPUT
  - id: step2
    run: echo "${{ steps.step1.outputs.output1 }}"
```
2. Using environment variables
3. Using artifacts

### 7. What are GitHub Actions Secrets?
**Answer:** Secrets are encrypted environment variables that you create in a repository or organization. They allow you to store sensitive information (like API tokens) securely and use them in your workflows. You can access them using:
```yaml
${{ secrets.SECRET_NAME }}
```

### 8. How do you create dependencies between jobs?
**Answer:** You can create job dependencies using the `needs` keyword:
```yaml
jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - run: ./setup.sh
  
  build:
    needs: setup
    runs-on: ubuntu-latest
    steps:
      - run: ./build.sh
```

### 9. What are action inputs and outputs?
**Answer:** Actions can accept inputs (parameters) and produce outputs that can be used by other steps:
```yaml
inputs:
  who-to-greet:
    description: 'Who to greet'
    required: true
    default: 'World'

outputs:
  time:
    description: 'The greeting time'
```

## Advanced Level

### 10. How do you implement matrix builds?
**Answer:** Matrix builds allow you to test multiple versions of languages and operating systems:
```yaml
jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [14, 16, 18]
    steps:
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
```

### 11. How do you handle conditional execution in GitHub Actions?
**Answer:** You can use conditional statements with `if`:
```yaml
steps:
  - name: Step that only runs on main branch
    if: github.ref == 'refs/heads/main'
    run: echo "This is main branch"
  
  - name: Step that runs only on success
    if: ${{ success() }}
    run: echo "Previous step succeeded"
```

### 12. How do you create reusable workflows?
**Answer:** Reusable workflows can be created using the `workflow_call` trigger:
```yaml
# reusable-workflow.yml
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
    secrets:
        token:
          required: true

# calling workflow
jobs:
  call-workflow:
    uses: ./.github/workflows/reusable-workflow.yml
    with:
      environment: production
    secrets:
      token: ${{ secrets.TOKEN }}
```

### 13. How do you implement self-hosted runners?
**Answer:** Self-hosted runners can be implemented by:
1. Adding a runner to your repository/organization
2. Installing the runner application
3. Configuring the runner in your workflow:
```yaml
jobs:
  build:
    runs-on: self-hosted
    steps:
      - uses: actions/checkout@v3
      - name: Run build
        run: ./build.sh
```

### 14. How do you handle workflow concurrency?
**Answer:** Concurrency can be managed using the `concurrency` key:
```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

### 15. What are composite actions and how do you create them?
**Answer:** Composite actions allow you to combine multiple workflow steps within one action:
```yaml
# action.yml
name: 'My Composite Action'
description: 'A composite action example'
runs:
  using: "composite"
  steps:
    - run: echo "First step"
      shell: bash
    - run: echo "Second step"
      shell: bash
```

## Expert Level

### 16. How do you implement GitHub Actions workflow throttling?
**Answer:** You can implement throttling using a combination of:
1. Concurrency groups
2. Environment protection rules
3. Rate limiting with external services
```yaml
concurrency: 
  group: production-deploys
  cancel-in-progress: false

jobs:
  deploy:
    environment: production
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy
        run: ./deploy.sh
```

### 17. How do you implement custom GitHub Actions in Docker?
**Answer:** Custom Docker actions require:
1. A Dockerfile
2. An action.yml file
3. The action's code
```yaml
# action.yml
name: 'Docker Action'
description: 'Custom Docker action'
inputs:
  who-to-greet:
    description: 'Who to greet'
    required: true
runs:
  using: 'docker'
  image: 'Dockerfile'
  args:
    - ${{ inputs.who-to-greet }}
```

### 18. How do you implement workflow security scanning?
**Answer:** You can implement security scanning by:
1. Using GitHub's built-in security features
2. Implementing custom security checks
```yaml
jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run security scan
        uses: github/codeql-action/analyze@v2
      - name: Custom security checks
        run: |
          ./security-scan.sh
          ./dependency-check.sh
```