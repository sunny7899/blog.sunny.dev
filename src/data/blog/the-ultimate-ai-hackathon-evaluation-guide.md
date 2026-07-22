---
title: The Ultimate AI Hackathon Evaluation Guide Judging Antigravity & AI Studio Projects
author: Sunny
pubDatetime: 2026-07-21T04:06:31Z
slug: the-ultimate-ai-hackathon-evaluation-guide
featured: false
draft: false
tags:
  - hackathon
description:
  When evaluating an AI hackathon—especially one centered around next-generation tools like Google Antigravity or Google AI Studio—traditional rubrics often fall short. You aren't just looking at standard CRUD applications; you are evaluating autonomous agents, multimodal reasoning, and dynamic, vibe-coded environments.
---

Whether you are organizing a community hackathon or sitting on the judging panel, here is a comprehensive guide and checklist to ensure you evaluate AI projects fairly, rigorously, and effectively.

## What is the Challenge About?

Before diving into the code, align the judging panel on the core objective of the hackathon. For a modern AI hackathon, the challenge typically revolves around:

* **Building Autonomous Solutions:** Moving beyond simple chat interfaces to create agents that plan, execute, and adapt.
* **Leveraging Native Multimodality:** Using models like Gemini to process text, audio, video, and code seamlessly.
* **Dynamic Environments:** Utilizing platforms like Google Antigravity to build fluid, non-static, and highly interactive applications that react to complex inputs.

## Overall Guidelines & Instructions

When setting up the evaluation process, communicate these non-negotiable instructions to both judges and participants:

* **Live Demos Only:** Pitch decks and videos are great for storytelling, but judges must be able to interact with the live application or see a live walkthrough.
* **No Faked Outputs:** The AI must generate responses in real-time. Hardcoded or "Wizard of Oz" demos where a human manually triggers static responses should be disqualified.
* **Transparent Architecture:** Teams must clearly explain which parts of the project are powered by AI and which rely on traditional deterministic logic.

---

## The Essential Evaluation Checklist

Use this checklist to vet every project before assigning a final score.

### 1. The Technical Baseline

* **Exclusive Tooling:** Is the project built *completely* using Google Antigravity or Google AI Studio? (Mixing in standard frontend frameworks is fine, but the core AI engine must rely on the mandated platforms).
* **Live Data & API Integration:** Does the app process real-world inputs? **No static data** or hardcoded JSON files are allowed. The app must dynamically fetch, generate, or reason over live information.
* **Gemini API Key Setup:** Is the project securely configured with a valid Gemini API key? (Verify that keys aren't exposed in public repositories).
* **The `agents.md` File:** Does the repository include a detailed `agents.md` file? This document is crucial for understanding the backend. It must explain the agent architecture, system prompts, external tools provided to the agent, and its reasoning loop.

### 2. Deployment (Additional Points)

* **Cloud Hosted:** Is the project deployed live using a Google Cloud account?
* **Production Readiness:** Projects that go beyond local development (`localhost`) and successfully deploy scalable infrastructure earn bonus points for execution and technical maturity.

### 3. Team Dynamics & Roles

* **Skill Distribution:** Did the team effectively distribute roles? Look for a healthy mix of skills, such as a Prompt Engineer, Backend/Cloud Architect, Frontend Developer, and Domain Expert.
* **Synergy:** Does the final product reflect a balance of good UX, solid engineering, and creative AI implementation, rather than just being a raw API wrapper built by a single person?

---

## Evaluation Criteria Rubric

Score each project out of 100 points based on the following breakdown:

| Criteria | Weight | Description |
| --- | --- | --- |
| **Technical Depth** | 35% | How well does the project utilize Antigravity's agentic features or AI Studio's multimodality? Is the code clean, functional, and well-prompted? |
| **Innovation & Creativity** | 25% | Does the project solve a problem in a novel way? Are they pushing the boundaries of what Gemini can do, or just building a standard chatbot? |
| **Impact & Utility** | 20% | Does the application solve a real-world problem? Is there a clear target audience that benefits from this solution? |
| **UX & Presentation** | 10% | Is the interface intuitive? Does it handle AI latency gracefully (e.g., loading states, streaming text)? Was the pitch compelling? |
| **Completeness** | 10% | Is it deployed on Google Cloud? Is the `agents.md` thorough and accurate? |

---

## FAQs for Judges

**Q: What if a team uses a different LLM in the backend instead of Gemini?**
Unless the hackathon rules explicitly allow multi-model architectures, projects must strictly adhere to using the Gemini API via AI Studio or Antigravity to qualify for the main prizes.

**Q: How do we verify if they used static data instead of live AI generation?**
During the live Q&A, ask the team to input a highly specific, obscure, or contradictory prompt that they couldn't have prepared for. If the app handles it dynamically, it's live. If it breaks or returns a generic response, investigate their data flow.

**Q: What exactly should be in the `agents.md` file?**
It should read like an architectural diagram for AI. It needs to list the agents used, their specific system instructions, the tools they have access to (like search or external APIs), and how multiple agents communicate with each other if applicable.

**Q: A team has a brilliant idea but the Google Cloud deployment failed at the last minute. How do we score them?**
Evaluate the local build based on Technical Depth and Creativity, but deduct points from the "Completeness" category. A working local prototype demonstrating complex agentic behavior is always better than a broken cloud deployment.

Developer Journey and Operational Flow
Registration and Capacity: The area operates on a first-come, first-served basis with no pre-registration. Each cohort holds a maximum of 25 participants, and attendance is logged via QR code scanning upon entry.
The Coding Session: Each session lasts 50 minutes total, consisting of a 40-minute coding challenge and a 10-minute evaluation period.

Challenge Structure:
Participants are guided by facilitators and have access to wired internet.
Staff will provide subscriptions to participants using their personal (non-corporate) accounts.
Participants are encouraged to share their work online after completion and "hit the alarm" at the exit as a celebration.
Evaluation Area: Located on the area, evaluators—including Experts will assess submissions.

Evaluation Guidelines
Mandatory Requirements: Usage of the Gemini API is mandatory, and solutions must demonstrate actual AI capabilities rather than relying on mock data.
Verification Process: Evaluators should verify if the project solves the problem statement, confirm it works (locally is acceptable), and ask simple questions about the technology stack to ensure the code is not hardcoded.
Efficiency: Evaluations should take 2–3 minutes to keep the process quick and engaging.

Staffing Responsibilities
Support: Staff members are responsible for explaining the challenge, assisting with development queries, and managing slot queues.
Operational Discipline: Sessions must start on time regardless of whether all 25 seats are filled. If a queue forms, staff should manage expectations for upcoming slots.
Resources: laptops will be available for participants who do not bring their own laptops.
Preparation: Staff should review the provided slides and challenge material, including testing Android app development via AI Studio, to create a handy FAQ guide.

Important Logistics
Cloud Credentials: Workshop attendees may have additional cloud credentials that expire at the end of the day, which are distinct from the Vibe Coding activities.
Rehearsal: A rehearsal for the staff should needed to familiarize the team with the space, technology, and movement plan.

these are the instructon
Skills and different roles