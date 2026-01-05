# VVUQ Code Quality Action

**Automatic multi-agent code review for GitHub** - Like SonarCloud, but with formal verification.

[![GitHub Action](https://img.shields.io/badge/GitHub-Action-blue)](https://github.com/marketplace/actions/vvuq-code-quality)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## Quick Start (2 Lines!)

```yaml
- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
```

**That's it!** Every PR now gets automatic code review.

---

## Features

- 🔒 **Security Analysis** - OWASP Top 10 vulnerability scanning
- 🏗️ **Architecture Review** - SOLID principles validation
- 🎭 **Deception Detection** - Mock/placeholder/TODO detection (unique!)
- 📐 **Formal Verification** - Lean4 mathematical proofs (unique!)
- 📊 **PR Comments** - Violations posted automatically
- ✅ **Quality Gates** - Block merge if score < 80%
- 🌍 **Multi-Language** - Python, JS, TS, Java, C++, Go, and more

---

## Setup

### Step 1: Get API Key

Generate your VVUQ API key:

```bash
curl -s https://vvuq.dirkenglund.org/auth/generate
```

Or if you have vvuq-mcp installed:
```bash
python -m vvuq_mcp.auth
```

### Step 2: Add Secret to Repository

1. Go to your repository **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name: `VVUQ_API_KEY`
4. Value: Paste your API key
5. Click **Add secret**

### Step 3: Create Workflow

Create `.github/workflows/code-quality.yml`:

```yaml
name: Code Quality

on:
  pull_request:
  push:
    branches: [main]

jobs:
  vvuq-review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write

    steps:
      - uses: actions/checkout@v4

      - name: VVUQ Code Review
        uses: dirkenglund/vvuq-action@v1
        with:
          api-key: ${{ secrets.VVUQ_API_KEY }}
```

**Done!** Push this file and every PR will get automatic code review.

---

## Example PR Comment

After integration, your PRs will show:

```
## 🔍 VVUQ Code Quality Report

| Metric | Value |
|--------|-------|
| **Status** | ❌ FAILED |
| **Compliance Score** | 75.0% |
| 🔴 Critical | 2 |
| 🟡 High | 3 |
| **Total** | 8 |

### 🔴 Critical Issues

1. **SQL Injection** at `src/api/users.py:42`
   Unsafe SQL query with string concatenation

2. **XSS Vulnerability** at `src/templates/render.py:18`
   Unsanitized user input in template

---
🔗 Powered by [VVUQ-MCP](https://vvuq.dirkenglund.org)
```

---

## Configuration

### Customize Rulesets

```yaml
- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
    rulesets: 'security:v1.0.0'  # Security only
```

Available rulesets:
- `security:v1.0.0` - OWASP Top 10, injection, XSS, auth
- `architecture:v1.0.0` - SOLID principles, coupling, patterns
- `deception:v1.0.0` - Mock detection, TODOs, placeholders
- `performance:v1.0.0` - N+1 queries, memory leaks, algorithms
- `quality:v1.0.0` - DRY, complexity, documentation

### Adjust Quality Gate

```yaml
- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
    quality-gate: '0.9'  # Require 90%
    fail-on-critical: 'true'
```

### Custom API Endpoint

```yaml
- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
    api-url: 'https://your-vvuq-instance.com'
```

---

## Advanced Usage

### Skip PR Comments (Just Quality Gate)

```yaml
- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
    post-comment: 'false'  # No PR comment
```

### Use Outputs in Subsequent Steps

```yaml
- name: VVUQ Review
  id: vvuq
  uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}

- name: Custom Logic
  run: |
    echo "Compliance Score: ${{ steps.vvuq.outputs.compliance-score }}"
    echo "Violations: ${{ steps.vvuq.outputs.violations-count }}"
    echo "Review ID: ${{ steps.vvuq.outputs.review-id }}"

    if [ "${{ steps.vvuq.outputs.critical-count }}" -gt "0" ]; then
      echo "Critical violations found - alerting team..."
    fi
```

### Multiple Language Projects

```yaml
# Analyze both Python and JavaScript
- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
    rulesets: 'security:v1.0.0,architecture:v1.0.0'
    # Action automatically detects file types
```

---

## Comparison to SonarCloud

| Feature | SonarCloud | VVUQ Action | Notes |
|---------|------------|-------------|-------|
| **Setup** | `uses: sonarsource/...` | `uses: dirkenglund/vvuq-action@v1` | Same simplicity |
| **PR Comments** | ✅ | ✅ | Same format |
| **Quality Gates** | ✅ | ✅ | Same blocking behavior |
| **Multi-Language** | ✅ 35 languages | ✅ LLM-based (any language) | VVUQ more flexible |
| **Security Scan** | ✅ SAST | ✅ OWASP Top 10 | Same coverage |
| **Architecture** | ✅ Code smells | ✅ SOLID principles | Same |
| **Custom Rules** | ✅ Quality profiles | ✅ Custom rulesets | Same |
| **Cost** | $10+/month | Free (self-hosted) | VVUQ cheaper |
| **Formal Verification** | ❌ | ✅ Lean4 proofs | **VVUQ unique** |
| **Deception Detection** | ❌ | ✅ Mock finding | **VVUQ unique** |
| **AI Analysis** | ⚠️ Basic | ✅ Claude Sonnet 4.5 | **VVUQ better** |

---

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `api-key` | ✅ | - | VVUQ API key |
| `api-url` | ❌ | `https://vvuq.dirkenglund.org` | API base URL |
| `rulesets` | ❌ | `security,architecture,deception` | Rulesets to apply |
| `quality-gate` | ❌ | `0.8` | Min compliance score (0-1) |
| `fail-on-critical` | ❌ | `true` | Fail on critical violations |
| `post-comment` | ❌ | `true` | Post PR comment |

## Outputs

| Output | Description |
|--------|-------------|
| `compliance-score` | Compliance score (0.0-1.0) |
| `violations-count` | Total violations found |
| `critical-count` | Critical violations count |
| `review-id` | VVUQ review ID |

---

## Examples

### Basic (Security Only)

```yaml
- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
    rulesets: 'security:v1.0.0'
```

### Strict (90% Threshold + Block on Critical)

```yaml
- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
    quality-gate: '0.9'
    fail-on-critical: 'true'
    rulesets: 'security:v1.0.0,architecture:v1.0.0,deception:v1.0.0,quality:v1.0.0'
```

### Informational (No Blocking)

```yaml
- uses: dirkenglund/vvuq-action@v1
  continue-on-error: true  # Don't fail workflow
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
    post-comment: 'true'
```

---

## Troubleshooting

### Issue: "jq: command not found"

The action installs jq automatically, but if it fails:

```yaml
- name: Install jq
  run: sudo apt-get install -y jq bc

- uses: dirkenglund/vvuq-action@v1
  with:
    api-key: ${{ secrets.VVUQ_API_KEY }}
```

### Issue: "No files changed"

This is normal if your PR doesn't change supported file types. The action skips analysis.

### Issue: "API key invalid"

Check that `VVUQ_API_KEY` is set correctly in repository secrets.

---

## Support

- **Documentation**: https://vvuq.dirkenglund.org/docs
- **API Reference**: https://vvuq.dirkenglund.org/docs#tag/review
- **Issues**: https://github.com/dirkenglund/vvuq-action/issues
- **Source Code**: https://github.com/dirkenglund/vvuq-mcp

---

## License

MIT © Dirk Englund

---

**Like SonarCloud, but with formal verification** 🚀
