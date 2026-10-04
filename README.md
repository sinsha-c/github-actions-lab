# GitHub Actions Lab: Your First Workflow

A beginner-friendly, hands-on lab. You will create a simple workflow that runs automatically every time you push code to GitHub.

**Level:** Beginner  |  **Time:** ~20 minutes  |  **Cost:** Free

---

## 📖 Table of Contents

1. [What is GitHub Actions?](#1-what-is-github-actions)
2. [Why use GitHub Actions?](#2-why-use-github-actions)
3. [Limitations](#3-limitations)
4. [Key Terms](#4-key-terms)
5. [Prerequisites](#5-prerequisites)
6. [Lab Task](#6-lab-task)
7. [Step-by-Step Lab](#7-step-by-step-lab)
8. [Cleanup](#8-cleanup)
9. [Troubleshooting](#9-troubleshooting)
10. [What You Learned](#10-what-you-learned)

---

## 1. What is GitHub Actions?

**GitHub Actions** is a built-in automation tool inside GitHub. It lets you run tasks automatically when something happens in your repository, for example when you push code, open a pull request, or on a schedule.

Think of it as a robot assistant: *"Whenever I push code, do these steps for me."*

Typical uses:

- Build and test your code (CI)
- Deploy your app to a server or cloud (CD)
- Run security or lint checks
- Send notifications
- Run scheduled jobs (like a cron job)

Workflows are written in **YAML** files stored in your repo at `.github/workflows/`.

---

## 2. Why use GitHub Actions?

| Benefit | Explanation |
|---|---|
| **Built into GitHub** | No separate server like Jenkins to install or maintain |
| **Free tier** | Free minutes for public and private repos |
| **Event-driven** | Reacts to push, pull request, issue, release, schedule, manual click, and more |
| **Reusable actions** | Thousands of ready-made actions in the GitHub Marketplace |
| **Config as code** | Pipeline lives in your repo, versioned with your code |
| **Hosted runners** | GitHub gives you Linux, Windows, and macOS machines to run jobs |

---

## 3. Limitations

Good to know before you rely on it for everything:

- **Usage limits on the free plan.** Private repos get a limited number of free minutes and storage each month. Public repos are more generous. Check the current numbers in GitHub's billing docs.
- **Time limits.** A single job on a GitHub-hosted runner can run for at most 6 hours, and a whole workflow run for at most 35 days (including waiting).
- **Concurrency limits.** Only a limited number of jobs can run at the same time, depending on your plan. Extra jobs wait in a queue.
- **Fresh machine every time.** GitHub-hosted runners are wiped after each job. Nothing is saved unless you use artifacts or caching.
- **Cache and artifact limits.** Caches and artifacts have size and retention limits and are cleaned up automatically.
- **YAML only.** Complex logic can get messy in YAML. Debugging often means "edit, push, wait, check logs."
- **Marketplace security risk.** Third-party actions are written by others. Pin versions and review before using.
- **Less control than self-hosted tools.** Tools like Jenkins give deeper customization of the build server. (You can use *self-hosted runners* in GitHub Actions, but you then manage and secure them yourself.)
- **Tied to GitHub.** Workflows only work for GitHub repositories.

---

## 4. Key Terms

| Term | Meaning |
|---|---|
| **Workflow** | An automated process defined in a YAML file |
| **Event** | What triggers the workflow (e.g. `push`) |
| **Job** | A group of steps that run on the same runner |
| **Step** | A single task inside a job (a command or an action) |
| **Runner** | The machine that runs your job (e.g. `ubuntu-latest`) |
| **Action** | A reusable block of code (e.g. `actions/checkout`) |

```
Event (push)  →  Workflow  →  Job  →  Steps  →  Runner executes them
```

---

## 5. Prerequisites

- A free [GitHub account](https://github.com/)
- A web browser (no software installation needed)

---

## 6. Lab Task

Create a workflow that, every time you push to the repository:

1. Prints a greeting message
2. Shows the current date and time
3. Shows which user and branch triggered it
4. Lists the files in your repository

You will also trigger it **manually** with a button and see what a **failed** run looks like.

---

## 7. Step-by-Step Lab

### Step 1: Create a new repository

1. Sign in to GitHub.
2. Click **+** (top right) → **New repository**.
3. Repository name: `github-actions-lab`
4. Select **Public**.
5. Tick **Add a README file**.
6. Click **Create repository**.

<img src="screenshots/01-create-repository.png" alt="Create new repository" width="800">

---

### Step 2: Open the Actions tab

1. In your repository, click the **Actions** tab.
2. You will see suggested starter workflows. For this lab we will write our own, so click **set up a workflow yourself**.

<img src="screenshots/02-actions-tab.png" alt="Actions tab" width="800">

---

### Step 3: Write the workflow file

GitHub opens an editor with a new file at `.github/workflows/main.yml`.

1. Rename the file to `hello.yml`.
2. Delete everything in the editor and paste this:

```yaml
name: Hello GitHub Actions

on:
  push:
    branches: [ "main" ]
  workflow_dispatch:   # allows manual run from the Actions tab

jobs:
  hello-job:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository code
        uses: actions/checkout@v4

      - name: Print greeting
        run: echo "Hello! My first GitHub Actions workflow is running 🚀"

      - name: Show date and time
        run: date

      - name: Show who triggered this run
        run: |
          echo "Triggered by: ${{ github.actor }}"
          echo "Branch: ${{ github.ref_name }}"
          echo "Event: ${{ github.event_name }}"

      - name: List repository files
        run: ls -la
```

<img src="screenshots/03-workflow-editor.png" alt="Workflow YAML in editor" width="800">

**What each part means**

| Part | Meaning |
|---|---|
| `name` | Display name of the workflow |
| `on` | Triggers: push to `main`, or manual click (`workflow_dispatch`) |
| `jobs` | The list of jobs to run |
| `runs-on` | The runner machine (Ubuntu Linux) |
| `uses` | Use a ready-made action |
| `run` | Run a shell command |
| `${{ ... }}` | Built-in variables provided by GitHub |

---

### Step 4: Commit the workflow

1. Click **Commit changes...** (top right).
2. Commit message: `Add hello workflow`
3. Choose **Commit directly to the main branch**.
4. Click **Commit changes**.

This push automatically triggers the workflow.

<img src="screenshots/04-commit-workflow.png" alt="Commit changes dialog" width="800">

---

### Step 5: Watch the workflow run

1. Click the **Actions** tab again.
2. You will see **Hello GitHub Actions** running.
   - 🟡 Yellow dot = running
   - ✅ Green check = success
   - ❌ Red cross = failed
3. Wait for the green check.

<img src="screenshots/05-workflow-running.png" alt="Workflow run list" width="800">

---

### Step 6: Open the run details

1. Click the run name (it matches your commit message).
2. On the left, click the job **hello-job**.

<img src="screenshots/06-run-summary.png" alt="Run summary page" width="800">

---

### Step 7: Check the step logs

1. Expand each step by clicking its arrow.
2. Confirm you see:
   - The greeting message
   - The current date and time
   - Your GitHub username, branch `main`, and event `push`
   - The list of files in your repo (`README.md`, `.github`)

<img src="screenshots/07-step-logs.png" alt="Expanded step logs" width="800">

---

### Step 8: Trigger the workflow by pushing a change

1. Go to the **Code** tab and open `README.md`.
2. Click the ✏️ pencil icon, add a line such as `Testing GitHub Actions`.
3. Commit directly to `main`.
4. Go to **Actions**. A **new run** starts automatically. Open it and note that the event is `push`.

<img src="screenshots/08-second-run.png" alt="Second run triggered by push" width="800">

---

### Step 9: Trigger the workflow manually

Because we added `workflow_dispatch`, you can run it with a button.

1. Go to **Actions** → select **Hello GitHub Actions** in the left sidebar.
2. Click **Run workflow** → keep branch `main` → click the green **Run workflow** button.
3. Refresh. A new run appears. Open it and check the log: the event now says `workflow_dispatch`.

<img src="screenshots/09-manual-run.png" alt="Run workflow button" width="800">

---

### Step 10: See a failed run (on purpose)

Learning to read failures is important.

1. Go to **Code** → `.github/workflows/hello.yml` → ✏️ edit.
2. Add this step at the bottom (match the indentation of the other steps):

```yaml
      - name: This step will fail
        run: exit 1
```

3. Commit to `main`.
4. In **Actions**, the new run shows ❌. Open it, click the failed step, and read the error (`Process completed with exit code 1`).

<img src="screenshots/10-failed-run.png" alt="Failed workflow run" width="800">

---

### Step 11: Fix the failure

1. Edit `hello.yml` again and **remove** the failing step (or change `exit 1` to `exit 0`).
2. Commit. The new run should be ✅ green again.

<img src="screenshots/11-fixed-run.png" alt="Fixed workflow run successful" width="800">

---

## 8. Cleanup

The lab costs nothing, but to tidy up:

1. Repository → **Settings** → scroll to **Danger Zone**.
2. Click **Delete this repository** and follow the prompts.

<img src="screenshots/12-delete-repository.png" alt="Delete repository" width="800">

---

## 9. Troubleshooting

| Problem | Fix |
|---|---|
| Workflow does not start | Check the file is inside `.github/workflows/` and ends with `.yml` |
| "Invalid workflow file" error | YAML indentation is wrong. Use **spaces**, never tabs |
| No **Run workflow** button | Make sure `workflow_dispatch:` is in the `on:` section and committed to the default branch |
| Pushed to another branch, nothing ran | The trigger only listens to `main`. Change `branches` if needed |
| Actions tab shows disabled | Repository → Settings → Actions → allow actions |

---

## 10. What You Learned

- What GitHub Actions is and where it fits in CI/CD
- Its main benefits and limitations
- How events, workflows, jobs, steps, and runners relate
- How to create, trigger (push and manual), and read a workflow run
- How to spot and fix a failed run

### Next steps

- Add a build or test step for a real app (Node, Python, Java)
- Use **Secrets** (Settings → Secrets and variables → Actions) for passwords and keys
- Build and push a Docker image
- Deploy to AWS using GitHub Actions

---

## 📁 Screenshot Folder Structure

```
github-actions-lab/
├── README.md
└── screenshots/
    ├── 01-create-repository.png
    ├── 02-actions-tab.png
    ├── 03-workflow-editor.png
    ├── 04-commit-workflow.png
    ├── 05-workflow-running.png
    ├── 06-run-summary.png
    ├── 07-step-logs.png
    ├── 08-second-run.png
    ├── 09-manual-run.png
    ├── 10-failed-run.png
    ├── 11-fixed-run.png
    └── 12-delete-repository.png
```

---

**Author:** Sinsha C  |  [GitHub](https://github.com/sinsha-c)  |  [LinkedIn](https://linkedin.com/in/sinshac/)
