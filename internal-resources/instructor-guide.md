<h1>
  <span class="headline">CI & Automation Basics</span>
  <span class="subhead">Instructor Guide</span>
</h1>

## Introduction to Continuous Integration

**How to deliver this microlesson:**

- Encourage learners to connect their understanding of CI with their experience using testing tools like _pytest_ and _Selenium_.
- Use the real-world analogies provided to clarify the immediate benefits of CI.

**Knowledge checks:**

1. Which of the following is a key benefit of implementing Continuous Integration?

   - Answer: Early detection of bugs and improved team collaboration.

2. In a CI pipeline, what is typically required for code to be merged into the main branch?
   - Answer: All automated tests must pass successfully.

---

## Setting Up a CI Pipeline

**How to deliver this microlesson:**

- Begin by connecting the topic of CI to students' prior experience running tests locally with `pytest`.
- Use a relatable analogy (e.g., GitHub Actions as a reliable team member who checks your code) to explain the "why" behind automation—not just the "how."
- Walk through the sample YAML file line by line.
  - Explain the structure (name, triggers, jobs, steps).
  - Highlight indentation and syntax since YAML is new for many students.
- Emphasize that this is a **starter workflow**: minimal steps, no app servers or browsers—just automated `pytest` tests.

### Knowledge check answers

1. B) on

### During the activity

- Model the setup in real time if possible:
  - Create the `.github/workflows/` folder and `ci.yml` file.
  - Push a commit and show the Actions tab live.
- Encourage students to use a known working test (or even a dummy test) to confirm that their workflow runs end-to-end.
- If students’ tests fail, focus discussion on understanding CI logs—not just fixing the code.
  - Reinforce that seeing the process work is more important than a passing result the first time.

---

## Managing CI Environments

**How to deliver this microlesson:**

- Begin with a short recap: students have already run tests in a basic GitHub Actions setup. This lesson builds on that by showing how to improve speed (caching), flexibility (variables), and security (secrets).
- Emphasize the value of **consistency** in automated environments and how small improvements make CI pipelines more reliable over time.
- Introduce each new concept (caching, secrets, variables) clearly and explain _why_ it matters in real-world dev workflows.
  - For example, caching prevents redundant installs, and secrets simulate real API key management.
- Use a live walkthrough or diagram to break down the `robust_ci.yml` file section by section.
  - Encourage questions like “What would happen if this step was missing?”
- Remind learners they are **replacing their previous CI file**—this reflects how workflows evolve in real projects.
- Reinforce safe practices: secrets should never be logged, and public repos must be handled carefully.

### Knowledge check answers

1. B) It ensures project dependencies are isolated and consistent between runs
2. C) Store them as repository secrets and reference them in the workflow as environment variables

### During the activity

- Encourage students to observe and reflect on what happens in the **Actions tab**:
  - Did the cache hit or miss?
  - Did the secret value get passed successfully?
  - What happens when you make a small code change and re-trigger the workflow?
- Optional but helpful: prompt them to add a harmless `print()` for the `ENV` variable and see it appear in logs.
- Support students who hit common errors (e.g., secrets not set, typos in YAML indentation).
- Prompt pairs or small groups to share what surprised them or what failed and why—it turns debugging into a learning opportunity.

> 🏆 Help learners see CI environment and dependency management as foundational skills—making their testing and deployment predictable, secure, and robust as their projects and teams grow.

---

## CI Logs and Troubleshooting

**How to deliver this microlesson:**

- Set the scene by connecting troubleshooting in CI pipelines to previous hands-on work with automated tests, emphasizing the move from individual to team-scale problem-solving.
- Use the annotated log and CI failure scenarios to model the process of reading logs and tracing issues, highlighting how even subtle text clues can pinpoint a problem quickly.
- Encourage learners to engage directly with failing workflows, not just passively read about them. If possible, walk through a real or simulated CI log as a group before the hands-on activity.
- In group or chat discussions, invite diverse experiences with pipelines—encouraging learners to share both challenges and creative log analysis strategies.
- Reinforce the practical value of clean log output, actionable error messages, and consistent debugging methods, especially as teams scale in size and codebase complexity.
- Highlight best practices for clear documentation and code review habits when working with CI logs.
- Ensure inclusion and accessibility by avoiding jargon, providing code comments, representing real-world scenarios neutrally, and using visual aids where possible.

### Knowledge check answers

1. C) Expand the failed step in the workflow to read the detailed log output
2. C) They provide targeted, clickable error messages in the GitHub UI, making issues easier to spot and resolve

> 🏆 Remind learners: Building skill at reading and troubleshooting CI logs is essential—not only to fix bugs, but to make every team deployment more predictable, collaborative, and robust.

---

## Writing CI-Compatible Tests

**How to deliver this microlesson:**

- Connect the lesson to learners' prior experience with _pytest_, Selenium, and FastAPI, reinforcing how these skills transfer to automated, production-ready pipelines.
- Use narrative examples, such as tests failing when reading local files in CI, to open discussion and offer relatable starting points.
- Demonstrate codes for both flaky and deterministic tests, highlighting best practices for controlling randomness, mocking time, and using fixtures.
- Illustrate parallel test execution—run suites both sequentially and in parallel to show the importance of test independence.
- Guide the activity step-by-step, emphasizing why documentation of fixes matters for team learning.
- Encourage peer sharing and active discussion: ask learners about real cases from their own testing and CI experience.
- Ensure that code, scenarios, and examples are globally relatable—avoid region-specific references or idioms, and use diverse, concise names in examples.
- Model clear reasoning, precise communication, and the use of visual assets.

### Knowledge check answers

1. B) Each test runs independently and produces consistent results no matter where or when it's run.
2. C) Mock or simulate API responses so the tests do not depend on the actual external service.

> 🏆 The key outcome is that learners understand the value of reliability in automated testing: trustworthy, CI-compatible test suites are the backbone of robust, agile development.

---

🏗️ **Under Construction**

We are constantly working to improve our resources for instructors and students.

**Have something to contribute to this Instructor Guide?** [Let us know](https://pages.git.generalassemb.ly/modular-curriculum-all-courses/universal-resources-internal/module-feedback.html).
