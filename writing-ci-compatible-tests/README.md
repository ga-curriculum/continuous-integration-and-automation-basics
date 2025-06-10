<h1>
  <span class="headline">CI & Automation Basics</span>
  <span class="subhead">Writing CI-Compatible Tests</span>
</h1>

**Learning Objective:** Apply best practices for writing CI-compatible tests and minimizing test flakiness.

## Characteristics of CI-friendly test suites

Testing in a Continuous Integration (CI) environment requires a mindset shift from running tests only on your local machine to ensuring tests run consistently for everyone. In a CI pipeline, each code change is automatically built and tested in a fresh environment, so your test suite must be robust and predictable.

What makes a test suite "CI-friendly"?

- **Deterministic results:** Tests should always pass or fail for the same reasons, no matter who runs them or where.
- **Isolation:** Each test runs independently, without relying on the side effects of other tests or outside systems.
- **Efficiency:** Fast-running tests keep pipelines smooth and developer feedback prompt.
- **Portability:** Tests should not assume any particular environment—everything they need should be in the repository or set up through configuration.
- **Minimal external dependencies:** If your tests require databases, APIs, or other systems, those dependencies should be controlled or simulated with mocks.

> 💡 Imagine a relay race where each runner relies on a clearly marked baton passed from the previous teammate. If the baton (test environment) is missing or unpredictable, the race cannot proceed smoothly. CI-friendly test suites guarantee that every runner receives the same baton, ensuring a fair and efficient race every time.

## Strategies for writing deterministic tests

_Determinism_ means that your test will always produce the same result given the same code and environment.

How can you ensure your tests remain deterministic in a CI pipeline?

**1. Control random inputs**

If your code uses random numbers or shuffles data, always set a consistent seed at the beginning of each test.

<code class="filepath">tests/test_tokens.py</code>

```python
import random

def test_generate_token():
    random.seed(42)
    token = generate_token()
    assert token == "expected-token-value"
```

_This ensures that your test will always use the same "random" value and have predictable behavior._

**2. Avoid time-based assumptions**

Tests depending on the current date or time can behave unexpectedly. Use dependency injection or mocks:

<code class="filepath">tests/test_expiry.py</code>

```python
import datetime
from unittest.mock import patch

def test_expiry():
    fixed_now = datetime.datetime(2023, 1, 1, 12, 0, 0)
    with patch("myapp.module.datetime") as mock_datetime:
        mock_datetime.datetime.now.return_value = fixed_now
        assert not is_expired()
```

> 🧠 Mocking the current time ensures that your test produces the same result, regardless of when or where it's run.

**3. Make external factors predictable**

Mock outside services and dependencies so your test environment is always under your control. For network calls, use libraries like `unittest.mock`, `pytest-mock`, or `responses`.

**4. Always clean up**

Tests should create and remove any files, database records, or other resources they use. _pytest's_ fixtures and the `tmp_path` fixture are designed to help with this.

<code class="filepath">tests/test_example.py</code>

```python
def test_temp_file(tmp_path):
    temp_file = tmp_path / "data.txt"
    temp_file.write_text("hello")
    assert temp_file.read_text() == "hello"
```

> ♻️ By creating and cleaning up resources within the test, you prevent cross-test interference.

## Handling external dependencies in CI tests

Many real-world applications require your tests to interact with databases, APIs, filesystems, or browsers. In a CI environment, these dependencies need to be tightly controlled to keep tests fast and reliable.

**1. Use in-memory databases**

Whenever possible, test against an in-memory or temporary database—like SQLite’s in-memory mode—rather than a persistent one.

<code class="filepath">app/db.py</code>

```python
SQLALCHEMY_DATABASE_URL = "sqlite:///:memory:"
```

_This keeps tests fast and reduces cleanup complexity._

**2. Mock external APIs**

Rather than making a real network request in your test, simulate the response.

<code class="filepath">tests/test_api.py</code>

```python
import requests
import responses

@responses.activate
def test_external_api_call():
    responses.add(
        responses.GET, "https://api.example.com/user/1",
        json={"id": 1, "name": "Aya"}, status=200
    )
    resp = requests.get("https://api.example.com/user/1")
    assert resp.json()["name"] == "Aya"
```

**3. Isolate browser tests**

When working with tools like Selenium, prefer temporary containers or cloud-based browser services. Avoid hardcoded paths or environment assumptions.

**4. Use pytest fixtures for setup and teardown**

Fixtures help you prepare the right environment and clean up properly after each test.

> 💡 Reliable tests in CI tell you about changes in your code—not random changes in external systems.

## Techniques for reducing test flakiness

A _flaky_ test is one that sometimes passes and sometimes fails, even if you have not changed the code. Test flakiness erodes trust in CI and slows down your entire team.

**Common causes of flakiness:**

- Waiting for an arbitrary time (such as sleeping for a few seconds).
- Tests that share state or depend on the order in which they are executed.
- Relying on network speed or other unpredictable system resources.
- Uncontrolled randomness or dependence on the actual time.

**Strategies to reduce flakiness:**

1. **Avoid timing-based waits**

   Never rely on `time.sleep()` to wait for an event. Instead, use explicit waits or condition polling.

   <code class="filepath">tests/test_login.py</code>

   ```python
   from selenium.webdriver.common.by import By
   from selenium.webdriver.support.ui import WebDriverWait
   from selenium.webdriver.support import expected_conditions as EC

   def test_login(driver):
       driver.get("http://localhost:8000/login")
       WebDriverWait(driver, 10).until(
           EC.presence_of_element_located((By.ID, "submit"))
       )
       # Proceed with login test steps
   ```

2. **Use unique test data**

   Create unique identifiers or user data for each test run to prevent accidental data overlap.

3. **Reset state before each test**

   Prepare a clean starting point. For example: wipe the test database or use fixtures for app state.

4. **Write parallel-safe tests**

   When running tests in parallel, ensure they do not try to use or modify the same files, ports, or data.

5. **Use retry logic only as a last resort**

   Retries can mask real issues. Only use retries for genuinely unreliable external systems, and document the reason.

> 🏆 Flaky tests should be fixed, not just re-run. Reliable tests build trust in both your test suite and your release pipeline.
