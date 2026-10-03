---
title: Agent loop to handle Angular upgrades on a 3–6 month
author: Sunny
pubDatetime: 2026-09-12T04:06:31Z
slug: agent-loop-to-handle-angular-upgrades
featured: false
draft: false
tags:
  - TypeScript
  - Angular
description:
  I’ve experimented with an unattended agent loop for Angular upgrades. suppose to run every 3/6 month. It runs ng update, uses AI for up to 3 remediation cycles, runs the final tests and opens a PR.   It worked quite well for minor upgrades package updates, migration cleanup, etc. But for 2–3 version jumps, the first PR was pretty rough some package mismatches it couldn’t resolve, and a bunch of warnings pushed into the PR description instead of actually fixing the issues. For verification I checked the description it updated action items for dev to pick up if any.
ogImage: https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Gemini_Generated_Image_en8zjgen8zjgen8z.png)

---
![agent-loop-to-handle-angular-upgrades](https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Gemini_Generated_Image_en8zjgen8zjgen8z.png)

To build an unattended upgrade loop, structure it as a scheduled runner (GitHub Actions, GitLab CI, or a containerized cron worker) that pairs deterministic CLI commands with an LLM remediation cycle.

**Architecture Overview**

```
[Cron Trigger]
       │
       ▼
[Checkout & Branch]
       │
       ▼
[Step 1: ng update @angular/core @angular/cli]
       │
       ▼
[Step 2: Build / Test Verification] ──► Passes? ──► [Step 4: Open PR]
       │ (Fails)
       ▼
[Step 3: AI Remediation Loop (Max 3 turns)]
       │
       ├── Send: compiler stderr + affected files + Angular diff docs
       ├── Receive & apply patch / command
       └── Re-test: Passes? ──► [Step 4] | Fails & cycles < 3? ──► [Repeat Step 3]
       │
       ▼ (Hits 3-cycle limit without full pass)
[Step 4: Generate Summary & Open Draft PR]
       └── Auto-generate remaining triage checklist & warnings for humans

```

---

**1. Orchestration & Environment**

Use GitHub Actions with a scheduled cron:

```yaml
name: Angular Scheduled Upgrade
on:
  schedule:
    - cron: '0 2 1 */3 *' # Runs at 02:00 on the 1st of every 3rd month
  workflow_dispatch:

jobs:
  upgrade:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: 'npm'
      - name: Run Upgrade Engine
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: node scripts/upgrade-agent.js

```

---

**2. The Upgrade Engine Script (`upgrade-agent.js`)**

The script executes shell tasks, parses failures, queries an LLM API via tool calling (or unified diff outputs), and checks test assertions.

```javascript
import { execSync } from 'child_process';
import { Octokit } from '@octokit/rest';
import Anthropic from '@anthropic-ai/sdk';
import fs from 'fs';

const anthropic = new Anthropic();
const octokit = new Octokit({ auth: process.env.GITHUB_TOKEN });

function run(cmd) {
  try {
    return { success: true, output: execSync(cmd, { encoding: 'utf-8' }) };
  } catch (err) {
    return { success: false, output: (err.stdout || '') + '\n' + (err.stderr || '') };
  }
}

async function main() {
  const branch = `chore/angular-upgrade-${Date.now()}`;
  run(`git checkout -b ${branch}`);

  // Step 1: Sequential major step or standard bump
  console.log("Running baseline Angular update...");
  let ngUpdate = run("npx ng update @angular/core @angular/cli --from=18 --to=19"); // adjust version boundaries

  // Step 2: Test & Build check
  let testResult = run("npm run test -- --watch=false && npm run build");
  let remediationNotes = [];
  let cycle = 0;
  const MAX_CYCLES = 3;

  // Step 3: Up to 3 Remediation Cycles
  while (!testResult.success && cycle < MAX_CYCLES) {
    cycle++;
    console.log(`Starting AI Remediation Cycle ${cycle}...`);

    const prompt = `
The Angular update or build failed with the following error:
\`\`\`
${testResult.output.slice(-3000)}
\`\`\`

Analyze the build failure. Return a JSON object with:
- "summary": One-sentence diagnosis.
- "actions": Shell commands to run or file paths to edit.
- "fileEdits": Array of { "path": string, "replacement": string } if specific code needs refactoring.
- "unresolvedRisks": What might still be broken if third-party peer deps mismatch.
`;

    const response = await anthropic.messages.create({
      model: "claude-3-5-sonnet-latest",
      max_tokens: 2000,
      messages: [{ role: "user", content: prompt }],
    });

    const parsed = JSON.parse(response.content[0].text);
    remediationNotes.push(`### Cycle ${cycle}: ${parsed.summary}\n${parsed.unresolvedRisks || ''}`);

    // Apply code edits
    for (const edit of parsed.fileEdits || []) {
      if (fs.existsSync(edit.path)) {
        fs.writeFileSync(edit.path, edit.replacement);
      }
    }

    // Re-verify tests
    testResult = run("npm run test -- --watch=false && npm run build");
  }

  // Step 4: PR Preparation & Checklist
  run("git add . && git commit -m 'chore: automated angular upgrade and fixes'");
  run(`git push origin ${branch}`);

  const isPassing = testResult.success;
  const prBody = `
## Automated Angular UpgradeWhat specific project, system, or feature are you looking to build? 

Share the details—such as the tech stack, core requirements, or what you want it to do—and I will lay out a step-by-step implementation guide with architecture and code.

```