# qsv-sarif-test

A live demo of [qsv](https://github.com/dathere/qsv) `validate --sarif`: it validates a CSV file against a JSON Schema in GitHub Actions and shows each invalid row as a GitHub code scanning alert, at the line of the CSV where the row starts.

`validate --sarif` is new in [qsv 29.0.0](https://github.com/dathere/qsv/releases/tag/29.0.0). This repo runs the example from the qsv repo, [`resources/examples/validate-sarif`](https://github.com/dathere/qsv/tree/master/resources/examples/validate-sarif), unchanged.

## What's here

| File | What it is |
|:---|:---|
| [`.github/workflows/validate-csv.yml`](.github/workflows/validate-csv.yml) | Installs qsv, runs `qsv validate --split-ragged … --sarif qsv-validate.sarif`, uploads the log with `github/codeql-action/upload-sarif`, then fails the job if any row was invalid |
| [`resources/examples/validate-sarif/sales.csv`](resources/examples/validate-sarif/sales.csv) | Sample data with one error of each kind |
| [`resources/examples/validate-sarif/sales.schema.json`](resources/examples/validate-sarif/sales.schema.json) | The JSON Schema it is validated against |

## What it shows

- **On a push to `main`**, code scanning lists 11 alerts under the tool name `qsv`, one for each invalid field, each at the right line of `sales.csv`. For example, the error in row 3 is reported at line 5, because row 2 has a quoted field that spans two lines. The alerts are on this repo's Security → Code scanning page, which you can see if you're signed in with access.
- **On a pull request**, the workflow adds a `qsv` check, and only new errors are flagged. [PR #1](https://github.com/jqnatividad/qsv-sarif-test/pull/1) adds a row with an invalid email. Code scanning reports "1 new alert including 1 error", and a review comment on the new line shows the message and the rule. The 11 alerts already on `main` aren't repeated.
- **The workflow behaves as designed:** the validate step fails, the upload still runs, and the final step fails the job.

Code scanning on a private repository needs GitHub Advanced Security. Public repositories like this one don't.

## Use it in your own repo

Copy [`github-workflow.yml`](https://github.com/dathere/qsv/blob/master/resources/examples/validate-sarif/github-workflow.yml) to `.github/workflows/` and change `DATA_FILE` and `SCHEMA_FILE`. The job needs the `security-events: write` permission to upload.

## Learn more

- [Validation & Schema wiki page: SARIF and code scanning](https://github.com/dathere/qsv/wiki/Validation-and-Schema#sarif), with a walk-through of this example
- [`docs/Validate.md`](https://github.com/dathere/qsv/blob/master/docs/Validate.md#sarif-output-for-github-code-scanning), the `validate` command deep-dive
- [`validate` command help](https://github.com/dathere/qsv/blob/master/docs/help/validate.md)
- [qsv on GitHub](https://github.com/dathere/qsv) and the [qsv wiki](https://github.com/dathere/qsv/wiki)
