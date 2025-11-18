# llm-tools

LLM tools - a history

# A Short History of “Tools” in the LLM Era

> *How we went from framework-specific “functions/plugins/skills” to a shared, protocol-level idea of tools (MCP).*

---

## 1. Pre-LLM Roots (Very High Level)

Before modern LLM “tools,” the **core idea** already existed under other names:

- **Webhooks / APIs / Web services** – external capabilities you can call over HTTP.
- **Database drivers / ORMs** – structured access to data sources.
- **Chatbot integrations** (Slack bots, etc.) – bots calling APIs based on intents.

What changed with LLMs is **who chooses** *when* to call those capabilities:  
instead of humans or hard-coded logic, **the model** chooses when and how to call them, via structured outputs.

---

## 2. Early LLM Tooling: Framework-Specific Abstractions

### 2.1 LangChain: “Tools” for Agents (2023 →)

**Ecosystem:** LangChain (Python & JS)  
**Concept name:** **Tools** (and Toolkits)  

LangChain formalized the idea of giving an LLM “buttons to press”:

- A **Tool** wraps a function, API call, or DB query with a name and argument schema.
- **Agents** dynamically decide which tools to call in a loop to solve tasks. :contentReference[oaicite:1]{index=1}  

This made “LLM + tools + loop” a standard mental model, but it was **LangChain-specific**: tools lived inside that framework.

---

### 2.2 Semantic Kernel: Skills → Plugins (2023 →)

**Ecosystem:** Microsoft Semantic Kernel (C#, Python, Java)  
**Concept names (evolving):**

- **Skills** and **functions** (early SK)
- Later unified as **Plugins**: a group of functions exposed to AI apps. :contentReference[oaicite:2]{index=2}:contentReference[oaicite:3]{index=3}  

Key ideas:

- Semantic (prompt-based) and native (code) functions grouped into plugins.
- LLMs can automatically call plugin functions via function calling.

Again, highly capable—but **tied to SK’s abstractions**.

---

### 2.3 Other Frameworks: LlamaIndex & AutoGen (2023 →)

**LlamaIndex**

- Uses **Tools** to wrap query engines, loaders, and pipelines (e.g., `OnDemandLoaderTool`).  
- Agents select these tools when answering questions over docs. :contentReference[oaicite:4]{index=4}  

**Microsoft AutoGen**

- Defines **tools** as functions/APIs that multi-agent systems call during their dialogues.
- Built around the idea of agents delegating work to specialized tools.

Each framework had its own flavor, but the pattern was the same:  
> *“Give the LLM some callable capabilities, and let it decide what to call.”*

---

## 3. OpenAI & Azure: First Widely-Standardized Tool Calling

### 3.1 OpenAI Function Calling → Tools (2023 →)

In **June 2023**, OpenAI introduced **function calling** for GPT-4 and GPT-3.5. :contentReference[oaicite:5]{index=5}:contentReference[oaicite:6]{index=6}  

- Developers describe **functions** via JSON schema.
- The model decides **if/when** to call them and returns structured arguments.
- Later this was generalized and renamed to **Tools**, with support for e.g. parallel calls and more flexible semantics. :contentReference[oaicite:7]{index=7}:contentReference[oaicite:8]{index=8}  

Conceptually, this is the **same idea** as LangChain/SK tools, but now directly built into the **model API**.

### 3.2 Azure OpenAI / Azure AI Foundry

Microsoft brought the same concepts into Azure:

- Azure OpenAI supports **function calling** with GPT-4 / GPT-3.5 models. :contentReference[oaicite:9]{index=9}  
- Azure AI Foundry Agents expose **tools** (functions, code interpreter, search grounding, etc.) that the agent can invoke server-side.

These are **cloud-platform implementations** of the tools idea, but still **tied to the OpenAI/Azure APIs**.

---

## 4. The Core Limitation: N×M Integrations

By late 2023 / early 2024, everyone had:

- **OpenAI tools / functions**
- **Azure tools**
- **LangChain tools**
- **SK plugins**
- **LlamaIndex tools**
- **AutoGen tools**
- …and so on.

But they were **not portable**:

- A “CSV validator tool” written for LangChain could not be reused directly by SK.
- Each new agent runtime meant writing another adapter.
- This is the classic **N×M problem**: *M tools* × *N runtimes* → lots of glue code.

The industry needed a **shared protocol**, not just more library-level abstractions.

---

## 5. MCP: Tools as a Cross-Ecosystem Protocol (Late 2024 →)

### 5.1 Anthropic’s Model Context Protocol (MCP)

Anthropic introduced **Model Context Protocol (MCP)** as an **open protocol** for connecting LLMs to tools and data. :contentReference[oaicite:11]{index=11}  

Key aspects:

- **Client–server architecture**:
  - **Host** (e.g., an IDE, agent runtime, or desktop app) runs the LLM.
  - **MCP servers** expose tools and data sources.
- **Standard message format** for:
  - Tool discovery (`tools/list`)
  - Tool invocation (`tools/call`)
  - Returning results and errors. :contentReference[oaicite:13]{index=13}  

In MCP, **Tools** are:

- Executable functions exposed over the protocol.
- Identified by name + schema.
- Invoked by clients on behalf of the model.

This turns tools into a **portable, wire-level concept** instead of a per-framework abstraction.

---

### 5.2 Why MCP Is Different from Earlier “Tools”

Earlier systems:

- Defined tools as **in-process objects** (or SDK constructs).
- Required tight coupling to a specific agent framework or cloud API.

MCP:

- Defines a **protocol** so that any host (Claude desktop, VS Code, agent frameworks, etc.) can use the **same MCP server**.
- Lets you write **one CSV validator MCP server**, then reuse it in multiple environments without rewriting adapters. :contentReference[oaicite:14]{index=14}:contentReference[oaicite:15]{index=15}  

This is analogous to the shift from:

- “Every DB has its own proprietary client” → to **SQL over TCP with standard drivers**.
- Or “every chat platform has a different bot API” → to something more universal.

---

## 6. Putting It All Together: Timeline Summary

Approximate timeline of “tool” concepts in the LLM ecosystem:

1. **Pre-2023** – APIs, webhooks, and classic bot integrations: tools exist, but humans/hard-code decide when to call them.
2. **Early 2023** – Framework abstractions:
   - **LangChain tools + agents** formalize the pattern in open-source. :contentReference[oaicite:16]{index=16}:contentReference[oaicite:17]{index=17}  
   - **Semantic Kernel skills/functions** provide a Microsoft-flavored version, later evolving into **plugins**. :contentReference[oaicite:19]{index=19}  
   - LlamaIndex, AutoGen, and others adopt similar “tool” constructs.
3. **Mid-2023** – **OpenAI function calling** (later “tools”) makes tools a first-class **API feature** in mainstream LLMs. :contentReference[oaicite:20]{index=20}  
4. **2023–2024** – Cloud & ecosystem integration:
   - Azure OpenAI / Azure AI Foundry adopt and extend tools (grounding, code, search, etc.). :contentReference[oaicite:21]{index=21}  
   - Frameworks build richer multi-agent stories around tools and function calling.
5. **Late 2024 → 2025** – **MCP** reframes tools as **portable protocol entities**:
   - Standardizes how tools are described, discovered, and invoked across hosts. :contentReference[oaicite:22]{index=22}:contentReference[oaicite:23]{index=23}  
   - Aimed at solving the N×M integration problem and encouraging reusable, secure tool servers.

---

## 7. Where We Are Now

Today, “tools” mean slightly different things depending on context:

- **Framework view (LangChain, SK, LlamaIndex, AutoGen)**  
  > Tools/Plugins/Skills = wrappers around functions/APIs in that framework.

- **Model API view (OpenAI, Azure, others)**  
  > Tools/Functions = JSON-schema-described actions models can call.

- **Protocol view (MCP)**  
  > Tools = standardized, protocol-level capabilities exposed by MCP servers and usable by any MCP-aware host.

Historically, **MCP doesn’t invent tools**, but it **elevates them**:

> From *“a pattern every framework reinvents”*  
> to *“a shared protocol that everyone can implement once and reuse everywhere.”*

---
