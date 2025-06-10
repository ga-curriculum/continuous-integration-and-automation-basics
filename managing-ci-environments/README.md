<h1>
  <span class="headline">CI & Automation Basics</span>
  <span class="subhead">Managing CI Environments</span>
</h1>

**Learning Objective:** Configure environment setup and dependency management in a CI workflow.

## Managing CI Environments

In software development, the classic “it works on my machine” problem often stems from inconsistent environments. Your tests may pass locally but fail in CI due to differences in Python versions, system packages, or configuration.

CI tools like GitHub Actions solve this by running tests in clean, reproducible environments—typically virtual machines—that behave the same way every time.

This consistency helps teams:

- Get reliable test results across all contributors
- Avoid version mismatches and configuration bugs
- Catch issues early, before they reach users

You've already seen a basic version of this in action. Now, let’s go deeper into how CI environments are managed and optimized.

We'll explore:

1. **Using `virtual environments` in GitHub Actions**
2. **Specifying `Python version and dependencies` in the workflow**
3. **Managing secrets and `environment variables`**
4. **Best practices for `dependency caching` to speed up CI runs**

## 1. Using `virtual environments` in GitHub Actions

A _virtual environment_ in Python is a self-contained directory that has its own Python interpreter and installed packages. This lets your project control which libraries and versions it depends on, avoiding conflicts with other projects or system-wide installations.

When you use GitHub Actions, every workflow job starts with a fresh setup, like turning on a brand-new computer each time. Even so, it is still a best practice to use Python’s virtual environment tools (`venv`, `pipenv`, `poetry`, etc.) to manage dependencies. This helps:

- Make sure all required packages are present, at the versions your project needs.
- Prevent different projects or workflows from overwriting each other’s dependencies.

In practice, a typical workflow step in GitHub Actions might look like this:

`.github/workflows/ci.yml`

```yaml
steps:
  - uses: actions/checkout@v4
  - name: Set up Python
    uses: actions/setup-python@v5
    with:
      python-version: '3.11'
  - name: Install dependencies
    run: |
      python -m venv venv
      source venv/bin/activate
      pip install -r requirements.txt
  - name: Run tests
    run: |
      source venv/bin/activate
      pytest
```

Notice how we activate the **`virtual environment`** before running our test commands.

## 2. Specifying Python `version and dependencies` in the workflow

**Why specify the Python version and dependencies explicitly?**  
This is essential for _deterministic builds_—meaning every run is reproducible, no matter who or where it's run.

### Setting the Python version

The `actions/setup-python` step guarantees that the expected Python interpreter (like 3.11) is installed on the CI runner:

```yaml
- name: Set up Python
  uses: actions/setup-python@v5
  with:
    python-version: '3.11'
```

### Managing dependencies

Keep your dependencies listed in a file such as `requirements.txt`, `Pipfile`, or `pyproject.toml`. The most common approach is:

```yaml
- name: Install dependencies
  run: pip install -r requirements.txt
```

This ensures every person and every CI run installs the _exact_ libraries you list.

#### Example

If your FastAPI app needs specific versions of `fastapi`, `uvicorn`, `pytest`, and `selenium`, your `requirements.txt` might look like:

```
fastapi==0.95.2
uvicorn==0.22.0
pytest==7.3.1
selenium==4.8.3
```

Committing this file means all teammates and your CI pipeline use the same, vetted versions—reducing the risk of “surprise” bugs.

> 🏆 Best practice: Always update your dependency files when adding or upgrading packages, and review changes suggested by package managers before committing.

## 3. Managing secrets and `environment variables` in GitHub Actions

Many apps—especially ones tested with Selenium or involving APIs—need secret information like tokens or passwords. **You should never write these directly into your source code or workflow files.**

GitHub Actions provides secure tools for handling this safely:

- **Secrets** are stored inside your repository settings and are not visible in logs or files.
- **Environment variables** can be set per workflow step, passing both secrets and regular config values as needed.

**Injecting a secret into the workflow:**  
Suppose your tests require a `MY_API_KEY` value.

1. Go to your repository Settings, then **Secrets and variables > Actions > New repository secret** and add the key.
2. Reference it in the workflow file:

```yaml
- name: Run Selenium tests
  env:
    MY_API_KEY: ${{ secrets.MY_API_KEY }}
  run: pytest --apikey $MY_API_KEY
```

Now, your secret is used only for the duration of the test, and is never exposed publicly.

**Setting non-sensitive environment variables:**  
For other configurations, such as running in a “ci” environment instead of “production,” you can set regular environment variables:

```yaml
- name: Run tests
  env:
    ENV: 'ci'
  run: pytest
```

Think of secrets like a house key—only trusted people should have it, and you certainly wouldn’t tape it to the front door.

## 4. Best practices for `dependency caching` to speed up CI runs

Dependency installation often takes the longest in a CI workflow, especially with complicated or large projects. _Caching_ helps avoid downloading and reinstalling the same packages every time—speeding up builds and saving bandwidth.

### How dependency caching works

- If dependencies haven’t changed (your `requirements.txt` is the same), the cache restores previously installed packages.
- If you change dependencies, a new cache is made for the next runs.

**Example: Caching pip dependencies**

```yaml
- name: Cache pip
  uses: actions/cache@v4
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}

- name: Install dependencies
  run: pip install -r requirements.txt
```

> ♻️ Pip’s cache is keyed on both the operating system and the hashed contents of `requirements.txt`, ensuring only compatible, up-to-date packages are reused.

**Tip:** Always test your cache logic. Caching misconfiguration can mean using outdated packages or missing new installs.

<div class="activity group-exercise">
  <h2 class="title">Build and analyze a robust CI workflow</h2>
  <span class="minutes">15 min</span>
</div>

You’ve already created a basic CI pipeline that runs tests using `pytest`. Now, let’s upgrade that workflow to handle more advanced CI tasks like caching dependencies, managing secrets, and setting environment variables.

In this exercise, you’ll **replace your original `ci.yml` file** with a more robust workflow configuration.

### Goal

Create a GitHub Actions workflow that includes:

- A specified Python version
- Cached dependencies (to speed up runs)
- Environment variables
- Access to repository secrets
- Automated testing with `pytest`

### 1. Replace your CI workflow file

If you already have a `.github/workflows/ci.yml` file, **replace its contents** with the following YAML.

Or, you can delete that file and **create a new one** called `robust_ci.yml`:

```yaml
# .github/workflows/robust_ci.yml

name: Robust Python CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Cache pip
        uses: actions/cache@v4
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Set environment variables
        run: echo "ENV=ci" >> $GITHUB_ENV

      - name: Run tests with secret
        env:
          MY_SECRET_KEY: ${{ secrets.MY_SECRET_KEY }}
        run: pytest
```

### 2. Add a GitHub secret

1. Go to your repo on GitHub
2. Click **Settings > Secrets and variables > Actions**
3. Click **New repository secret**
   - **Name:** `MY_SECRET_KEY`
   - **Value:** any sample value (Ex: `abc123`)
   - Click **Add secret**

> 🔐 This simulates how real projects use API keys and other private values.

### 5. Commit and push your changes

```bash
git add .
git commit -m "Add robust CI workflow with caching and secrets"
git push
```

### 6. Trigger the workflow

Make a small change (even just a comment) and push again.

### 7. Watch your workflow run

- Go to the **Actions** tab on GitHub
- Click on **Robust Python CI**
- Confirm the steps run successfully:
  - Python installed
  - Dependencies installed
  - Cache used (check for “cache hit” or “miss”)
  - Environment variable set
  - Secret accessed
  - Tests executed

> 💡 Congrats! You're now ready to add even more automation—like linting, coverage reports, or deployment steps—in future projects.

<div class="activity discussion">
  <h2 class="title">Reflect</h2>
  <span class="minutes">2 min</span>
</div>

How does careful environment and dependency management influence the reliability of your automated tests and overall deployment? Are there scenarios you can imagine where failing to pin versions or improperly handling secrets could lead to serious issues in production?

Let’s discuss how establishing strong practices for managing environments and dependencies leads to long-term stability, efficiency, and security in any development project.

## Knowledge checks

❓ What is the primary benefit of using a virtual environment in your CI workflow for Python projects?

- A) It speeds up your internet connection
- B) It ensures project dependencies are isolated and consistent between runs
- C) It allows you to use multiple operating systems simultaneously
- D) It disables caching

❓ In a GitHub Actions workflow, how should you handle sensitive information such as API keys or database passwords?

- A) Hard-code them into your repository files for easy access
- B) Store them in plain text in your workflow YAML
- C) Store them as repository secrets and reference them in the workflow as environment variables
- D) Share them via email with your team before each run
