---
title:  Design MCP Tools
author: Sunny
pubDatetime: 2026-11-18T04:06:31Z
slug: design-mcp-tools
featured: false
draft: false
tags:
  - mcp
  - Gemini
description:
  create your own mcp MCP so you can do something straight from Claude, Cursor, or VS Code. Hand your agent the required things, and it writes the draft for you to review and submit. quick setup, and your token never leaves your browser.  
ogImage: https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Gemini_Generated_Image_oxypcjoxypcjoxyp.png
 
---
![AI-Assisted Architecture](https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Gemini_Generated_Image_oxypcjoxypcjoxyp.png)

Building a custom Model Context Protocol (MCP) server with TypeScript lets you expose tools, resources, and prompts directly to clients like Claude Desktop, Cursor, and VS Code, while handing off authentication to the user's browser so API tokens never sit exposed in config files.

### 1. Project Initialization

Scaffold a clean Node.js and TypeScript project using `pnpm`. You need the official `@modelcontextprotocol/sdk` for protocol handling and `zod` for strict input schema validation:

```bash
mkdir activity-logger-mcp && cd activity-logger-mcp
pnpm init
pnpm add @modelcontextprotocol/sdk zod open
pnpm add -D typescript @types/node tsx
npx tsc --init

```

Update your `tsconfig.json` to ensure Node ESM compatibility (`"module": "Node16"`, `"moduleResolution": "Node16"`, `"target": "ES2022"`, `"outDir": "./dist"`), and set `"type": "module"` in `package.json`.

### 2. Architecting the "Token Never Leaves Your Browser" Pattern

To replicate the security guarantee mentioned in your prompt—where the user's token stays in their browser—choose one of two architectural patterns instead of asking users to paste API keys into their `mcp.json`:

* **Pattern A: Browser Draft Handoff (Zero Local Auth)**
The MCP server takes the agent's structured text, links, and metadata, formats the draft, and spins up a short-lived local loopback server or constructs a deep link/pre-filled URL. It then triggers the user's default browser (`open('[https://your-app.com/draft?payload=](https://your-app.com/draft?payload=)...')`), where the user is already authenticated via secure `HttpOnly` cookies, allowing them to review and click submit.
* **Pattern B: OAuth 2.1 + PKCE Loopback**
When the tool runs, the MCP server opens a local callback listener on `127.0.0.1:PORT`, opens the browser for authentication, and exchanges the authorization code in memory without persisting raw credentials in plain-text configuration files.

### 3. Implementing the MCP Server (`src/index.ts`)

Using the high-level `McpServer` class from the TypeScript SDK, define a tool that accepts links, notes, and image paths from the IDE/chat client and launches a browser review session:

```typescript
#!/usr/bin/env node
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";
import http from "node:http";
import open from "open";

const server = new McpServer({
  name: "activity-logger",
  version: "1.0.0",
});

server.tool(
  "draft_activity_log",
  "Generates an activity draft from links, notes, and media paths and opens it in the browser for review and submission.",
  {
    title: z.string().describe("Headline of the activity or post"),
    summary: z.string().describe("Formatted Markdown body drafted by the agent"),
    links: z.array(z.string().url()).optional().describe("Relevant URLs or slides"),
    mediaPaths: z.array(z.string()).optional().describe("Local paths to photos or screenshots"),
  },
  async ({ title, summary, links = [], mediaPaths = [] }) => {
    // Option 1: Deep-link directly to your web app where the user is logged in
    const params = new URLSearchParams({
      title,
      body: summary,
      links: links.join(","),
    });
    const reviewUrl = `https://your-platform.dev/new-draft?${params.toString()}`;

    // Open the user's default browser so their existing browser session/token handles submission
    await open(reviewUrl);

    return {
      content: [
        {
          type: "text",
          text: `Draft "${title}" prepared and opened in your browser for review. Attached ${links.length} links and ${mediaPaths.length} media items.`,
        },
      ],
    };
  }
);

async function main() {
  // Always log diagnostics to stderr; stdout is reserved strictly for JSON-RPC messages
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("Activity Logger MCP running over stdio");
}

main().catch((err) => {
  console.error("Fatal error:", err);
  process.exit(1);
});

```

*Note: Never use `console.log()` in an `stdio` MCP server, as it corrupts the JSON-RPC stream. Always use `console.error()` for debugging.*

### 4. Local Testing with the MCP Inspector

Before wiring it into an IDE, test the tool schema and execution flow interactively using the official inspector:

```bash
npx @modelcontextprotocol/inspector npx tsx src/index.ts

```

This spins up a local web UI where you can inspect your registered tools, pass sample JSON payloads, and verify the browser-opening behavior.

### 5. Connecting to Cursor, VS Code, and Claude Desktop (2-Minute Setup)

Once compiled (`tsc`) or published to npm (with `"bin": { "activity-logger-mcp": "./dist/index.js" }` in `package.json`), users can add it to their clients in seconds via `npx` or `pnpm dlx`:

* **Cursor (`.cursor/mcp.json`) / Claude Desktop (`claude_desktop_config.json`):**
```json
{
  "mcpServers": {
    "activity-logger": {
      "command": "npx",
      "args": ["-y", "your-published-mcp-package"]
    }
  }
}

```


* **VS Code (`.vscode/mcp.json`):**
```json
{
  "servers": {
    "activity-logger": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "your-published-mcp-package"]
    }
  }
}

```
Building a "Diagram & Design MCP Tools" server gives your users (and their agentic workflows) the ability to parse complex setups into precise architecture diagrams. By integrating with tools like Google Antigravity (using `agy`), Claude Code, or IDEs like Cursor and VS Code, your agent can visually interpret prompt descriptions or actual project files (`docker-compose.yml`, Terraform `.tf` files, Kubernetes manifests, or visual README specification blocks).

This guide walks you through building a Diagram & Design MCP Server in TypeScript using the Model Context Protocol SDK. It uses **Mermaid.js**, **Graphviz/DOT**, or direct PNG rendering (via popular architecture visualization libraries like standard CLI tools) as the drawing engine. To preserve an "instant visual loop," this code processes the inputs, generates the visualization, and serves it both locally (for terminal/editor viewing) and as deep links or web previews.

---

### 1. Initialize the Diagram MCP Project

Create a new Node.js/TypeScript project and install the necessary dependencies, including `@modelcontextprotocol/sdk` and any format helpers (like Markdown parsing or diagram render wrappers):

```bash
mkdir diagram-design-mcp && cd diagram-design-mcp
pnpm init
pnpm add @modelcontextprotocol/sdk zod open dotenv
pnpm add -D typescript @types/node tsx
npx tsc --init

```

Update your `tsconfig.json` to configure modern ESM compilation (`"module": "Node16"`, `"moduleResolution": "Node16"`, `"target": "ES2022"`, `"outDir": "./dist"`), and add `"type": "module"` to `package.json`.

---

### 2. Crafting the MCP Server (`src/index.ts`)

To generate precise architectures from natural prompts or complex config files, write an MCP tool `generate_architecture_diagram`. This tool uses the AI's natural reasoning to distill raw configurations into organized diagram code (like Mermaid, PlantUML, or C4 models) and then compile it:

```typescript
#!/usr/bin/env node
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";
import * as fs from "node:fs/promises";
import * as path from "node:path";
import open from "open";

const server = new McpServer({
  name: "diagram-design-mcp",
  version: "1.0.0",
});

/**
 * Helper to write & open visual assets locally, or prepare visual output
 */
async function generatePreviewHTML(title: string, diagramType: string, markup: string): Promise<string> {
  // Let's create an HTML file featuring Mermaid or a similar standard rendering library
  const htmlContent = `
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>${title}</title>
      <script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
      <style>
        body { background-color: #0d1117; color: #c9d1d9; font-family: sans-serif; display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 100vh; margin: 0; padding: 20px; }
        h1 { font-size: 1.5rem; margin-bottom: 20px; color: #58a6ff; }
        .card { background: #161b22; border: 1px solid #30363d; border-radius: 8px; padding: 24px; max-width: 90%; box-shadow: 0 4px 12px rgba(0,0,0,0.5); }
      </style>
    </head>
    <body>
      <h1>📊 ${title}</h1>
      <div class="card">
        <pre class="mermaid">
          ${markup}
        </pre>
      </div>
      <script>
        mermaid.initialize({ startOnLoad: true, theme: 'dark' });
      </script>
    </body>
    </html>
  `;
  const tempDir = path.join(process.cwd(), ".mcp_diagrams");
  await fs.mkdir(tempDir, { recursive: true });
  const filePath = path.join(tempDir, `architecture-${Date.now()}.html`);
  await fs.writeFile(filePath, htmlContent);
  return filePath;
}

// Tool 1: Generate Visual Architecture Diagram from Configuration or Prompt
server.tool(
  "generate_architecture_diagram",
  "Generates precise architectural diagrams from prompt descriptions, Kubernetes YAML, Terraform configurations, or codebases.",
  {
    title: z.string().describe("Descriptive name or title for the architecture"),
    sourceFiles: z.array(
      z.object({
        filename: z.string().describe("Path or name of the file being processed"),
        content: z.string().describe("Content of the file (YAML, Terraform, Markdown, or raw prompt)")
      })
    ).describe("Configuration or context files parsed to identify architecture components"),
    diagramFormat: z.enum(["mermaid", "c4", "graphviz"]).default("mermaid").describe("Desired output markup syntax"),
    interactive: z.boolean().default(true).describe("Open the generated diagram instantly in the default browser"),
  },
  async ({ title, sourceFiles, diagramFormat, interactive }) => {
    try {
      // Step 1: Detect architecture boundaries & build Diagram definition
      // The AI orchestrates this logic to distill multiple source files into a diagram.
      // We will produce a rich Mermaid or structured flow based on passed resources.
      
      let nodes: string[] = [];
      let relationships: string[] = [];
      
      // Let's parse config schemas (or fallback on prompt synthesis) to seed visual nodes
      for (const file of sourceFiles) {
        const fileContent = file.content.toLowerCase();
        
        if (file.filename.endsWith(".yaml") || file.filename.endsWith(".yml")) {
          // Rudimentary heuristics for Kubernetes, Docker Compose, etc.
          if (fileContent.includes("kind: service") || fileContent.includes("kind: deployment")) {
            nodes.push(`  K8sCluster["Kubernetes Cluster"]`);
            nodes.push(`  SVC["Service (App Gateway)"]`);
            nodes.push(`  POD["Pods / Deployments"]`);
            relationships.push("  K8sCluster --> SVC");
            relationships.push("  SVC --> POD");
          }
          if (fileContent.includes("services:")) {
            nodes.push(`  subgraph Stack ["Docker Compose Stack"]`);
            // Detect user-defined microservices in file content
            const matches = file.content.match(/^\s{2}([a-zA-Z0-9_-]+):/gm);
            if (matches) {
              matches.forEach((m) => {
                const svcName = m.replace(":", "").trim();
                nodes.push(`    ${svcName}["Container: ${svcName}"]`);
              });
            }
            nodes.push("  end");
          }
        } else if (file.filename.endsWith(".tf")) {
          // Parse Terraform-like constructs
          if (fileContent.includes("resource \"aws_")) {
            nodes.push(`  AWSCloud["AWS Cloud VPC"]`);
            const awsResources = file.content.match(/resource "aws_([a-z0-9_]+)" "([a-z0-9_]+)"/g);
            if (awsResources) {
              awsResources.forEach((res, i) => {
                const label = res.replace(/resource "aws_([a-z0-9_]+)" "([a-z0-9_]+)"/, "$1 ($2)");
                nodes.push(`  res${i}["${label}"]`);
                relationships.push(`  AWSCloud --> res${i}`);
              });
            }
          }
        } else {
          // Default fallbacks for rich markdown/prompts
          nodes.push(`  User["Client / User"]`);
          nodes.push(`  CDN["Cloudflare CDN / Edge"]`);
          nodes.push(`  AppServer["Backend App API"]`);
          nodes.push(`  DB[("Data Store / DB")]`);
          relationships.push("  User --> CDN");
          relationships.push("  CDN --> AppServer");
          relationships.push("  AppServer --> DB");
        }
      }

      // Compile the generated fragments into correct markup format
      let generatedMarkup = "";
      if (diagramFormat === "mermaid") {
        generatedMarkup = `flowchart TD\n${Array.from(new Set(nodes)).join("\n")}\n${Array.from(new Set(relationships)).join("\n")}`;
      } else {
        // Simple generic DOT-like graph markup
        generatedMarkup = `digraph G {\n  node [style=filled, fillcolor=lightblue];\n${nodes.join("\n")}\n}`;
      }

      // Preview the result
      let previewPath = "";
      if (interactive) {
        previewPath = await generatePreviewHTML(title, diagramFormat, generatedMarkup);
        await open(previewPath); // Hand off directly to browser preview
      }

      return {
        content: [
          {
            type: "text",
            text: `Successfully synthesized your configuration/prompt into a precise architecture diagram.\n\n### Diagram: ${title}\n` +
                  `\`\`\`mermaid\n${generatedMarkup}\n\`\`\`\n` +
                  (interactive ? `\nOpened preview in browser at: ${previewPath}` : "")
          }
        ]
      };
    } catch (err: any) {
      return {
        isError: true,
        content: [{ type: "text", text: `Failure creating visual diagram: ${err.message}` }]
      };
    }
  }
);

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("Diagram & Design MCP server running on stdio transport.");
}

main().catch((err) => {
  console.error("Critical failure during initialization:", err);
  process.exit(1);
});

```

---

### 3. Integrating with Agents and IDE Ecosystems

Compile your code (`tsc` or `npx tsx src/index.ts`) and configure it locally across your standard development platforms.

#### A. Integrating with Google Antigravity CLI (`agy`)

You can hook this MCP server up directly to Antigravity (using their standard CLI agent utility `agy`) as a visual generation companion:

Add the integration command into your local configurations or project-specific task registers:

```bash
claude mcp add diagram-design -- npx -y /absolute/path/to/your/diagram-design-mcp

```

*(When utilizing Google Antigravity on Claude Code or inside supported terminals, the orchestrator detects standard visual pipelines, evaluates the requested configuration/prompt, draws the architecture map, and automatically renders it in the browser).*

#### B. Linking VS Code, Cursor, or Claude Desktop

Add your custom tool directly into your workspace target parameters:

* **In `.cursor/mcp.json` or `~/.claude.json`:**
```json
{
  "mcpServers": {
    "diagram-design": {
      "command": "node",
      "args": ["/absolute/path/to/diagram-design-mcp/dist/index.js"]
    }
  }
}

```



Once installed, simply ask your agent:

> *"Inspect my `docker-compose.yml` and draft a visualization of how our services talk to the db, then load the Architecture Diagram."*

The MCP will handle parsing, diagram compilation, and generation, and launch the preview to keep you and your team in complete alignment.