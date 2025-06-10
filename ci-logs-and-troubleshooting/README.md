<h1>
  <span class="headline">CI & Automation Basics</span>
  <span class="subhead">CI Logs and Troubleshooting</span>
</h1>

**Learning objective:** Interpret CI logs and test failures to troubleshoot issues effectively.

## GitHub Actions logs

When you push code or open a pull request, GitHub Actions runs your defined workflow and creates detailed logs for every step. Knowing how to read and navigate these logs is essential for swiftly finding and fixing problems.

- **Workflow summary:** Each job (such as `test`) displays whether it succeeded, failed, or was skipped. You can expand each job to look inside.
- **Step-by-step logs:** Each step (for example, “Set up Python” or “Run tests”) has its own log. Clicking into a step reveals commands that were executed, the environment, and any output.
- **Command output:** When running commands (such as _pytest_), all output appears here—successes, failures, tracebacks, and error messages.
- **Annotations:** GitHub highlights critical issues like Python tracebacks or test failures, both in the workflow summary and directly in code on pull requests.

> 💡 Think of log output as a conversation between your code and your CI system. Learning to “listen” to this conversation helps you find and resolve issues much faster.

**Example workflow log section:**

```bash
Run pytest
============================= test session starts ==============================
platform linux -- Python 3.11.0, pytest-7.3.1, pluggy-1.0.0
collected 3 items

tests/test_api.py ..F                                                [100%]

=================================== FAILURES ===================================
_________________________ test_create_user_duplicate ____________________________

    def test_create_user_duplicate():
        # ... test logic ...
>       assert response.status_code == 400
E       AssertionError: assert 422 == 400
E        +  where 422 = <Response [422]>.status_code

tests/test_api.py:20: AssertionError
```

Here you can see:

- The “Run tests” step executed _pytest_.
- The test suite ran 3 tests: 2 passed (indicated by `..`), 1 failed (`F`).
- The output details which test failed (`test_create_user_duplicate`), what assertion failed, and the expected vs. actual result.

## Common CI failure scenarios and their indicators

Not all failures are the same. Here are some typical reasons a CI pipeline may break, along with how to identify them by their log output.

### 1. Failed dependency installation

- **Indicators:**
  - Warnings or errors from commands like `pip install`. Look for phrases such as “Could not find a version,” or “No matching distribution found.”
  - Logs may cut off directly after the install step.
- **Common causes:**
  - Typographical errors in package names.
  - Nonexistent or incompatible package versions.
  - Network issues in the CI environment.

**Example:**

```
ERROR: Could not find a version that satisfies the requirement fastapi==9.9.9 (from versions: 0.1.8, ...)
ERROR: No matching distribution found for fastapi==9.9.9
```

> ⚠ Double check your `requirements.txt` and the availability of versions before troubleshooting further.

### 2. Test failures

- **Indicators:**
  - _pytest_ output contains “FAILURES” across one or more test cases.
  - Log points directly to the file and line number.
- **Common causes:**
  - Recent code changes introduced bugs.
  - Environmental or configuration issues (such as missing environment variables).

**Example:**

```bash
_________________________ test_login_valid_user _________________________
E       AssertionError: assert 404 == 200
```

> 💡 Comparing expected and actual values can help you narrow down whether the test expectation or your implementation needs to be reviewed.

### 3. Environment issues or missing secrets

- **Indicators:**
  - Errors about missing environment variables, such as “KeyError: 'MY_SECRET_KEY'” or “Environment variable XYZ not set.”
  - Fails early, likely before tests even run.
- **Common causes:**
  - Required secrets (like API keys) not set in CI.
  - Local configuration differences that aren’t mirrored in the workflow.

### 4. Service unavailability (for integration/Selenium tests)

- **Indicators:**
  - “Timeout,” “Connection refused,” or browser automation (Selenium) tests that start and then hang.
- **Common causes:**
  - A backend service needed for tests is not started.
  - Incorrect connection details or startup timing.

## Strategies for debugging failed CI runs

Solving CI failures is like piecing together a mystery: follow clues in the logs, and check each aspect methodically.

### 1. Start with the failed step

- Review the workflow summary.
- Expand the failed job and step—was it “Install dependencies,” “Run tests,” or “Set up environment”?

### 2. Read error messages closely

- Note the error type (such as `AssertionError` or `ImportError`).
- Identify the file and line number, and see which command ran just before the error.

### 3. Compare CI and local environments

Ask yourself:

- Does this test also fail on your machine?
- Are there any differences in the Python version, package versions, OS, or environment variables between your setup and the CI runner?

> 💡 Even small differences in dependencies can cause surprising failures that only appear in CI.

### 4. Use verbose output and print statements

- Many tools allow you to show more detail (`pytest -v`, `pip install -v`).
- Add print statements or logging to your application when you need more context around what’s happening.

### 5. Make one change at a time

- Adjust one variable, workflow step, or test at a time and rerun the workflow.
- Making many changes at once can hide the true cause.

> ♻️ If a step intermittently fails or reports network errors, try rerunning—sometimes, transient CI issues are outside your control.

## Using GitHub Actions annotations for clearer error reporting

GitHub Actions provides _annotations_—visual highlights (like error or warning icons) that bring attention to the most important problems:

- **Error annotations:** Summarize issues like tracebacks, exceptions, or failed assertions. These show at the top of workflow runs or beside code in pull requests.
- **Warning annotations:** Highlight less critical issues, such as deprecated features.

You can also create custom annotations for more direct or team-specific guidance.

```bash
echo "::error file=tests/test_api.py,line=20::Expected status 400 but got 422"
```

This will generate a clickable error message at the correct file and line in the GitHub UI.

> 🏆 Effective use of annotations saves time during code reviews and helps collaborators focus directly on problem areas.

<div class="activity discussion">
  <h2 class="title">Troubleshooting test failures</h2>
  <span class="minutes">5 min</span>
</div>

Troubleshooting test failures often means piecing together why something broke, not just what failed.

_How do you currently use CI logs to diagnose issues in your development workflow? What new strategies from this lesson can you use to improve your troubleshooting speed and accuracy? In what ways could clearer log messages or custom error annotations benefit your team as your project grows?_

## Knowledge checks

❓ When reviewing a failed CI run in GitHub Actions, what is typically the most effective first step?

- A) Immediately rerun the workflow without changes
- B) Update all dependencies and rerun
- C) Expand the failed step in the workflow to read the detailed log output
- D) Contact your team lead

❓ Which of the following best describes the benefit of using custom annotations in your CI workflow?

- A) They add unnecessary steps to the pipeline
- B) They slow down the workflow execution
- C) They provide targeted, clickable error messages in the GitHub UI, making issues easier to spot and resolve
- D) They are required for all CI runs
