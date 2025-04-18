# 🔍 snyk-scan-action

`snyk-scan-action` is a **custom reusable GitHub Action** for running **Snyk scans** on your code or Infrastructure as Code (IaC) files. It identifies vulnerabilities, generates a JSON report, and can optionally comment on pull requests with the scan results.

## ✨ Features

- Scan source code or IaC using Snyk.
- Detect vulnerabilities and optionally report them directly in pull requests.
- Customize severity thresholds.
- Optionally install Python requirements before scanning for more complete dependency trees.
- Supports `pip` and `poetry` Python dependency managers.
- Supports additional flags like `--skip-unresolved` and `--exclude-licenses`.
- Supports `.snyk` policy files to ignore known/approved issues, including custom paths.
- Outputs scan results to a JSON file for further use in workflows or audit logs.

## 📦 Inputs

| Input                        | Description                                                                                                                                           | Required | Default           |
|-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| `snyk-token`                | Your [Snyk API token](https://docs.snyk.io/getting-started/how-to-obtain-and-authenticate-with-your-snyk-api-token).                                 | ✅       | N/A               |
| `snyk-org`                  | Your [Snyk Organization ID](https://docs.snyk.io/snyk-cli/scan-and-maintain-projects-using-the-cli/using-snyk-code-from-the-cli/set-the-snyk-organization-for-the-cli-tests). | ✅       | N/A               |
| `snyk-endpoint`             | (Optional) Custom Snyk API endpoint. Useful for self-hosted Snyk.                                                                                      | ❌       | `""`              |
| `scan-mode`                 | Choose the scan mode: `iac` for Infrastructure as Code or `code` for dependency scanning.                                                              | ✅       | N/A               |
| `severity-threshold`        | Minimum severity level to report: `low`, `medium`, `high`, or `critical`.                                                                             | ❌       | `low`             |
| `code-file`                 | Path to the dependency file (e.g., `requirements.txt`, `poetry.lock`). Only used in `code` scan mode.                                                  | ❌       | `requirements.txt`|
| `install-requirements`      | Whether to install Python requirements before scanning (code scans only). Improves detection of transitive dependencies.                              | ❌       | `false`           |
| `python-package-manager`    | Python package manager to use when installing dependencies. Either `pip` or `poetry`.                                                                  | ❌       | `pip`             |
| `skip-unresolved`           | Whether to use `--skip-unresolved` in code scan. Must be `true` or `false`.                                                                            | ❌       | `false`           |
| `exclude-licenses`          | Whether to use `--exclude-licenses` in code scan. Must be `true` or `false`.                                                                           | ❌       | `false`           |
| `json-output-file`          | Path to save the output of the scan in JSON format.                                                                                                     | ❌       | `snyk.json`       |
| `update-pr-with-scan-results` | Whether to add a PR comment with scan results.                                                                                                         | ❌       | `false`           |
| `use-policy-file`           | Whether to use a `.snyk` policy file to ignore known/approved issues.                                                                                   | ❌       | `true`            |
| `policy-file-path`          | Path to a custom `.snyk` policy file (relative to the repo root).                                                                                       | ❌       | `.`               |

---

## 🚀 Example Usage (pip-based project)

```yaml
permissions:
  issues: write
  pull-requests: write

jobs:
  snyk-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Snyk scan
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
```

---

## 🚀 Example Usage (poetry-based project)

```yaml
permissions:
  issues: write
  pull-requests: write

jobs:
  snyk-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Snyk scan (Poetry)
        uses: furmidgeuk/snyk-scan-action@v1.0.0
        with:
          snyk-token: ${{ secrets.SNYK_TOKEN }}
          snyk-org: ${{ secrets.SNYK_ORG }}
          scan-mode: code
          code-file: "poetry.lock"
          install-requirements: true
          python-package-manager: poetry
```

---

## 🚀 Example Usage (IaC scanning)

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
```

> 📘 **Note:** Snyk IaC scans do not require dependency installation. The action will recursively scan Terraform, Kubernetes, CloudFormation, and other supported configuration files.

---

## 💬 Pull Request Comment Example

If vulnerabilities are found, the action will post a Markdown-formatted summary as a comment on the PR:

![Snyk scan result comment](https://imgur.com/YTOHD9l.png)

---

## 🛠️ Development To-Do

- [x] Support `.snyk` policy files to exclude known/approved vulnerabilities.
- [x] Add support for installing requirements before code scan.
- [x] Support for Poetry-managed projects.
- [x] Add example for IaC scanning.
- [ ] Improve formatting of PR scan result comments (e.g., sorting, badges, clickable links).
- [ ] Add support for additional scan modes like `container` or `open source`.

---

## 📚 References

- [Snyk CLI Documentation](https://docs.snyk.io/snyk-cli)
- [Snyk API Token](https://docs.snyk.io/getting-started/how-to-obtain-and-authenticate-with-your-snyk-api-token)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

---

## 🤝 Contributing

Feel free to fork, open issues, or submit pull requests to improve this action. All contributions are welcome!

---

## 📄 License

MIT © 2025 [furmidgeuk](https://github.com/furmidgeuk)