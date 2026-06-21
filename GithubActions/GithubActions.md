# CI/CD Pipeline — Quick Reference for Testers

## What Happens After You Push

Every push automatically triggers the Selenium test suite via GitHub Actions. Tests run in an isolated cloud environment — no local execution needed.

**Sample Repository:** [selenium-automation](https://github.com/priyadharshini-qa/selenium-automation)

---

## Pipeline Flow

1. Code is pushed → pipeline triggers automatically
2. A clean environment is provisioned (JDK, dependencies, browser)
3. Tests execute in headless mode
4. Reports and failure screenshots are generated
5. Pass/fail status is posted to the commit/PR

---

## Status Indicators

| Status | Meaning |
|--------|---------|
| 🟡 Yellow | Run in progress |
| ✅ Green | All tests passed |
| ❌ Red | One or more tests failed |

Check status under the **Actions** tab or directly on the pull request.

---

## On Failure

1. Open the failed run → review logs for the failing step
2. Download artifacts (test report + failure screenshots)
3. Fix and push — this auto-triggers a new run
4. Use **Re-run jobs** if the failure looks like a one-off flake

---

## Key Points

- No local browser setup required — tests run headless on GitHub's runners
- Each run starts from a clean environment; nothing persists between runs
- Don't assume failure = flaky test — check logs first
- Merge may be blocked on failure if branch protection is enabled

## Workflow File Location

GitHub Actions workflows live under:

Example file: `.github/workflows/selenium-tests.yml`

GitHub automatically detects any `.yml`/`.yaml` file in this folder and registers it as a workflow, visible under the **Actions** tab of the repository.

## Sample Workflow File

```yaml
name: Selenium Test Automation CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
  workflow_dispatch:

jobs:
  selenium-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up JDK
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'
          cache: 'maven'

      - name: Install Google Chrome
        run: |
          sudo apt-get update
          sudo apt-get install -y google-chrome-stable

      - name: Run Selenium Tests
        run: mvn clean test -Dbrowser=chrome -Dheadless=true

      - name: Upload Test Reports
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: target/surefire-reports/

      - name: Upload Screenshots (on failure)
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: failure-screenshots
          path: screenshots/
```