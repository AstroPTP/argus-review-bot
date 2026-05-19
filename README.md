# Argus Review Bot

> An AI-powered GitHub PR code review bot that analyses pull request diffs and posts inline review comments with actionable feedback — combining static analysis with LLM reasoning.

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Node.js](https://img.shields.io/badge/Node.js-20+-green.svg)
![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED.svg)

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [1. Clone the repo](#1-clone-the-repo)
  - [2. Create a GitHub App](#2-create-a-github-app)
  - [3. Configure environment variables](#3-configure-environment-variables)
  - [4. Run with Docker Compose](#4-run-with-docker-compose)
- [Configuration](#configuration)
- [How It Works](#how-it-works)
- [Contributing](#contributing)
- [Licence](#licence)

---

## Overview

Argus is a self-hosted bot that installs as a GitHub App on your repositories. Every time a pull request is opened or updated, Argus fetches the diff, runs it through a static analysis engine and the Claude API, then posts inline review comments directly on the changed lines — just like a human reviewer would.

It is designed to be lightweight, easy to self-host, and free to run at personal scale (you supply your own Anthropic API key).

---

## Features

- **Inline PR comments** anchored to specific lines using the GitHub Review Comments API
- **Static analysis** — catches hardcoded secrets, missing error handling, SQL injection patterns, and high cyclomatic complexity without an LLM call
- **LLM-powered review** — sends diff context to Claude Sonnet and returns structured, actionable feedback
- **Result deduplication** — merges static and LLM findings, removing overlapping reports
- **Severity ranking** — comments are ordered by severity (critical → warning → suggestion)
- **False positive dismissal** — reviewers can dismiss findings; dismissals are recorded to improve future reviews
- **Self-reviewing** — Argus reviews its own pull requests on this repo
- **Fully self-hosted** — your code and API keys never leave your infrastructure

---

## Architecture

```
GitHub PR event
      │
      ▼
NestJS Webhook Receiver
  (HMAC-SHA256 validation)
      │
      ▼
CircleCI Pipeline
      │
      ├──────────────────────┐
      ▼                      ▼
Static Rules Engine    LLM Review Engine
(Python / ast /        (Python / Claude
 tree-sitter)           Sonnet 4.6)
      │                      │
      └──────────┬───────────┘
                 ▼
        Result Aggregator
        (dedup + rank)
                 │
                 ▼
      GitHub Review API
      (inline PR comments)
                 │
                 ▼
           PostgreSQL
        (runs + findings)
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Webhook receiver & orchestration | NestJS (TypeScript) |
| Review engine | Python 3.11 |
| Static analysis | Python `ast`, `tree-sitter` |
| LLM | Anthropic Claude Sonnet 4.6 |
| Database | PostgreSQL |
| Containerisation | Docker + Docker Compose |
| CI/CD | CircleCI |
| External APIs | GitHub REST API |

---

## Prerequisites

- [Node.js](https://nodejs.org/) 20+
- [Python](https://www.python.org/) 3.11+
- [Docker](https://www.docker.com/) and Docker Compose v2
- A GitHub account with permission to create a GitHub App
- An [Anthropic API key](https://console.anthropic.com)

---

## Getting Started

### 1. Clone the repo

```bash
git clone https://github.com/AstroPTP/argus-review-bot.git
cd argus-review-bot
```

### 2. Create a GitHub App

1. Go to **GitHub → Settings → Developer settings → GitHub Apps → New GitHub App**
2. Set the following:
   - **Homepage URL**: your server URL (or `http://localhost:3000` for local dev)
   - **Webhook URL**: `https://your-server.com/webhook` (use [ngrok](https://ngrok.com) for local dev)
   - **Webhook secret**: generate a random string and save it
3. Set the following **permissions**:
   - Repository: `Pull requests` → Read & Write
   - Repository: `Contents` → Read
4. Subscribe to the **Pull request** event
5. After creation, generate and download a **private key** (`.pem` file)
6. Note your **App ID** from the app settings page

### 3. Configure environment variables

```bash
cp .env.example .env
```

Edit `.env` with your credentials:

```env
# Anthropic
ANTHROPIC_API_KEY=sk-ant-...

# GitHub App
GITHUB_APP_ID=123456
GITHUB_APP_PRIVATE_KEY_PATH=./keys/private-key.pem
GITHUB_WEBHOOK_SECRET=your_webhook_secret

# Database
DATABASE_URL=postgresql://argus:argus@db:5432/argus

# Server
PORT=3000
NODE_ENV=development
```

Place your downloaded `.pem` file at `./keys/private-key.pem`.

> **Never commit your `.env` file or private key.** Both are in `.gitignore` by default.

### 4. Run with Docker Compose

```bash
docker-compose up --build
```

This starts three containers:

- `argus-webhook` — NestJS webhook receiver on port 3000
- `argus-engine` — Python review engine
- `argus-db` — PostgreSQL database

To verify everything is running:

```bash
curl http://localhost:3000/health
# → { "status": "ok" }
```

Open a pull request on a repo where your GitHub App is installed. Argus will post review comments within a few seconds.

---

## Configuration

Argus behaviour can be tuned via environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `ANTHROPIC_API_KEY` | — | Your Anthropic API key (required) |
| `GITHUB_APP_ID` | — | GitHub App ID (required) |
| `GITHUB_APP_PRIVATE_KEY_PATH` | `./keys/private-key.pem` | Path to GitHub App private key |
| `GITHUB_WEBHOOK_SECRET` | — | Webhook secret for HMAC validation (required) |
| `DATABASE_URL` | — | PostgreSQL connection string (required) |
| `PORT` | `3000` | Port for the webhook receiver |
| `MAX_DIFF_LINES` | `500` | Maximum diff lines sent to the LLM per file |
| `REVIEW_SEVERITY_THRESHOLD` | `warning` | Minimum severity to post (`suggestion`, `warning`, `critical`) |
| `LLM_MODEL` | `claude-sonnet-4-6` | Anthropic model to use for reviews |

---

## How It Works

1. A developer opens or updates a pull request on a repo with Argus installed.
2. GitHub sends a webhook event to the Argus webhook receiver.
3. The receiver validates the `X-Hub-Signature-256` header using HMAC-SHA256.
4. The diff is extracted and split by file.
5. Each file diff is passed through the **static rules engine** (checking for secrets, SQL injection, complexity, missing error handling) and the **LLM review engine** (Claude Sonnet) concurrently.
6. Results from both engines are merged and deduplicated.
7. Findings are ranked by severity and posted as inline review comments on the PR via the GitHub Review Comments API.
8. The review run and all findings are persisted to PostgreSQL.

---

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](./CONTRIBUTING.md) before submitting a pull request.

For bug reports and feature requests, [open an issue](https://github.com/AstroPTP/argus-review-bot/issues).

---

## Licence

[MIT](./LICENSE)