---
title: Stop Guessing How to Build an LLM Recommender for Agentic AI into Any Web App
author: Sunny
pubDatetime: 2026-10-18T04:06:31Z
slug: stop-guessing-how-to-build-an-llm-recommender-for-agentic-ai-into-any-web-app
featured: false
draft: false
tags:
  - AI
  - Agent
  - LLM
description:
  Building agentic AI systems is fundamentally different from building standard chat wrappers. An agent doesn't just generate text; it reasons, plans loops, formats tool calls, retrieves context, and self-corrects on failures.

---

Choosing the wrong model breaks your pipeline fast:

* Pick a model weak at **JSON/tool calling**, and your orchestration engine crashes on invalid syntax.
* Pick a heavy frontier reasoning model for a sub-agent task, and your **latency and API bills explode**.
* Pick a model with a small context window, and multi-turn scratchpads truncate mid-execution.

With dozens of viable models—from Claude 3.5 Sonnet, GPT-4o, and Gemini 1.5 Pro to open-weight champions like Llama 3.3, Mistral Large, and Qwen 2.5—developers face choice paralysis.

Here is how you can embed an **LLM Recommender widget** into your web application so users (or your internal team) can pick the optimal model based on real operational constraints.

---

## 1. The Core Architecture

An LLM recommender doesn't need to be an opaque AI black box. In fact, developers prefer **transparent, rule-weighted heuristics** that justify *why* a model was chosen.

```
+---------------------------------------------------------+
|                    Client / UI                          |
|  - Reasoning Level (Low / Mid / High / Frontier)        |
|  - Primary Task (Tool use, Coding, Multi-turn planning) |
|  - Strict Constraints (Budget, Latency, Data Privacy)   |
+----------------------------+----------------------------+
                             |
                             v
+---------------------------------------------------------+
|                  Recommender Engine                     |
|  1. Hard Filtering (e.g., self-hosted only, max price)  |
|  2. Multi-Attribute Scoring (tool calling + latency)    |
|  3. Ranked Output + Explanation Fallback                |
+----------------------------+----------------------------+
                             |
                             v
+---------------------------------------------------------+
|                  Recommended Models                     |
|  - Top Match: Primary Driver                            |
|  - Budget Alternative: Low-cost fallback                |
+---------------------------------------------------------+

```

---

## 2. Model Metadata Schema

To power recommendations, maintain a clean, declarative registry of model specs. Store this as a JSON configuration or a lightweight database table:

```typescript
// models.ts
export interface LLMProfile {
  id: string;
  name: string;
  provider: "OpenAI" | "Anthropic" | "Google" | "Meta" | "Mistral" | "OpenSource";
  inputCostPer1M: number;   // USD
  outputCostPer1M: number;  // USD
  contextWindow: number;    // Tokens
  latencyTier: "ultra-low" | "medium" | "high";
  toolCallingReliability: number; // Scale 1-10
  agenticReasoningScore: number;  // Scale 1-10
  supportsStructuredOutputs: boolean;
  selfHostable: boolean;
}

export const MODEL_REGISTRY: LLMProfile[] = [
  {
    id: "claude-3-5-sonnet",
    name: "Claude 3.5 Sonnet",
    provider: "Anthropic",
    inputCostPer1M: 3.0,
    outputCostPer1M: 15.0,
    contextWindow: 200_000,
    latencyTier: "medium",
    toolCallingReliability: 9.8,
    agenticReasoningScore: 9.7,
    supportsStructuredOutputs: true,
    selfHostable: false
  },
  {
    id: "gpt-4o-mini",
    name: "GPT-4o Mini",
    provider: "OpenAI",
    inputCostPer1M: 0.15,
    outputCostPer1M: 0.60,
    contextWindow: 128_000,
    latencyTier: "ultra-low",
    toolCallingReliability: 8.5,
    agenticReasoningScore: 7.5,
    supportsStructuredOutputs: true,
    selfHostable: false
  },
  {
    id: "llama-3-3-70b",
    name: "Llama 3.3 70B Instruct",
    provider: "Meta",
    inputCostPer1M: 0.60, // approximate via inference provider
    outputCostPer1M: 1.20,
    contextWindow: 128_000,
    latencyTier: "medium",
    toolCallingReliability: 8.8,
    agenticReasoningScore: 8.6,
    supportsStructuredOutputs: true,
    selfHostable: true
  }
];

```

---

## 3. The Recommendation Logic

The engine runs a two-step evaluation:

1. **Hard Filtering:** Immediately drops models that violate non-negotiables (e.g., self-hosting requirements, budget ceilings, context requirements).
2. **Weighted Scoring:** Calculates suitability based on user priorities (speed vs. precision vs. cost).

```typescript
// recommender.ts
export interface UserPreferences {
  maxInputBudgetPer1M?: number;
  requireSelfHost?: boolean;
  minContextTokens?: number;
  priority: "speed" | "quality" | "cost" | "tool-accuracy";
}

export function recommendLLM(prefs: UserPreferences): LLMProfile[] {
  // Step 1: Filter out ineligible models
  const eligible = MODEL_REGISTRY.filter((m) => {
    if (prefs.requireSelfHost && !m.selfHostable) return false;
    if (prefs.maxInputBudgetPer1M && m.inputCostPer1M > prefs.maxInputBudgetPer1M) return false;
    if (prefs.minContextTokens && m.contextWindow < prefs.minContextTokens) return false;
    return true;
  });

  // Step 2: Score eligible models based on user priority
  return eligible.sort((a, b) => {
    switch (prefs.priority) {
      case "speed":
        return getLatencyWeight(a.latencyTier) - getLatencyWeight(b.latencyTier);
      case "cost":
        return (a.inputCostPer1M + a.outputCostPer1M) - (b.inputCostPer1M + b.outputCostPer1M);
      case "tool-accuracy":
        return b.toolCallingReliability - a.toolCallingReliability;
      case "quality":
      default:
        return b.agenticReasoningScore - a.agenticReasoningScore;
    }
  });
}

function getLatencyWeight(tier: LLMProfile["latencyTier"]): number {
  if (tier === "ultra-low") return 1;
  if (tier === "medium") return 2;
  return 3;
}

```

---

## 4. Building the UI Component (React Example)

Give users a clean form with immediate visual feedback. Here is an embeddable React component you can drop into an existing dashboard or onboarding flow:

```tsx
import React, { useState } from "react";
import { recommendLLM, UserPreferences, LLMProfile } from "./recommender";

export const LLMRecommenderWidget: React.FC = () => {
  const [priority, setPriority] = useState<UserPreferences["priority"]>("tool-accuracy");
  const [requireSelfHost, setRequireSelfHost] = useState(false);
  const [maxBudget, setMaxBudget] = useState<number | undefined>(undefined);

  const recommendations = recommendLLM({
    priority,
    requireSelfHost,
    maxInputBudgetPer1M: maxBudget,
  });

  const bestMatch = recommendations[0];

  return (
    <div className="max-w-xl mx-auto p-6 bg-white rounded-xl shadow-md border border-gray-100">
      <h2 className="text-xl font-bold text-gray-900 mb-1">Agent Model Advisor</h2>
      <p className="text-sm text-gray-500 mb-6">Select your system priorities to match the right engine.</p>

      {/* Controls */}
      <div className="space-y-4 mb-6">
        <div>
          <label className="block text-xs font-semibold uppercase text-gray-600 mb-2">Primary Priority</label>
          <div className="grid grid-cols-2 gap-2">
            {(["tool-accuracy", "quality", "speed", "cost"] as const).map((p) => (
              <button
                key={p}
                onClick={() => setPriority(p)}
                className={`py-2 px-3 text-sm rounded-lg border font-medium transition-colors ${
                  priority === p 
                    ? "bg-blue-50 border-blue-600 text-blue-700" 
                    : "border-gray-200 text-gray-700 hover:bg-gray-50"
                }`}
              >
                {p.replace("-", " ")}
              </button>
            ))}
          </div>
        </div>

        <div className="flex items-center justify-between pt-2">
          <label className="text-sm font-medium text-gray-700">Must be self-hostable (VPC / On-prem)</label>
          <input
            type="checkbox"
            checked={requireSelfHost}
            onChange={(e) => setRequireSelfHost(e.target.checked)}
            className="h-4 w-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500"
          />
        </div>
      </div>

      {/* Result Display */}
      {bestMatch ? (
        <div className="p-4 bg-gray-50 rounded-lg border border-gray-200">
          <div className="flex items-center justify-between mb-2">
            <span className="text-xs font-bold uppercase tracking-wider text-green-700 bg-green-100 px-2 py-0.5 rounded">
              Recommended Best Match
            </span>
            <span className="text-xs text-gray-500">{bestMatch.provider}</span>
          </div>
          <h3 className="text-lg font-bold text-gray-900">{bestMatch.name}</h3>
          
          <div className="mt-3 grid grid-cols-3 gap-2 text-center text-xs text-gray-600 border-t border-gray-200 pt-3">
            <div>
              <span className="block font-semibold text-gray-900">${bestMatch.inputCostPer1M}</span>
              Input / 1M tokens
            </div>
            <div>
              <span className="block font-semibold text-gray-900">{bestMatch.toolCallingReliability}/10</span>
              Tool Reliability
            </div>
            <div>
              <span className="block font-semibold text-gray-900">{bestMatch.latencyTier}</span>
              Latency
            </div>
          </div>
        </div>
      ) : (
        <div className="p-4 bg-amber-50 rounded-lg border border-amber-200 text-amber-800 text-sm">
          No model matches your exact constraints. Try loosening the budget or self-hosting filters.
        </div>
      )}
    </div>
  );
};

```

---

## 5. Taking It Further: Dynamic Multi-Model Routing

Recommending a single model is great for onboarding, but the most sophisticated agentic architectures use **dynamic routing**:

1. **Router Model (Fast & Cheap):** An ultra-fast model (like GPT-4o Mini or Gemini 1.5 Flash) categorizes user requests and formulates execution plans.
2. **Worker Model (Reliable Execution):** A medium-tier model executes deterministic sub-tasks and code calls.
3. **Escalation / Auditor Model (Frontier Reasoning):** When a step fails validation twice, escalate the state to a frontier model (like Claude 3.5 Sonnet or o1) for deep replanning.

Embedding this logic into your application turns your recommender from a static selector into a **smart router configuration wizard**—ensuring users achieve high reliability without overpaying for agent compute.

---
 https://www.aimletc.com/