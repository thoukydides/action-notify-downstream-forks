# `action-notify-downstream-forks`

This action identifies forks that have commits ahead of the base repository, and sends a notification email if any of those commits were after the last run of this action.

The date of the last seen downstream commit is stored in the `ACTION_NOTIFY_DOWNSTREAM_FORKS` repository variable. This is used to ensure that emails are only sent when new commits have been detected.

> [!CAUTION]
> This action is provided for my own use and published in case it is useful to others. If you rely on it, fork and maintain your own copy. No support or stability guarantees are offered.

## Prerequisites

Before using this workflow, ensure:
- A fine-grained Personal Access Token (PAT) has been created with the following permissions:
  - Contents: Read-only
  - Metadata: Read-only
  - Variables: Read and write
- Repository secrets have been created for:
  - The PAT (e.g. `PAT_NOTIFY_FORKS`)
  - The SMTP email server and credentials (e.g. `EMAIL_SMTP_URL`) in either `smtp://user:password@server:port` or `smtp+starttls://user:password@server:port` format
  - The recipient email addresses (e.g. `EMAIL_TO`)

## Inputs

Various inputs are defined in the action to configure its operation:

| Name | Description | Default
| --- | --- | ---
| `sub_forks` | Whether to include forks of forks when checking for downstream commits ahead | `true`
| `email_smtp_url` | The email connection URL: `smtp://user:password@server:port` or `smtp+starttls://user:password@server:port` | *required*
| `email_to` | The recipient email addresses, comma-separated | *required*
| `email_from` | The sender's name (email address is optional) | `"GitHub Actions"`
| `email_subject` | The subject line for the notification email | `"Downstream Forks (${{ github.repository }})"`
| `email_body_file` | The file to use for the Markdown-format email body | `./email_body.txt`
| `github_token` | The GitHub token used to create an authenticated client | *required*

> [!CAUTION]
> Use repository or organisation secrets for `email_smtp_url` and `email_to`.

## Usage

Create a workflow that performs a daily check for new commits to downstream forks.

```yaml
name: Downstream Fork Notification
permissions: {}

on:
  workflow_dispatch:
  schedule:
    # Runs at 09:00 UTC daily
    # Stagger different repos to avoid hitting GitHub API rate limits
    - cron: '0 9 * * *'

jobs:
  downstream-forks:
    runs-on: ubuntu-latest

    steps:
    - name: Notify of new commits to downstream forks
      uses: thoukydides/action-notify-downstream-forks@v1
      with:
        email_smtp_url: ${{ secrets.EMAIL_SMTP_URL }}
        email_to: ${{ secrets.EMAIL_TO }}
        github_token: ${{ secrets.PAT_NOTIFY_FORKS }}
```

## ISC License (ISC)

<details>
<summary>Copyright © 2026 Alexander Thoukydides</summary>

> Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted, provided that the above copyright notice and this permission notice appear in all copies.
>
> THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
</details>