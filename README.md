# 🔍 snyk-scan-action

`snyk-scan-action` is a **custom reusable GitHub Action** for running **Snyk scans** on your code, Infrastructure as Code (IaC) files, or performing Static Application Security Testing (SAST). It identifies vulnerabilities, generates a JSON report, and can optionally comment on pull requests with the scan results.

## ✨ Features

- **Multi-mode scanning**: Scan source code dependencies, Infrastructure as Code, or perform Static Application Security Testing (SAST).
- Detect vulnerabilities and optionally report them directly in pull requests.
- Customize severity thresholds and filtering options.
- Optionally install Python requirements before scanning for more complete dependency trees.
- Supports `pip` and `poetry` Python dependency managers.
- Supports additional flags like `--skip-unresolved` and `--exclude-licenses`.
- Supports `.snyk` policy files to ignore known/approved issues, including custom paths.
- Outputs scan results to a JSON file for further use in workflows or audit logs.
- Optionally generate a standalone PDF report (available for code and SAST scanning).
- Adds clickable reference links in PR comments (CVE, Snyk blog, PoCs).
- **SAST capabilities**: Analyze source code for security vulnerabilities with file-level filtering.

## 📦 Inputs

| Input                        | Description                                                                                                                                           | Required | Default           |
|-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| `snyk-token`                | Your [Snyk API token](https://docs.snyk.io/getting-started/how-to-obtain-and-authenticate-with-your-snyk-api-token).                                 | ✅       | N/A               |
| `snyk-org`                  | Your [Snyk Organization ID](https://docs.snyk.io/snyk-cli/scan-and-maintain-projects-using-the-cli/using-snyk-code-from-the-cli/set-the-snyk-organization-for-the-cli-tests). | ✅       | N/A               |
| `snyk-endpoint`             | (Optional) Custom Snyk API endpoint. Useful for self-hosted Snyk.                                                                                      | ❌       | `""`              |
| `scan-mode`                 | Choose the scan mode: `iac` for Infrastructure as Code, `code` for dependency scanning, or `sast` for Static Application Security Testing.             | ✅       | N/A               |
| `severity-threshold`        | Minimum severity level to report: `low`, `medium`, `high`, or `critical`.                                                                             | ❌       | `low`             |
| `code-file`                 | Path to the dependency file (e.g., `requirements.txt`, `poetry.lock`). Only used in `code` scan mode.                                                  | ❌       | `requirements.txt`|
| `sast-path`                 | Path to scan for SAST analysis. Only used in `sast` scan mode.                                                                                         | ❌       | `.`               |
| `sast-include`              | Comma-separated list of file extensions or paths to include in SAST scan (e.g., `*.py,*.js,src/`).                                                     | ❌       | `""`              |
| `sast-exclude`              | Comma-separated list of file extensions or paths to exclude from SAST scan (e.g., `test/,*.test.js`).                                                  | ❌       | `""`              |
| `install-requirements`      | Whether to install Python requirements before scanning (code scans only). Improves detection of transitive dependencies.                              | ❌       | `false`           |
| `python-package-manager`    | Python package manager to use when installing dependencies. Either `pip` or `poetry`.                                                                  | ❌       | `pip`             |
| `skip-unresolved`           | Whether to use `--skip-unresolved` in code scan. Must be `true` or `false`.                                                                            | ❌       | `false`           |
| `exclude-licenses`          | Whether to use `--exclude-licenses` in code scan. Must be `true` or `false`.                                                                           | ❌       | `false`           |
| `json-output-file`          | Path to save the output of the scan in JSON format.                                                                                                     | ❌       | `snyk.json`       |
| `update-pr-with-scan-results` | Whether to add a PR comment with scan results.                                                                                                         | ❌       | `false`           |
| `use-policy-file`           | Whether to use a `.snyk` policy file to ignore known/approved issues.                                                                                   | ❌       | `true`            |
| `policy-file-path`          | Path to a custom `.snyk` policy file (relative to the repo root).                                                                                       | ❌       | `.`               |
| `generate-pdf-report`      | Whether to generate a standalone PDF report using `snyk-to-html` for code and SAST scans.                                                             | ❌       | `false`           |
| `continue-on-error`         | Whether to allow the workflow to continue if the Snyk scan fails. Set to `true` for non-blocking scans.                                               | ❌       | `false`           |

---

## 🚀 Example Usage (Code Dependencies - pip-based project)

```yaml
permissions:
  issues: write
  pull-requests: write

jobs:
  snyk-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Snyk Code Dependency Scan
        uses: furmidgeuk/snyk-scan-action@v1.0.0
        with:
          snyk-token: ${{ secrets.SNYK_TOKEN }}
          snyk-org: ${{ secrets.SNYK_ORG }}
          scan-mode: code
          code-file: "requirements.txt"
          severity-threshold: "high"
          update-pr-with-scan-results: true
          use-policy-file: true
          policy-file-path: "./security/.snyk"
          install-requirements: true
          python-package-manager: pip
          generate-pdf-report: true
          continue-on-error: true
```

---

## 🚀 Example Usage (Code Dependencies - poetry-based project)

```yaml
permissions:
  issues: write
  pull-requests: write

jobs:
  snyk-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Snyk Code Dependency Scan (Poetry)
        uses: furmidgeuk/snyk-scan-action@v1.0.0
        with:
          snyk-token: ${{ secrets.SNYK_TOKEN }}
          snyk-org: ${{ secrets.SNYK_ORG }}
          scan-mode: code
          code-file: "poetry.lock"
          install-requirements: true
          python-package-manager: poetry
          generate-pdf-report: true
          continue-on-error: true
```

---

## 🛡️ Example Usage (SAST - Static Application Security Testing)

```yaml
permissions:
  issues: write
  pull-requests: write

jobs:
  snyk-sast-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Snyk SAST Scan
        uses: furmidgeuk/snyk-scan-action@v1.0.0
        with:
          snyk-token: ${{ secrets.SNYK_TOKEN }}
          snyk-org: ${{ secrets.SNYK_ORG }}
          scan-mode: sast
          sast-path: "./src"
          sast-include: "*.py,*.js,*.ts,*.jsx,*.tsx"
          sast-exclude: "test/,*.test.js,__pycache__/"
          severity-threshold: medium
          update-pr-with-scan-results: true
          generate-pdf-report: true
          continue-on-error: true
```

---

## 🏗️ Example Usage (IaC scanning)

```yaml
permissions:
  issues: write
  pull-requests: write

jobs:
  snyk-iac-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Run Snyk IaC Scan
        uses: furmidgeuk/snyk-scan-action@v1.0.0
        with:
          snyk-token: ${{ secrets.SNYK_TOKEN }}
          snyk-org: ${{ secrets.SNYK_ORG }}
          scan-mode: iac
          severity-threshold: high
          update-pr-with-scan-results: true
          continue-on-error: true
```

> 📘 **Note:** Snyk IaC scans do not require dependency installation. The action will recursively scan Terraform, Kubernetes, CloudFormation, and other supported configuration files.

---

## 🔄 Example Usage (Multi-Job Workflow)

You can run multiple scan types in the same workflow:

```yaml
permissions:
  issues: write
  pull-requests: write

jobs:
  security-scanning:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        scan-type: [code, sast, iac]
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Run Snyk Scan - ${{ matrix.scan-type }}
        uses: furmidgeuk/snyk-scan-action@v1.0.0
        with:
          snyk-token: ${{ secrets.SNYK_TOKEN }}
          snyk-org: ${{ secrets.SNYK_ORG }}
          scan-mode: ${{ matrix.scan-type }}
          sast-path: ${{ matrix.scan-type == 'sast' && './src' || '' }}
          sast-include: ${{ matrix.scan-type == 'sast' && '*.py,*.js,*.ts' || '' }}
          code-file: ${{ matrix.scan-type == 'code' && 'requirements.txt' || '' }}
          severity-threshold: medium
          update-pr-with-scan-results: true
          continue-on-error: true
```

---

## 💬 Pull Request Comment Examples

When vulnerabilities are detected, this action posts a Markdown-formatted summary directly to the pull request with helpful links.

### Code Dependency Scan Example

| Package   | Version | Severity | Title                                | More Info |
|-----------|---------|----------|--------------------------------------|-----------|
| jinja2    | 3.1.2   | medium   | Cross-site Scripting (XSS)           | [CVE-2024-22195](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-22195) |
| requests  | 2.31.0  | medium   | Always-Incorrect Control Flow        | [CVE-2024-35195](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-35195) |
| setuptools| 68.1.2  | high     | Code Injection                       | [PoC](https://huntr.com/bounties/d6362117-ad57-4e83-951f-b8141c6e7ca5) |

### SAST Scan Example

| File | Line | Rule | Severity | Issue | Description | Help |
|------|------|------|----------|-------|-------------|------|
| app.py | 42 | `python/sql-injection` | high | SQL Injection | User input is used in SQL query without sanitization | [Info](https://docs.snyk.io/...) |
| utils.js | 18 | `javascript/xss` | medium | Cross-site Scripting | Unescaped user input in HTML context | [Info](https://docs.snyk.io/...) |
| auth.py | 95 | `python/hardcoded-secrets` | critical | Hardcoded Secret | API key found in source code | [Info](https://docs.snyk.io/...) |

### IaC Scan Example

| File         | Resource                        | Severity | Issue                              | Impact                              | Resolution                             | Link |
|--------------|----------------------------------|----------|-------------------------------------|--------------------------------------|----------------------------------------|------|
| main.tf      | azurerm_storage_account.default | high     | Geo-replication disabled           | Data might be lost during outages    | Set `sku.name` to `GRS` or `RAGRS`     | [Docs](https://docs.microsoft.com/...) |
| k8s-deploy.yml | Deployment/web-app             | medium   | Container runs as root             | Potential privilege escalation       | Add `securityContext.runAsNonRoot: true` | [Docs](https://kubernetes.io/...) |

---

## 🔧 Scan Mode Comparison

| Feature | Code Dependencies | SAST | IaC |
|---------|------------------|------|-----|
| **Purpose** | Find vulnerable dependencies | Analyze source code for security issues | Check infrastructure configuration |
| **Input** | Package files (requirements.txt, etc.) | Source code files | Infrastructure files (*.tf, *.yml, etc.) |
| **Output** | Dependency vulnerabilities | Code security flaws | Configuration misconfigurations |
| **File Filtering** | ❌ | ✅ (include/exclude patterns) | ❌ |
| **PDF Reports** | ✅ | ✅ | ❌ |
| **Policy Files** | ✅ | ✅ | ✅ |

---

## 🛠️ Development To-Do

- [x] Support `.snyk` policy files to exclude known/approved vulnerabilities.
- [x] Add support for installing requirements before code scan.
- [x] Support for Poetry-managed projects.
- [x] Add example for IaC scanning.
- [x] **Add SAST (Static Application Security Testing) support**.
- [x] **Add file filtering for SAST scans**.
- [x] **Support PDF reports for SAST scans**.
- [ ] Improve formatting of PR scan result comments (e.g., sorting, badges).
- [ ] Add support for additional scan modes like `container` or `open source`.
- [ ] Add support for custom Snyk Code configuration files.

---

## 📚 References

- [Snyk CLI Documentation](https://docs.snyk.io/snyk-cli)
- [Snyk Code (SAST) Documentation](https://docs.snyk.io/products/snyk-code)
- [Snyk API Token](https://docs.snyk.io/getting-started/how-to-obtain-and-authenticate-with-your-snyk-api-token)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

---

## 🤝 Contributing

Feel free to fork, open issues, or submit pull requests to improve this action. All contributions are welcome!

### Contributing Guidelines

- Ensure new features include appropriate input validation
- Add examples for new functionality in the README
- Test with different scan modes before submitting PRs
- Follow existing code style and formatting

---

## 📄 License

MIT © 2025 [furmidgeuk](https://github.com/furmidgeuk)
