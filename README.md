# aws-ses-mailer-skill

Send emails via Amazon SES from AI coding agents. Works with **Claude Cowork**, **OpenClaw**, and any other agentic assistant that supports the [Agent Skills](https://agentskills.io) open standard.

Supports plain text, HTML, CC/BCC, reply-to, sender display name, file attachments, and dry-run mode.

## Features

- Plain text and HTML email bodies (or both)
- Multiple recipients with TO, CC, and BCC
- Reply-To header support
- Sender display name (e.g. "Acme Corp \<sender@example.com\>")
- File attachments with automatic MIME type detection
- Inline base64 attachments
- Dry-run mode for testing without sending
- SES identity verification check

## Prerequisites

- [AWS CLI](https://aws.amazon.com/cli/) installed and configured
- [Python 3](https://www.python.org/) installed
- AWS SES with a [verified sender identity](https://docs.aws.amazon.com/ses/latest/dg/verify-addresses-and-domains.html)
- IAM permissions: `ses:SendEmail`, `ses:SendRawEmail`, `ses:GetIdentityVerificationAttributes`

## Claude Cowork

### Install via Marketplace (Recommended)

1. Open Claude Cowork
2. Go to **Settings** > **Plugins** > **Add marketplace**
3. Enter: `andasv/aws-ses-mailer-skill`
4. Click **Sync**

The plugin will be installed and auto-discovered. Updates sync automatically when the repo is updated.

### Manual Installation

Copy the plugin into your personal skills directory:

```bash
mkdir -p ~/.claude/skills
cp -r aws-ses-mailer-skill/aws-ses-mailer/skills/aws-ses-mailer ~/.claude/skills/aws-ses-mailer
cp -r aws-ses-mailer-skill/aws-ses-mailer/scripts ~/.claude/skills/aws-ses-mailer/scripts
```

### Configuration

Set environment variables before starting Claude Cowork:

```bash
export SES_FROM_ADDRESS="sender@example.com"
export SES_AWS_REGION="eu-west-1"
export AWS_PROFILE="my-profile"  # optional
```

### Usage

Once installed, the skill is auto-discovered. Ask Claude to send an email:

> "Send an email to alice@example.com with subject 'Hello' and body 'Hi there!'"

> "Email the report.pdf to bob@example.com with CC to manager@example.com"

> "Do a dry run of sending a newsletter to the team"

## OpenClaw

### Installation

Copy the skill into your OpenClaw skills directory:

```bash
cp -r aws-ses-mailer-skill/openclaw ~/.openclaw/skills/aws-ses-mailer
cp -r aws-ses-mailer-skill/scripts ~/.openclaw/skills/aws-ses-mailer/scripts
```

### Configuration

Add to `~/.openclaw/openclaw.json`:

```json
{
  "skills": {
    "entries": {
      "aws-ses-mailer": {
        "env": {
          "SES_FROM_ADDRESS": "sender@example.com",
          "SES_AWS_REGION": "eu-west-1",
          "AWS_PROFILE": "my-profile"
        }
      }
    }
  }
}
```

### Usage

Once configured, ask OpenClaw to send an email:

> "Send an email to alice@example.com with subject 'Hello' and body 'Hi there!'"

> "Mail the attached report to bob@example.com"

## Other Agentic Assistants

This skill follows the [Agent Skills](https://agentskills.io) open standard, which is supported by 40+ platforms. The `generic/` directory contains a platform-agnostic SKILL.md that works with any compatible agent, including:

- **Claude Code** — Anthropic's CLI for Claude
- **Cursor** — AI-powered code editor
- **Gemini CLI** — Google's command-line AI agent
- **VS Code** (with Copilot or compatible extensions)
- **Windsurf** — Codeium's AI IDE
- **Junie** — JetBrains AI agent
- **OpenHands** — open-source AI software engineer
- **Autohand Code** — agentic coding assistant
- **Mux** — multi-agent orchestrator
- **Letta** — long-term memory AI agents

### Installation

Copy the generic skill and scripts into your agent's skill directory:

```bash
cp -r aws-ses-mailer-skill/generic /path/to/your-agent/skills/aws-ses-mailer
cp -r aws-ses-mailer-skill/scripts /path/to/your-agent/skills/aws-ses-mailer/scripts
```

Refer to your agent's documentation for the exact skill directory location.

### Configuration

Set the required environment variables:

```bash
export SES_FROM_ADDRESS="sender@example.com"
export SES_AWS_REGION="eu-west-1"
export AWS_PROFILE="my-profile"  # optional
```

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `SES_FROM_ADDRESS` | Yes | Verified SES sender email address |
| `SES_AWS_REGION` | Yes | AWS region where SES is configured (e.g. `eu-west-1`) |
| `AWS_PROFILE` | No | AWS CLI profile name (falls back to IAM role if not set) |

## Scripts

| Script | Description |
|--------|-------------|
| `scripts/send_ses_email.sh` | Send plain text and/or HTML emails via `aws ses send-email` |
| `scripts/send_ses_raw.py` | Send emails with file attachments via `aws ses send-raw-email` |
| `scripts/check_ses_identity.sh` | Check if an email identity is verified in SES |

## Examples

### Basic email
```bash
scripts/send_ses_email.sh --to "recipient@example.com" --subject "Hello" --body "Hi there!"
```

### HTML email with CC and display name
```bash
scripts/send_ses_email.sh \
  --to "alice@example.com" \
  --cc "bob@example.com" \
  --subject "Update" \
  --html "<h1>Weekly Update</h1><p>All good.</p>" \
  --from-name "Weekly Bot" \
  --reply-to "noreply@example.com"
```

### Email with file attachment
```bash
scripts/send_ses_raw.py \
  --to "recipient@example.com" \
  --subject "Invoice" \
  --body "Please find the invoice attached." \
  --attach-file "/path/to/invoice.pdf"
```

### Dry run
```bash
scripts/send_ses_email.sh --to "test@example.com" --subject "Test" --body "Testing" --dry-run
```

### Check identity verification
```bash
scripts/check_ses_identity.sh --email "sender@example.com"
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Email address is not verified" | Verify the From address in SES console or run `check_ses_identity.sh` |
| "AccessDenied" | Ensure IAM role/profile has `ses:SendEmail` and `ses:SendRawEmail` permissions |
| "MessageRejected" | SES may be in sandbox mode — [request production access](https://docs.aws.amazon.com/ses/latest/dg/request-production-access.html) |
| Attachment not received | Ensure file exists and is readable; for inline attachments use `base64 -w0` |

## License

This project is licensed under the [MIT License](LICENSE).

**Important:** Please read the [DISCLAIMER](DISCLAIMER) file for additional terms regarding liability, AWS costs, email compliance, and data handling.

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.
