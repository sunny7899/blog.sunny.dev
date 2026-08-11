---
title: Angular skills
author: Sunny
pubDatetime: 2026-07-11T04:06:31Z
slug: angular-skills
featured: true
draft: false
tags:
  - AI agents
  - Angular skills
description:
  The angular/skills repository is Angular's official collection of Agent Skills—reusable instruction packages that teach AI coding assistants how to work effectively with Angular. Rather than fine-tuning a model, these skills provide structured, task-specific knowledge that an agent loads only when needed. 
---
### What is an Agent Skill?

An Agent Skill is a self-contained package that typically includes:

* A `SKILL.md` file with metadata and detailed instructions
* Best practices and architectural guidance
* Example workflows
* Commands or scripts
* Domain-specific knowledge

When an AI agent recognizes that a task matches a skill, it loads that skill dynamically to improve the quality and consistency of its output. This approach is similar to how Anthropic's Skills system works, but Angular provides framework-specific expertise. ([GitHub][2])

---

## Current Skills

Angular currently maintains two official skills.

### 1. `angular-developer`

This is the primary development skill. It helps an AI assistant:

* Generate idiomatic Angular code
* Follow current Angular architecture
* Use **Signals**, `linkedSignal`, and `resource`
* Create components, directives, pipes, and services
* Build reactive forms
* Configure routing
* Apply dependency injection correctly
* Implement SSR and hydration
* Write accessible (ARIA-compliant) components
* Generate tests
* Use Angular CLI effectively

Instead of relying on outdated training data, the AI follows the latest Angular guidance. ([Angular][1])

---

### 2. `angular-new-app`

This skill focuses on bootstrapping a new Angular application.

It helps with:

* Creating a project using the Angular CLI
* Enabling routing
* Configuring Server-Side Rendering (SSR)
* Choosing stylesheet options
* Generating components and services
* Setting up a modern project structure

The goal is to scaffold projects using Angular's current recommendations without manually remembering every CLI option. ([Official Agent Skills Directory][3])

---

## Installing the Skills

You can install the official Angular skills using the community `skills` CLI:

```bash
npx skills add https://github.com/angular/skills
```

Or install a specific skill:

```bash
npx skills add https://github.com/angular/skills --skill angular-developer
```

The CLI also supports listing available skills, installing globally, and targeting specific AI coding agents. ([npm][4])

---

## Why This Matters

Traditionally, AI coding assistants rely on:

* Their training data
* Repository context
* User prompts

Agent Skills add a fourth layer: **framework-maintained expertise**.

This means the assistant can:

* Follow the latest Angular APIs
* Avoid deprecated patterns
* Recommend idiomatic code
* Respect Angular's architectural conventions
* Stay aligned with evolving best practices

Because the skills are maintained alongside the framework, they can evolve as Angular evolves, without waiting for a new model release. ([Angular][1])

---

## Relationship to `.agent`

These two pieces complement each other:

| `.agent`                                                | `angular/skills`                                  |
| ------------------------------------------------------- | ------------------------------------------------- |
| Repository-specific instructions                        | Reusable Angular expertise                        |
| Tells an AI how to contribute to the Angular repository | Teaches an AI how to develop Angular applications |
| Used mainly inside the Angular repo                     | Can be installed into any project                 |

Think of it this way:

* **`.agent`** answers: *"How should an AI work in this repository?"*
* **`angular/skills`** answers: *"How should an AI write modern Angular code?"*

Together, they represent a broader trend toward making software projects **AI-native** by providing structured, machine-readable guidance directly to coding agents.

