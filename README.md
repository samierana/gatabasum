# GitHub Actions Starter & Examples Guide

Welcome to your GitHub Actions reference and starter repository! This repository contains a curated set of clean, beginner-to-intermediate examples demonstrating core GitHub Actions workflows and best practices.

---

## 📚 Core Concepts Primer

Before diving into the examples, here is the basic mental model for GitHub Actions:

```
+-------------------------------------------------------------+
|  Event (Trigger)                                            |
|  (e.g., push, pull_request, schedule, workflow_dispatch)    |
+-------------------------------------------------------------+
                               |
                               v
+-------------------------------------------------------------+
|  Workflow (.github/workflows/*.yml)                         |
|                                                             |
|   +-----------------------+     +-----------------------+   |
|   | Job 1: Build          | --> | Job 2: Test           |   |
|   | (runs on runner VM)   |     | (needs: [Build])      |   |
|   |                       |     |                       |   |
|   | - Step 1: Checkout    |     | - Step 1: Checkout    |   |
|   | - Step 2: Setup tool  |     | - Step 2: Run tests   |   |
|   | - Step 3: Run script  |     +-----------------------+   |
|   +-----------------------+                                 |
+-------------------------------------------------------------+
```

| Concept | Description |
| :--- | :--- |
| **Workflow** | An automated process defined in a `.yml` file inside `.github/workflows/`. |
| **Event / Trigger** | A specific activity that triggers the workflow (e.g. code `push`, `pull_request`, `schedule`, or manual `workflow_dispatch`). |
| **Job** | A set of steps executed on the same runner VM. By default, jobs run in **parallel**, unless chained with `needs`. |
| **Step** | An individual task within a job. Can be a shell script (`run:`) or an action (`uses:`). |
| **Action** | A reusable application/extension created for GitHub Actions (e.g., `actions/checkout@v4`). |
| **Runner** | The server/virtual machine that runs your jobs (e.g., `ubuntu-latest`, `macos-latest`, `windows-latest`). |

---

## 🗂️ Included Example Workflows

This repo includes 4 progressive examples in [`.github/workflows/`](.github/workflows/):

### 1. [01 - Hello World & Manual Dispatch](.github/workflows/01-hello-world.yml)
- **What it teaches:**
  - Basic workflow structure and runner specification (`runs-on: ubuntu-latest`).
  - Running single and multi-line shell commands.
  - Using **GitHub context variables** (e.g. `${{ github.actor }}`, `${{ github.sha }}`).
  - Workflow-level vs step-level environment variables (`env:`).
  - Manual triggers with user inputs (`workflow_dispatch`) with dropdown options and text inputs.

### 2. [02 - CI Test Matrix](.github/workflows/02-ci-test-matrix.yml)
- **What it teaches:**
  - Checking out your source code (`actions/checkout@v4`).
  - Configuring a language runtime (`actions/setup-python@v5`).
  - Running real unit tests against [`src/calculator.py`](src/calculator.py).
  - **Matrix Strategy**: Running the same test suite in parallel across multiple versions (Python 3.10, 3.11, 3.12, 3.13) without duplicating code.

### 3. [03 - Job Dependencies & Artifacts](.github/workflows/03-job-dependencies-and-artifacts.yml)
- **What it teaches:**
  - Orchestrating sequential multi-job pipelines using `needs: [build, test]`.
  - Creating and storing build outputs with `actions/upload-artifact@v4`.
  - Passing build outputs to downstream jobs using `actions/download-artifact@v4`.
  - Simulating a real-world **Build -> Test -> Deploy** staging pipeline.

### 4. [04 - Scheduled Cron Job](.github/workflows/04-scheduled-cron.yml)
- **What it teaches:**
  - Running automated recurring tasks via POSIX cron syntax (`schedule: - cron: '0 8 * * *'`).
  - Combining `schedule` with `workflow_dispatch` so you can manually test cron jobs anytime without waiting for the timer.
  - Useful for daily health checks, dependency scans, or automated reports.

---

## 🚀 How to Run & Test These Workflows

### Step 1: Push Changes to GitHub
Push these files to your repository on GitHub:

```bash
git add .
git commit -m "Add basic GitHub Actions examples and documentation"
git push origin main
```

### Step 2: View Workflow Runs on GitHub
1. Open your repository in your browser on GitHub.
2. Click on the **Actions** tab at the top.
3. You will see all workflows listed in the left sidebar.

### Step 3: Trigger Workflows Manually
Because each example workflow includes `workflow_dispatch`, you can trigger them directly from the web interface:
1. In the **Actions** tab, click on any workflow on the left (e.g. **01 - Hello World & Manual Dispatch**).
2. Click the **Run workflow** dropdown button on the right.
3. Enter your input parameters (such as `user_name` and `environment_choice`).
4. Click the green **Run workflow** button!
5. Refresh the page after a few seconds, click on the run, and view the live streaming console logs.

---

## 📁 Repository Structure

```
.
├── .github/
│   └── workflows/
│       ├── 01-hello-world.yml                    # Intro, contexts, env vars, manual dispatch
│       ├── 02-ci-test-matrix.yml                 # CI tests across a version matrix
│       ├── 03-job-dependencies-and-artifacts.yml # Multi-job pipeline & artifacts
│       ├── 04-scheduled-cron.yml                 # Recurring cron job
│       └── codeql.yml                            # GitHub CodeQL code scanning
├── src/
│   └── calculator.py                             # Sample application code
├── tests/
│   └── test_calculator.py                        # Automated unit tests
└── README.md
```

---

## 💡 Quick Syntax Cheat Sheet

### Secrets & Variables
Store sensitive tokens in **Settings > Secrets and variables > Actions**:
```yaml
- name: Use secret
  env:
    API_KEY: ${{ secrets.MY_API_KEY }}
  run: echo "Using secured secret"
```

### Conditional Execution
Execute steps only under specific conditions:
```yaml
- name: Run only on main branch
  if: github.ref == 'refs/heads/main'
  run: echo "Running on main!"
```

### Cron Expression Cheat Sheet
```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23, UTC)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6, 0 = Sunday)
│ │ │ │ │
* * * * *
```
- `0 0 * * *` : Every day at midnight UTC
- `0 */6 * * *` : Every 6 hours
- `0 9 * * 1-5` : Monday through Friday at 09:00 UTC
