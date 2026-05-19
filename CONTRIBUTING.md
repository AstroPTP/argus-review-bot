# Contributing to Argus Review Bot

First off, thanks for taking the time to contribute! All types of contributions are encouraged and valued — from bug reports and feature suggestions to code and documentation improvements.

If you like the project but don't have time to contribute right now, that's completely fine. Other ways to support it:

- Star the repo
- Share it on LinkedIn or Dev.to
- Mention it to colleagues who do code review tooling
- Open an issue with a feature idea

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [I Have a Question](#i-have-a-question)
- [Prerequisites](#prerequisites)
- [I Want to Contribute](#i-want-to-contribute)
  - [Reporting Bugs](#reporting-bugs)
  - [Suggesting Enhancements](#suggesting-enhancements)
  - [Your First Code Contribution](#your-first-code-contribution)
  - [Improving the Documentation](#improving-the-documentation)
- [Styleguides](#styleguides)
  - [Commit Messages](#commit-messages)
  - [Code Style](#code-style)
- [Join the Project Team](#join-the-project-team)

---

## Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](./CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code. Please report unacceptable behaviour to `argus-bot@proton.me`.

---

## I Have a Question

Before opening an issue to ask a question:

1. Read the [README](./README.md) — particularly the quickstart and configuration sections.
2. Search existing [Issues](https://github.com/AstroPTP/argus-review-bot/issues) to see if it has already been asked.
3. Search the internet — many environment-specific issues (Docker networking, GitHub App setup, API keys) are well documented elsewhere.

If you still need clarification, [open an issue](https://github.com/AstroPTP/argus-review-bot/issues/new) and include:

- What you were trying to do
- What you expected to happen
- What actually happened (include logs or error output)
- Your environment: OS, Node.js version, Python version, Docker version

---

## Prerequisites

Make sure you have the following before contributing:

- **Node.js** 20 or higher
- **Python** 3.11 or higher
- **Docker** and **Docker Compose** (v2)
- **A GitHub account** with permissions to create a GitHub App
- **An Anthropic API key** — get one at [console.anthropic.com](https://console.anthropic.com)

---

## I Want to Contribute

> **Legal notice:** When contributing to this project, you confirm that you have authored 100% of the content, that you have the necessary rights to it, and that it may be provided under the project licence.

### Reporting Bugs

#### Before Submitting a Bug Report

A good bug report means others don't need to chase you for more information. Please do the following before submitting:

- Confirm you are on the **latest version**.
- Check that it is actually a bug and not a misconfiguration — re-read the [README](./README.md) and your `.env` file.
- Search [existing issues](https://github.com/AstroPTP/argus-review-bot/issues?q=label%3Abug) to see if the bug has been reported before.
- If it has, add a comment to the existing issue rather than opening a new one.

#### Security Vulnerabilities

**Do not report security issues in public.** Send details privately to `argus-bot@proton.me`. Include as much detail as possible so the issue can be reproduced and assessed.

#### How to Submit a Bug Report

Open an [issue](https://github.com/AstroPTP/argus-review-bot/issues/new) and include:

- A clear, descriptive title
- Steps to reproduce the issue
- What you expected to happen
- What actually happened (include full error output and logs)
- Environment details: OS, Node.js version, Python version, Docker version
- Whether the issue is consistently reproducible

Once filed:

- The maintainer will label it and attempt to reproduce it.
- If reproduction steps are missing, the issue will be tagged `needs-repro` and held until they are provided.
- Confirmed bugs are tagged `needs-fix` and added to the backlog.

---

### Suggesting Enhancements

This covers both new features and improvements to existing functionality.

#### Before Submitting an Enhancement

- Check you are on the **latest version** — the feature may already exist.
- Search [existing issues](https://github.com/AstroPTP/argus-review-bot/issues) to see if it has already been suggested. If so, add a comment rather than opening a new issue.
- Consider whether the enhancement fits the project's scope: a focused, self-hosted AI code review bot. Suggestions that serve most users are prioritised over niche use cases.

#### How to Submit a Good Enhancement Suggestion

Open an [issue](https://github.com/AstroPTP/argus-review-bot/issues/new) and include:

- A clear, descriptive title
- A step-by-step description of the proposed behaviour
- The current behaviour and what you would expect instead
- Why this would be useful to most users of Argus
- Any prior art or similar tools that handle this well

---

### Your First Code Contribution

#### Setup

1. Fork the repo on GitHub, then clone your fork:
   ```bash
   git clone https://github.com/AstroPTP/argus-review-bot.git
   cd argus-review-bot
   ```

2. Copy the environment template and fill in your credentials:
   ```bash
   cp .env.example .env
   ```

   Required variables:
   ```
   ANTHROPIC_API_KEY=your_key_here
   GITHUB_APP_ID=your_app_id
   GITHUB_APP_PRIVATE_KEY_PATH=./keys/private-key.pem
   GITHUB_WEBHOOK_SECRET=your_webhook_secret
   DATABASE_URL=postgresql://argus:argus@localhost:5432/argus
   ```

3. Start all services:
   ```bash
   docker-compose up
   ```

4. Run the test suites:
   ```bash
   # NestJS service
   npm test

   # Python review engine
   pytest
   ```

#### Finding Something to Work On

- Look for issues labelled [`good first issue`](https://github.com/AstroPTP/argus-review-bot/issues?q=label%3A%22good+first+issue%22) — these are deliberately scoped to be achievable without deep knowledge of the whole codebase.
- Comment on the issue to say you're working on it before starting, so we don't duplicate effort.

#### Submitting a Pull Request

1. Create a branch from `main`:
   ```bash
   git checkout -b feat/your-feature-name
   ```

2. Make your changes. Write or update tests where relevant.

3. Ensure all tests pass:
   ```bash
   npm test && pytest
   ```

4. Push your branch and open a pull request against `main`.

5. Fill in the PR template. Argus will automatically review its own PR — address any comments it raises before requesting a human review.

6. A maintainer will review your PR. Expect feedback within a few days.

---

### Improving the Documentation

Documentation lives in:

- `README.md` — primary reference for setup and usage
- Inline code comments — for implementation detail
- This file — for contribution process

Doc fixes and improvements don't need an issue first — open a PR directly. If you spot something unclear, wrong, or missing, fix it.

---

## Styleguides

### Commit Messages

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

| Type | When to use |
|------|-------------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation only |
| `refactor` | Code restructuring with no behaviour change |
| `test` | Adding or updating tests |
| `chore` | Tooling, dependencies, config |

Examples:

```
feat: add tree-sitter parser for TypeScript diffs
fix: handle binary files in webhook payload
docs: add GitHub App setup steps to README
chore: bump anthropic-sdk to 0.20.0
```

Rules:

- Subject line under 72 characters
- Use the imperative mood ("add" not "added" or "adds")
- No full stop at the end of the subject line
- Reference relevant issues in the body: `Closes #42`

### Code Style

**TypeScript (NestJS service)**

- Format with [Prettier](https://prettier.io/) — run `npm run format` before committing
- Lint with ESLint — run `npm run lint` and fix all warnings
- No `any` types without a comment explaining why

**Python (review engine)**

- Format with [Black](https://black.readthedocs.io/) — run `black .` before committing
- Lint with [Ruff](https://docs.astral.sh/ruff/) — run `ruff check .`
- Type hints on all function signatures

---

## Join the Project Team

Argus is currently maintained by one person. If you have made several contributions and want to take on a more active role as a maintainer, open an issue and let's talk about it.

---

## Attribution

Contribution process inspired by [contributing.md](https://contributing.md/generator). Adapted for Argus Review Bot.
