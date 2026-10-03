---
title: Decoding AI Your 2026 Glossary to the Latest Buzzwords
author: Sunny
pubDatetime: 2026-09-25T04:06:31Z
slug: decoding-ai-glossary
featured: false
draft: false
tags:
  - AI terms
description:
  It feels like overnight, the world started speaking a new language. If your inbox, newsfeed, and meetings are suddenly flooded with acronyms like LLM, RAG, and GenAI, you aren't alone. As artificial intelligence continues to evolve at breakneck speed, keeping up with the terminology is half the battle.
ogImage: https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/licensed-image.jpg)
---
![decoding-ai-glossary](https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/licensed-image.jpg)

Whether you're a professional trying to navigate new tools or just someone wanting to understand the headlines, here is a quick, no-nonsense guide to the most essential AI terms today.


## The Core Dictionary

* **Generative AI (GenAI):** A branch of AI that doesn't just analyze data, but creates brand new content. When you ask a tool to write a poem, design a logo, or code a website from scratch, you are using Generative AI.
* **Large Language Model (LLM):** The "brain" powering most text-based AI assistants today (like ChatGPT, Claude, and Gemini). These massive models are trained on billions of pages of text so they can understand, predict, and generate human-like language.
* **Tokens:** Think of these as the fundamental building blocks of language for an AI. A token isn't always a full word; it might be a syllable or a punctuation mark. When AI tools have limits on how much text you can input or output, they measure it in tokens.
* **Prompt Engineering:** The skill of talking to AI effectively. It’s the practice of designing, structuring, and refining your instructions (prompts) to get the most accurate and useful output from a model.
* **Retrieval-Augmented Generation (RAG):** A technique that gives an AI model a specialized "open-book test" capability. Instead of relying solely on the general knowledge it was trained on, RAG allows the AI to search a specific, private database (like your company's internal documents) to find accurate answers before generating a response.
* **Hallucination:** When an AI model confidently presents false, fabricated, or nonsensical information as if it were an absolute fact. It happens because LLMs are ultimately predicting the next best word, not checking a ledger of truth.
* **Multimodal AI:** AI systems that can process and generate multiple types of data simultaneously. Instead of just text, a multimodal AI can "see" images, "listen" to audio, and "watch" video, allowing for much richer interactions.
* **Artificial General Intelligence (AGI):** The holy grail (and frequent sci-fi trope) of AI research. AGI refers to a hypothetical, future AI that possesses the ability to understand, learn, and apply knowledge across any intellectual task at the same level as a human being.

A barrel file is a module file (usually named index.js or index.ts) that imports and re-exports components, functions, or variables from other files in a directory 

Interoperability is the ability of different products, systems, or organizations to communicate, exchange data, and work together effectively

Monkey patching is a technique used to dynamically add, modify, or suppress the default behavior of a piece of code or a third-party module at runtime without altering its original source files 

FAISS (Facebook AI Similarity Search) is an open-source software library built by Meta AI for fast similarity search and clustering of high-dimensional dense vectors. 

An orchestrators is a central software system that automates, coordinates, and manages complex workflows across different tools, services, or infrastructure. Coordinates multi-agent systems, tool selections, and retrieval steps to run autonomous AI workflows. 

A vector database is a specialized data system designed to store, manage, and index high-dimensional numerical vectors called embeddings. Embeddings are lists of numbers that represent the meaning of words, images, or other data so computers can understand their relationships.  The core difference is that vector databases solve for "what is similar," while graph databases solve for "how things are connected". A vector database converts unstructured data (like text, images, or audio) into high-dimensional numerical vectors (embeddings) to find content with similar meanings or context. A graph database maps data as discrete entities (nodes) and explicit, defined relationships (edges) to trace interconnected networks and paths.

Vector search is a modern data retrieval technique that finds similar items by comparing numerical representations of their conceptual meaning rather than matching exact keywords

Transformers are a foundational deep learning neural network architecture that process entire sequences of data simultaneously rather than step-by-step, using self-attention mechanisms to understand context and relationships between data elements 

LangChain is the original ecosystem created for Python and JavaScript/TypeScript. LangChain4j is an independent, community-driven framework built from scratch specifically for the Java Virtual Machine (JVM). It is not a direct code port of the Python library.

An Ollama container is a lightweight software environment that acts as the local engine to run and serve AI models like Google's Gemma on your hardware.
Ollama (The Engine/Container): Ollama is the runtime program (which can be packaged inside a Docker container) that manages the heavy lifting of AI inference, memory allocation, and hardware acceleration. It exposes a local API (typically on port 11434) so other apps can talk to the AI. 

Gemma (The Model): Gemma is an open-weight family of large language models created by Google. Gemma cannot run on its own without software to interpret its weights and process prompts. 
How They Connect: Think of the Ollama container as the vehicle engine and chassis, and Gemma as the specific fuel or driver module you load into it. By executing a command like ollama pull gemma inside an Ollama Docker Container, you download Google's Gemma model weights into Ollama, allowing you to run the AI completely offline, locally, or inside a microservice architecture
Gemma is Google's open-weight model family designed for local execution, while Gemini is Google's flagship proprietary cloud AI service accessed via web apps or APIs.

Supabase is an open-source, full-stack backend-as-a-service platform built on top of the PostgreSQL relational database. It serves as a popular alternative to Firebase 

giscus A comments system powered by GitHub Discussions. Let visitors leave comments and reactions on your website via GitHub! Heavily inspired by utterances.
View the file path: Run npm config get userconfig to see the exact path of the global file being used.
View the contents: Run npm config list to see all active configurations. Look for lines starting with your Artifactory URL, which should include an auth token or base64 string.
 https://github.com/settings/installations/
 https://github.com/{username}/{reponame}/settings/installations
 https://github.com/apps/giscus
 https://giscus.app/

A workflow is a structured, repeatable sequence of tasks, steps, or decisions used to complete a business process or operational goal. We can use Google Cloud for this also. workflow is the sequence of steps needed to complete a task, while automation is the use of technology to execute those steps without human effort. 
https://cloud.google.com/workflows

Large Language Models (LLMs) are advanced artificial intelligence systems trained on massive amounts of text to understand, summarize, generate, and translate human language. Work on llm integration.

Agentic AI refers to autonomous artificial intelligence systems that can reason, plan, use tools, and execute multi-step actions to achieve a goal with minimal human intervention. Agentic AI operates in a continuous loop of perception, decision-making, action, and reflection 
Prompt guardrails are external security and logic layers that sit between users and AI models to validate, filter, and sanitize inputs and outputs. They act like middleware to ensure safety, privacy, and compliance. 
```
project structure1
 /agent_project
    ├── /prompts
    │   ├── worker_system_prompt.txt     # Instructs the agent to prioritize facts
    │   └── reviewer_system_prompt.txt   # Instructs the agent to check the worker's math/logic
    ├── /tools
    │   └── search_database.py           # The script the agent uses to fetch real data
    ├── /context_data                    # The trusted documents the agent reads from
    └── main_agent_loop.py               # The code that connects the prompt, tools, and LLM
	
	/agent_ecosystem
├── agents.md                    # The Master Manifest/Router (Orchestrator Profile/Agent)
├── /knowledge_base              # The context the agents read
│   ├── part1_architecture.md    # Domain-specific knowledge
│   └── part2_integration.md     # Domain-specific knowledge
└── /agent_roles                 # Specific instructions for each agent
    ├── researcher_prompt.md
    └── reviewer_prompt.md
	{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "choices": [
    {
      "message": {
        "role": "assistant",
        "content": "Hello! I am an AI model. How can I help you today?"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 15,       // The number of tokens in your initial question
    "completion_tokens": 14,   // The number of tokens the AI generated in its answer
    "total_tokens": 29         // The total amount deducted from your quota/context window
  }
}
```
Practice Project

Guidelines for Defining Context in `.md` Files**
Use Frontmatter (Metadata)
use environment variables 
Markdown Tables for Logic
define the Output Format
```
{
  "mcpServers": {
    "my-mcp-server": {
      "command": "node",
      "args": ["/path/to/server/index.js"],
      "env": {
        "API_KEY": "your_key_here"
      }
    }
  }
}
```
To start implementing AI in CRUD app-
Semantic Search (Upgrading your "Search" & "Sort") ->  embedding model like OpenAI's `text-embedding-3-small`
Automated Data Enrichment (Upgrading "Create" & "Update") -> standard generative model like Claude 3.5 Haiku or Gemini Flash
Chat with your Data" / RAG (Upgrading "Read")
```
project structure2
/your_app_root
├── /controllers
│   └── crud_controller.js         # Your existing logic
├── /ai_services                   # NEW: Isolate AI logic here
│   ├── embedding_service.js       # Handles turning text into searchable vectors
│   ├── enrichment_agent.js        # Handles auto-tagging and summarization
│   └── /prompts                   # Store your AI instructions as text files
│       └── data_cleanup_prompt.md
└── /routes
    └── api_routes.js
```
best way to use Coding Agents
Start in Plan Mode
Use Edit Mode for Atomic Tasks Only
Maintain a PLAN.md at the Root of Your Project - it should have well defined structure
Teach the Agent How You Work
Version Everything — Including Agent Sessions
Validate Everything — Especially ML Outputs
Use Agents for Structure, Not Creativity
Keep a Session Log
Treat It Like a Collaborator, Not a Replacement
 
 Quick Start Checklist
 
 ai-config .md file -< AGENTS.md general -> add in root dir -> under 2,000 words
 Project Context & Memory

The AGENTS.md file is a universal standard used to provide context and persistent instructions directly to AI coding agents (such as Claude Code, Cursor, GitHub Copilot, Windsurf, and Devin). Think of it as a "README for machines".
Historically, different AI tools required their own configuration files (e.g., .cursorrules or CLAUDE.md). However, an industry consensus established AGENTS.md as the open, platform-agnostic standard
https://agents.md/
```
 project structure3
 CLAUDE.md
 /weather-dashboard
├── .antigravity/          <-- The "Agent Brain" folder
│   ├── plan.md            <-- The visible step-by-step blueprint
│   └── checkpoints/       <-- Snapshots to "Undo" agent mistakes
├── src/
│   ├── components/
│   ├── hooks/             <-- Agent chooses to modularize logic
│   └── App.js
├── tailwind.config.js     <-- Agent runs 'npx tailwindcss init'
└── package.json
```
```
project structure4
context.md

*Never store your AI API keys in your Angular frontend.

/ai-middleware
├── .env                     # Keep your GOOGLE_API_KEY here (never commit this)
├── package.json             # Your dependencies
├── server.js                # The main entry point
├── /routes
│   └── ai.routes.js         # Defines the API endpoints (e.g., /api/enrich)
├── /controllers
│   └── ai.controller.js     # The actual logic that talks to the Gemini API
└── /prompts
    └── system.prompts.js    # Store all your system instructions here
	controllers/search.controller.js
	
	/ai-middleware
├── /db
│   └── index.js             # PostgreSQL connection pool
├── /controllers
│   └── search.controller.js  # Updated to include PG queries
├── /routes
│   └── ai.routes.js
└── server.js
rag.controller.js
recommendation.controller.js
```
	MCP: List Servers - on any agent tool
	Try out popular mcp servers 
	mcp.json
	
Skill location - two places to put
<workspace-root>/.agents/skills/<skill-folder>/	Workspace-specific
~/.gemini/config/skills/<skill-folder>/	Global (all workspaces; legacy ~/.gemini/antigravity/skills/ is also supported)
.agents/skills/git-commit-writer/SKILL.md now it will be visible in ide

1. **Global** — `~/.claude/CLAUDE.md` — applies to every session on your computer
2. **Project** — `ProjectFolder/CLAUDE.md` — applies only when working in that folder
Project skills, stored in your repository	.github/skills/, .claude/skills/, .agents/skills/
Personal skills, stored in your user profile	~/.copilot/skills/, ~/.claude/skills/, ~/.agents/skills/

Skills
Create a file at `~/.claude/skills/monthly-report.md`:


Skills in AI are modular, reusable instruction packages (often stored as SKILL.md files in a folder) that teach large language models and AI agents how to perform specialized, multi-step workflows You can explore community-made packages and guidelines at platforms like [Agent Skills](https://agentskills.io/home) or skills.sh 
Complete the SKILL.md file by filling in the YAML frontmatter and adding instructions in the body of the file.

---
name: skill-name
description: Description of what the skill does and when to use it

---

# Skill Instructions

Your detailed instructions, guidelines, and examples go here...

Custom agents are defined in a .agent.md
Workspace	.github/agents folder
Workspace (Claude format)	.claude/agents folder
User profile	~/.copilot/agents or ~/.claude/agents
.ps1 and .sh files are both script files used to automate tasks on computers, but they belong to different operating systems and command-line environments.
Set-ExecutionPolicy RemoteSigned 
The fundamental difference is that .ps1 files are written for Microsoft PowerShell, which is primarily used on Windows, whereas .sh files are Shell scripts written for Unix-like environments such as Linux and macOS


copilot guide example implementation

always test anything generated with ai because it looks more confident.

The choice between open-weight models (such as Llama 4 or Qwen 3) and API-access models (such as GPT-5.4 or Claude 4.6) is a foundational architecture decision. Open-weight models allow you to download the pre-trained weights and run them on your own infrastructure, giving you total control, data privacy, and a zero per-token cost structure. In contrast, API-access models are managed entirely by third-party providers, offering immediate access to the highest-performing "frontier" intelligence without requiring complex hardware or maintenance 
Multi-model orchestration is the practice of connecting an application or workflow to multiple AI models so a central layer routes each request to the model that best fits the specific task, cost target, latency requirement, or quality threshold 

Streaming UI displays AI-generated text or data piece-by-token as it is created, while non-streaming (blocking) UI waits for the entire response to finish before showing anything 
An MCP server tool is an executable function exposed by a Model Context Protocol server that allows AI models to interact securely with external systems and data 

Awesome MCP Servers  https://mcpservers.org/

A streamed response sends data in real-time pieces (tokens) as the server generates them, whereas a non-streamed response waits until the entire output is finished before sending it all at once

MLOps (Machine Learning Operations) is a set of practices that combines machine learning, software engineering, and DevOps to deploy and maintain machine learning models in production reliably and efficiently.
Key Stages of the MLOps Lifecycle
Data Preparation: Collects, cleans, and versions datasets to ensure high quality and reproducibility.
Model Training: Runs experiments, tunes settings, and validates model accuracy.
Packaging and CI/CD: Wraps models into containers (like Docker) and automates testing via continuous integration.
Deployment: Pushes models to production for online or batch predictions.
Monitoring: Tracks performance metrics and watches for data drift to know when retraining is neede
Natural Language Processing (NLP) is a subfield of artificial intelligence (AI) and computer science that helps computers understand, interpret, and generate human language 

MLOps in Natural Language Processing (NLP) is the practice of automating and standardizing the lifecycle of text-based machine learning models—from text cleaning and tokenization to deployment and monitoring 

A context window is the maximum amount of text—measured in tokens—that a large language model can hold in its working memory during a single interaction code behaves the same way as regular text inside a context window. Code files, snippets, system prompts, and chat histories all get broken down into smaller pieces called tokens. They all compete for the exact same space in the model's active working memory 

A feedback loop is a continuous cycle where the output of a system circles back to act as an input, shaping future behavior . A feedback loop in AI is a continuous cycle where an AI system's outputs are evaluated and the results are fed back into the model to improve future performance 

Hugging Face Transformers is an open-source deep learning library that provides APIs and tools to download and fine-tune state-of-the-art pre-trained models 


Google Cloud Platform (GCP) is a suite of modular cloud computing services and infrastructure provided by Google for building, running, and managing applications 
Core Services
Compute: Run virtual machines via Compute Engine, or deploy scalable containers using Google Kubernetes Engine (GKE) and Cloud Run.
Storage and Databases: Store unstructured or structured data reliably with scalable global options.
Data and AI: Analyze large datasets and build machine learning models using tools like BigQuery and Vertex AI.
Networking: Connect resources securely via fast global Virtual Private Clouds (VPCs) backed by Google's private network backbone

Stitch is an AI-native user interface (UI) design tool by Google Labs that transforms natural language prompts, wireframes, and sketches into high-fidelity web and mobile application designs.  Try this in ai studio

Explore the open-source implementation and toolkit through the GenMedia Creative Studio https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio on Vertex AI, which brings together Google DeepMind's powerful generative media models into a unified platform. 
Core Components
Image Generation: Powered by Nano Banana (Imagen) models for conversational editing, batch processing, and strong character consistency across scenes. 
Video Generation: Utilizes Veo and Gemini Omni to produce high-resolution, cinematic clips with synchronized audio and sequential editing. 
Music Composition: Driven by the Lyria family of models for structured, professional-grade background tracks and vocal styles. 
Speech and Audio: Uses Gemini Text-to-Speech (TTS) and Chirp for expressive narration and multi-character voiceovers
you do not need the LangChain SDK to build AI agents with Gemini. You can build functional agents natively using Google's official google-genai SDK. 
Choosing between a native build and an external framework depends entirely on the complexity of your project and your deployment preferences.

Option 1: Native Gemini Agent (No LangChain Required)
Gemini models natively support Function Calling, which is the core mechanism behind AI agents. You provide the model with a list of tools (written as standard Python functions), and Gemini acts as the "brain"—deciding when to call a tool, extracting the necessary arguments, and processing the results. 
How it works: You pass your tools directly into the Gemini API client (google-genai), and the model automatically handles the reasoning loop.
When to use it: When you want maximum execution speed, less code overhead, or plan to deploy your agent using Google Cloud's Reasoning Engine (Vertex AI's managed runtime for agents). 

Option 2: Using LangChain / LangGraph with Gemini
LangChain is an orchestration framework, not a requirement. It wraps around Gemini to provide structured templates, pre-built memory management, and easier integrations with third-party tools. 
How it works: You install packages like langchain-google-genai to use Gemini as the underlying LLM inside a LangChain or LangGraph workflow.
When to use it: When you are building highly complex, stateful, or multi-agent systems that require cyclic loops, advanced human-in-the-loop workflows, or if your application logic needs to stay model-agnostic. 

Direct Comparison
Feature
Native Gemini SDK (google-genai)
LangChain / LangGraph SDK
Dependency
Light (Google official SDK only)
Heavy (Requires third-party wrappers)
Agent Mechanics
Built-in native Function Calling
Abstraction layers (create_agent)
State Management
Handled manually or via Vertex AI runtime
Advanced, structured persistence (graphs/nodes)
Multi-Agent Setup
Complex to code from scratch
Seamless (via LangGraph or CrewAI)
Cloud Ecosystem
Deep integration with Google Cloud / Vertex AI
Works across any cloud or model provider

If you are just getting started or building a single-agent system that interacts with a few APIs, stick to the native Gemini SDK. If you need multiple agents passing tasks back and forth, look into LangGraph

LangGraph is an open-source, low-level orchestration framework built by the LangChain team to design and deploy stateful, long-running, and cyclical AI agent workflows

Google ADK (Agent Development Kit) is powerful, but it is built primarily for structured, multi-agent production workflows—often tied to enterprise environments or Google Cloud 

Genkit https://genkit.dev/ is an open-source framework by Google for building full-stack, AI-powered, and agentic applications 

Android Studio Quail is a major release cycle of Google's official IDE for Android development, bringing heavily optimized tools built for the agentic AI era 

Agents in vscode - how to create, load, 

A kernel in a notebook is the "computational engine" or background process that runs your code, stores variables in memory, and returns results to the interface 

In a package-lock.json file, resolved and integrity are security and verification fields that ensure every developer on your team and your production servers download the exact same, untampered code 
resolved
This field tells npm exactly where to download the package from. 
For registry packages: It is a direct URL to the package's zipped archive (tarball) hosted on the npm Registry or a private registry.
For Git dependencies: It will display the full Git repository URL alongside the specific commit SHA hash. 
2. integrity
This field is a unique cryptographic checksum (usually a sha512 or sha1 hash) of the downloaded package file.
It follows the Subresource Integrity (SRI) standard. When you run npm install, npm hashes the freshly downloaded file and checks if it matches this string. If the hashes don't match, npm blocks the installation

FastMCP https://gofastmcp.com/getting-started/welcome is a standard, Python-first framework designed to make building Model Context Protocol (MCP) servers, clients, and interactive applications fast and simple

FastAPI is a general-purpose web framework built to expose standard REST APIs to public-facing clients and traditional frontends, FastMCP is a framework explicitly designed to build Model Context Protocol (MCP) servers, allowing AI agents and Large Language Models (LLMs) to interact with external data, resources, and tools

Agentic workflows are dynamic artificial intelligence processes where autonomous systems plan, use tools, and loop through steps to reach a goal with little human help 

The [@google/genai](https://www.npmjs.com/package/@google/genai) package is primarily designed to be used in the backend (server-side) 

Gemini 3 models
Gemini 2.5 Pro models
Gemini 2.5 Flash models
Gemini 2.0 models
Live API models
Audio models
Embedding models
Imagen models
Veo models
Gemini Omni Flash models
Lyria models
Robotics models
Managed agents

Stable  Stable models usually don't change. Most production apps should use a specific stable model. -> flash
Preview  Preview models will typically have billing enabled, might come with more restrictive rate limits and will be deprecated with at least 2 weeks notice.
Latest This can be a stable, preview or experimental release
Experimental  not be suitable for production us

Gemini 3.8 Flash
Our most intelligent Flash model, engineered for long-horizon software engineering, autonomous agents, and complex enterprise workflows.



Gemini 3.8 Live
Default Live API model for most low-latency voice agent experiences without reasoning delays.
gemini-3.8-live  


Gemini 3.8 Live Extended Thinking
High-reasoning Live API model for voice interactions, recommended when higher background reasoning is required.
gemini-3.8-live-extended-thinking


Gemini 3.7 Flash
Our previous-generation Flash model for complex coding, agentic workflows, and reliable multi-step execution.

Gemini 3.6 Flash
Our previous-generation Flash model, balancing speed and multimodal capabilities across general agentic and everyday tasks.

Gemini 3.5 Flash
Our legacy Flash model, providing baseline speed and foundational performance for routine, high-throughput workloads.

Gemini 3.5 Flash-Lite
Our fastest, most cost-effective 3.5 model for high-throughput execution.

Gemini 3.1 Flash-Lite
Frontier-class performance rivaling larger models at a fraction of the cost.

Nano Banana 2
Powerful, high-efficiency image generation and editing, optimized for speed and high-volume use cases.

Nano Banana 2 Lite
Ultra-low latency and cost-effective image generation and editing, designed for high-volume interactive use cases.

Nano Banana Pro
State-of-the-art image generation and editing models for highly contextual native image creation.

Gemini 3.5 Transcribe

Gemini-2.0-flash deprecated

AI reasoning is a process where an artificial intelligence model pauses to "think" step-by-step using internal hidden tokens before generating a final response

Calculate the cost. In the Gemini API, how to get cost of thinking tokens.

```
{
    "promptTokenCount": 172,
    "candidatesTokenCount": 6,
    "totalTokenCount": 596,
    "promptTokensDetails": [
        {
            "modality": "TEXT",
            "tokenCount": 172
        }
    ],
    "thoughtsTokenCount": 418,
    "serviceTier": "standard"
}
```

Agentic AI shifts artificial intelligence from passive text generators to proactive systems that perceive, plan, call tools, and execute multistep tasks autonomously. Google provides a full-stack ecosystem to build, orchestrate, and deploy these autonomous agents across consumer and enterprise environments.

---

### Core Technology Stack

| Layer | Primary Google Technologies | Role in Agentic Workflows |
| --- | --- | --- |
| **Reasoning & Planning Brain** | Gemini Models (Ultra, Pro, Flash), Gemma (open weights) | Serves as the cognitive engine for intent decomposition, multi-turn reasoning, and structured output formatting. |
| **Agent Orchestration** | Vertex AI Agent Builder, Agent Development Kit (ADK), LangChain on Vertex | Coordinates multi-agent collaboration, handles agent memory, and manages workflow state machines. |
| **Tools & Execution** | Vertex AI Extensions, Function Calling API, Model Context Protocol (MCP) | Allows agents to take external actions, query databases, execute code in sandboxes, and trigger REST APIs. |
| **Grounding & Knowledge** | Vertex AI Search, Enterprise Datastores, Google Search Grounding | Connects agents to live web search and internal enterprise systems (BigQuery, Drive, Jira) to reduce hallucinations. |
| **Governance & Guardrails** | Model Armor, Vertex AI Safety Filters, Cloud IAM | Enforces permission boundaries, checks for prompt injection, and audits agent actions. |

---

### Key Capabilities in Practice

* **Dynamic Tool Calling:** Instead of answering purely from internal weights, Gemini determines which tools to call (e.g., querying an inventory database or booking a calendar event), waits for API responses, and continues execution iteratively.
* **Multi-Agent Orchestration:** Specialized agents handle distinct stages of an objective (e.g., a triage agent receives a request, delegates analysis to a data agent, and passes findings to an executive-summary agent).
* **Multimodal Actions:** With Gemini Live API and multimodal inputs, agents process audio, video feeds, and UI screen states in real time to trigger physical or digital actions (such as robotics control or automated software navigation).
* **Grounding with Enterprise Data:** Agents use Vertex AI Search as a Retrieval-Augmented Generation (RAG) backend to retrieve verified factual documents before planning next steps.

---

### Common Enterprise Use Cases

* **Customer Care Resolution:** Moving beyond static FAQ bots, customer service agents can autonomously process refunds, verify account status in CRM systems, and rebook appointments.
* **Autonomous IT & DevOps:** Ingesting monitoring alerts, diagnosing stack traces, and triggering automated Cloud Run or Kubernetes rollbacks with human approval checkpoints.
* **Supply Chain & Logistics:** Tracking live shipment anomalies, rerouting shipments via vendor APIs, and reconciling supplier invoices.


The **token context window** is the maximum amount of information—measured in tokens—that a Large Language Model (LLM) can read, process, and retain in memory at a single time.

**Tokens and the Context Window**

* **What a token is:** Models do not read full words or sentences directly; they split text into fragments called tokens. In English, 1 token is roughly 3/4 of a word (100 tokens ≈ 75 words).
* **The "working memory":** Think of the context window as the model's short-term working memory. Everything in an ongoing exchange—your instructions, uploaded files, past messages in the conversation, and the response the model generates—must fit inside this limit.
* **What happens when it exceeds:** If a conversation or document exceeds the window, older tokens drop off ("forgetting") unless condensed, summarized, or managed via external tools like retrieval systems (RAG).
* **Size variation:** Context windows vary widely by model. Early LLMs supported 2,048 to 4,096 tokens, whereas modern architectures typically range from 128,000 tokens (e.g., standard GPT-4 setups) up to 1 million or 2 million+ tokens (e.g., Google's Gemini models).

---

**Is Copilot a Model?**
**No, Copilot is an application (or product), not an underlying base model.**

* **The Interface/Product:** Microsoft Copilot (and GitHub Copilot) is an AI-powered assistant product integrated into apps like Windows, Microsoft 365 (Word, Excel, Teams), web browsers, and code editors.
* **What powers it:** Under the hood, Copilot calls underlying foundation models—primarily OpenAI’s GPT models (such as GPT-4o) along with custom orchestration layers (like Microsoft's Prometheus architecture) and specialized coding models.

----------
To turn unstructured conversational responses into clean, queryable database records, you use **Structured Outputs** (JSON Schema/Tool Calling) to force the LLM to return data in a strict contract, then decompose that JSON across a normalized database schema.

---

**Step 1: Enforce Strict JSON from the Model**

Never ask an LLM to return free-form text if your app needs to parse it. Instead, define an exact schema using tools like **Pydantic** (Python) or **Zod** (TypeScript) and use native structured output features:

```json
{
  "trip_name": "Weekend in Tokyo",
  "destination": "Tokyo, Japan",
  "days": [
    {
      "day_number": 1,
      "date": "2026-10-12",
      "activities": [
        {
          "title": "Visit Senso-ji Temple",
          "start_time": "09:30",
          "location_name": "Senso-ji, Asakusa",
          "cost_usd": 0,
          "category": "sightseeing"
        }
      ]
    }
  ]
}

```

---

**Step 2: Relational Database Schema (PostgreSQL)**

To allow users to bookmark, edit, or delete individual activities without corrupting the rest of the plan, divide the data into parent-child relational tables:

```
users (1) ───< trips (N) ───< itinerary_days (N) ───< activities (N)
                                                           │
                                                           └──< places (cached coords/details)

```

* **`trips` Table (Parent Record)**
* `id` (UUID, Primary Key)
* `user_id` (Foreign Key referencing `users`)
* `title` (e.g., "Weekend in Tokyo")
* `destination_city` (e.g., "Tokyo")
* `start_date`, `end_date`


* **`itinerary_days` Table (Daily Segments)**
* `id` (UUID, PK)
* `trip_id` (FK referencing `trips.id` with `ON DELETE CASCADE`)
* `day_number` (e.g., `1`, `2`, `3`)
* `calendar_date` (e.g., `2026-10-12`)


* **`activities` Table (Atomic Granular Info)**
* `id` (UUID, PK)
* `day_id` (FK referencing `itinerary_days.id`)
* `title` (e.g., "Senso-ji Temple")
* `category` (`sightseeing`, `food`, `transit`, `hotel`)
* `start_time`, `end_time`
* `estimated_cost`
* `is_saved` (Boolean, default `false` for user favorites)


* **`places` Table (Metadata & Geolocation)**
* Store geocoordinates (latitude/longitude), Google Place IDs, addresses, and operating hours separately so you can plot them on map widgets without querying the LLM again.



---

**Step 3: The End-to-End Implementation Flow**

1. **Prompt & Generation:** The user prompts: *"Plan a 3-day trip to Tokyo."* Your backend calls OpenAI or Gemini with `response_format` set to your Pydantic/Zod schema.
2. **Backend Ingestion:** Your API validates the output against the schema.
3. **Database Write:** In a single database transaction, insert into `trips`, loop over days into `itinerary_days`, and batch-insert each event into `activities`.
4. **UI Granularity:** The frontend receives the records with individual database IDs (`activity.id = "act_123"`).
5. **Targeted User Actions:**
* When a user taps **"Save Activity"**, the frontend sends `POST /api/activities/act_123/save`.
* When they want to adjust timing, send `PATCH /api/activities/act_123` with the updated time.
* You only query the LLM once for generation; all subsequent interactions (reordering, saving, checking off) run as fast, deterministic database updates.

GPT-6 model family-
GPT-6 Astra
GPT-6.1 Sol
GPT-6 Luna
https://developers.openai.com/api/docs/guides/latest-model

MCP (Model Context Protocol) and APIs (Application Programming Interfaces) are not competing technologies; rather, MCP acts as an AI-friendly abstraction layer that sits on top of traditional APIs. While an API is designed for rigid, software-to-software communication, MCP is designed to translate those APIs into a format that AI models and agents can dynamically discover and use.

## Key Differences at a Glance

| Feature | Traditional API (REST / GraphQL) | Model Context Protocol (MCP) |
|---|---|---|
| Primary Consumer | Software applications and developers | AI Large Language Models (LLMs) and agents |
| Workflow Nature | Fixed & Deterministic: Code dictates exactly when and how to call an endpoint. | Dynamic & Agentic: The AI model decides which tool to call at runtime based on user intent. |
| State Management | Typically Stateless (requires the developer to handle session states manually). | Stateful (maintains context across multi-step agent workflows natively). |
| Integration Complexity | M × N problem: Every new app needs custom, hardcoded integrations for every new service. | M + N solution: One universal MCP client talks to any standardized MCP server wrapper. |
| Discovery Mechanism | Manual (developers read documentation and hardcode the endpoints). | Runtime Discovery: The AI automatically scans a manifest of available tools and schemas. |
| Primitives | HTTP Methods (GET, POST, PUT, DELETE) mapped to resources. | Tools (executable actions), Resources (data/logs), and Prompts (reusable templates). |

------------------------------
## Understanding the Core Concepts
## What is an API?
An API is a structural contract allowing two programs to exchange data. Every individual API has its own unique endpoints, authentication methods (like OAuth or custom API keys), and data structures. A developer must write explicit code to fetch data from Endpoint A, transform it, and pass it to Endpoint B.
## What is MCP?
Introduced as an open standard, MCP normalizes how AI applications connect to data sources and tools. Instead of giving an AI model direct, dangerous access to raw network URLs and secret API keys, you wrap your existing APIs inside an MCP Server. The AI model communicates safely with the MCP Server using a uniform language (JSON-RPC 2.0), leaving the server to handle the actual underlying API plumbing. 
## When to Use Which?

* Use a Traditional API when building predictable, scripted automation. If you need a website button to reliably save user data to a database every time it's clicked, a standard API is the correct, highly efficient tool. 
* Use MCP when you are building AI agents or natural language workflows. If you want an AI assistant inside an IDE or chat interface to dynamically decide whether it needs to fetch a file, run a terminal command, or look up documentation to solve a user's prompt, MCP provides the exact runtime flexibility required.

Vibe coding
Build web apps directly from prompts without boilerplate.

Database backends
Connect Google AI Studio prompt outputs to Cloud SQL.

rules, skills, MCP

AG-UI, A2UI, and MCP Apps are complementary layers in the modern AI agent protocol stack rather than competing standards, working together to deliver generative user interfaces

Agents & Harness
Evaluation & Security
NLP
LangGraph
OpenAI SDK, Claude & Grok
Google ADK
SLMs
Building with TypeScript or Python

AI inference is the operational phase where a trained artificial intelligence model processes live, unseen input data to make real-time predictions, generate content, or solve tasks

MCP hosts
MCP client
MCP server


1. AI Security and Governance
2. Running Offline AI on Mobile Devices
3. MCP at Scale: Going Serverless
4. Architecting Temporal Graph Memory Harness for Evolving State in AI Agents
5. Stop Prompting, Start Engineering: The Architecture Behind Production AI Agents
6. 1 Million tokens are not an agent‚Äôs memory
7. Building Intelligent Copilots with Advanced AI Builder Capabilities in Copilot Studio
8. Voice Agents: Ready for Production or Just a Great Demo?
9. AI (Claude) is the sidekick, YOU are the protagonist
10. How Ai employees are redefining the way companies work ?
11. Can AI Fix Your Relationship? Designing an Agentic AI System from First Principles
12. From ‚ÄúIt Works‚Äù to ‚ÄúIt Survives‚Äù: Harness Engineering & Evals for Production AI
13. Stop Vibe Coding. Start Vibe Engineering.
14. How to create Voice Google ADK agents via agents cli
15. From Commit to Production: Building AI-Ready CI/CD Pipelines with Harness
16. Evaluating LLMs in Production -Pre-Deployment Testing vs. Real-Time Monitoring
17. Building the machine-readable web
18. Same AI,Different Answers: Understanding the Real Impact of RAG
19. A Guardrail Blueprint for Production AI Agents
20. The App Store for AI Agents
21. Building AI Agents with TypeScript & Google ADK
22. The Modern Developer Doesn't Write Code Anymore
23. Your evals are lying to you
24. From Vibe Coding to Verifiable Coding
25. From AI Hype to AI Engineering: Building Practical AI Systems in the Real World
26. Production-Ready AI Agents: From LLM Demos to Secure, Reliable Autonomous Systems
27. From prototype to Production: Engineering Trustworthy AI Agents Across Frameworks
28. Beyond Chatbots: Building Safe AI Agents for Real Business Applications
29. I Built a Browser That Runs Its Own LLM
30. 30 min
31. Any but llm
32. We Can't Improve What we Can't Measure: Evals, Harnesses, and Silent  Failure in Production Agents
33. Agents for healthcare regulatory documentation
34. Agents Need to Forget, Not Remember
35. The new B2B buying journey with LLM search
36. Self healing agents
37. A brief intro to Inference Engineering for Software Engineers
38. Inference Engineering at Scale
39. Context Rot
40. Self-improving agent architectures
41. Keeping Ontologies Alive in Fast-Moving Domains
42. Building a Spec-Driven Multi-Agent Software Engineering System
43. How to Create Optimized Workflows with AI Agents
44. MCP is Alive and How to Use it the Best Way
45. stop starting your AI agents from zero: give them memory
46. Inside a Multi-Agent AI System: ReAct, Reflection, Planning, and Critic Agents in One Enterprise Workflow
47. AI web stack : Keeping future ready
48. Agentic workflows for PIM, DAM and CMS world
49. Your AI Is Leaking Data - Here‚Äôs How to Fix It with PII Shield
50. Progressive Tool Disclosure for Multi-Tenant Agents

The AI landscape will inevitably keep shifting, but understanding these foundational concepts will give you the footing you need to adapt to whatever tools come next.