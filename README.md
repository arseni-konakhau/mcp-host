# MCP HOST

## High-Level Application Architecture Summary

### Core Components Breakdown

1. **Business Client (Web Application)**
   - Thin web UI for user interaction
   - **DAG Executor (UI execution tool)** - executes DAG locally in browser
   - Visual DAG renderer and progress display
   - Configuration manager for LLM providers
   - DAG sync client (keeps UI DAG in sync with backend)

2. **MCP-Host (Local Runtime)**
   - Tool registry and management
   - MCP client orchestration (1:1 connections to MCP servers)
   - Security and permission layer
   - Local tool execution coordination
   - Interface to external LLM providers

3. **Server Layer (Backend Services)**
   - Main LLM Provider (configurable: cloud/local)
   - Mini-LLM for semantic processing (tool matching, intent analysis)
   - **DAG Engine (structure generation and mutation)** - backend orchestration
   - MCP Registry (trusted server catalog)
   - DAG Storage (database for DAG persistence and caching)

### Component Relationships & Communication

```
User Input
    ↓
Business Client (Web App)
├── UI Layer (displays DAG, progress, results)
├── DAG Executor (browser-based execution)
│   ├── Local DAG Runner
│   ├── Progress Tracker
│   └── Sync Client (real-time sync with backend)
├── MCP-Host (local runtime)
│   ├── Tool Registry (maps MCP server capabilities)
│   ├── Client Manager (connects to MCP servers)
│   └── Security Layer (permission validation)
└── Mini-Orchestrator (coordinates UI ↔ Backend)
    ↓
Server Layer (Backend)
├── DAG Engine (generates/mutates DAG)
│   ├── Task Decomposition (calls LLM)
│   ├── Tool Matching (calls mini-LLM)
│   ├── Graph Builder/Mutator
│   └── Execution Coordinator
├── LLM Provider (semantic processing)
├── Mini-LLM (tool matching)
├── MCP Registry (server catalog)
├── DAG Storage (persistence & caching)
│   ├── DAG Repository
│   ├── Execution History
│   └── Sync Service
└── Sync API (WebSocket/REST)
    ↓
MCP Servers (external tools)
```

### Component Communication Flow

**🎯 High-Level Data Flow (Updated for Hybrid Architecture):**

```
User Input
     │
     ▼
Business Client (Web App)
├── UI Layer (request input)
│   │
│   ▼
├── DAG Sync Client → Sync API → Backend DAG Engine
│   │
│   │  (request text + context)
│   ▼
│   Backend DAG Engine → LLM Provider (semantic processing)
│   │
│   │  generates task decomposition
│   ▼
│   Backend DAG Engine → Mini-LLM (tool matching)
│   │
│   │  matches tools to tasks
│   ▼
│   Backend DAG Engine
│   │
│   │  builds DAG structure
│   ▼
│   DAG Storage (persistence & caching)
│   │
│   │  saves DAG + execution history
│   ▼
│   Sync API (WebSocket) → Client Sync Client
│   │
│   │  synchronizes DAG to UI
│   ▼
├── DAG Executor (browser-based execution)
│   │
│   │  executes DAG locally
│   ▼
├── Mini-Orchestrator (coordinates with backend)
│   │
│   │  manages execution coordination
│   ▼
├── MCP-Host (local runtime)
│   │
│   │  orchestrates tool calls
│   ▼
├── MCP Servers (external tools)
│   │
│   │  execute actual tools
│   ▼
│   Results → Sync API → UI Layer (real-time updates)
│
└── Server Layer (Backend)
    ├── DAG Engine (generation & mutation)
    ├── LLM Provider (semantic processing)
    ├── Mini-LLM (tool matching)
    ├── MCP Registry (server catalog)
    └── DAG Storage (database)
```

### Independent Development Units

You can build these as separate modules:

**Client-Side Units (Web App):**
- **DAG Executor module** (browser-based DAG execution and progress tracking)
- DAG Sync Client (real-time sync with backend DAG state)
- MCP-Host module (tool orchestration)
- UI Rendering Engine (DAG visualization)
- Local Config Manager (user preferences)
- Mini-Orchestrator Runtime (UI ↔ Backend coordination)

**Server-Side Units (Backend):**
- **DAG Engine module** (graph generation, mutation, backend orchestration)
- LLM Provider Interface (pluggable models for semantics)
- Semantic Processor (mini-LLM wrapper for tool matching)
- MCP Registry Service (server catalog)
- DAG Storage Service (persistence, caching, history)
- Sync API Service (WebSocket/REST for client sync)

**Communication Protocols:**
- JSON-RPC/WebSocket for client-server comms
- MCP protocol for Host-to-Server tool calls
- GraphQL/JSON for DAG structures
- Event streaming for real-time updates

### Key Integration Points

1. **DAG Generation Flow:** UI → Backend DAG Engine → LLM Provider → DAG Structure → DAG Storage → Sync to UI
2. **DAG Execution Flow:** UI DAG Executor → Sync Client → Backend DAG Engine → Mini-Orchestrator → MCP-Host → MCP Servers → Results Sync → UI
3. **Tool Discovery:** MCP Registry → MCP-Host → Tool Registry → Backend DAG Engine → Sync to UI
4. **DAG Sync Flow:** Backend DAG Storage ↔ Sync API ↔ Client Sync Client (WebSocket for real-time updates)
5. **Mutation Flow:** UI Request → Backend DAG Engine → LLM Provider → Graph Mutations → DAG Storage → Sync to UI

### Architecture Principles

This architecture implements a **hybrid DAG approach** with backend intelligence and UI execution, ensuring **real-time synchronization** between server-side DAG generation and client-side execution. The DAG Engine serves as the backend "agent brain" for complex orchestration, while the DAG Executor provides responsive UI execution. DAG Storage enables persistence, caching, and history tracking, with WebSocket-based sync maintaining consistency across distributed components. LLM providers remain external for semantic processing, maximizing privacy while enabling powerful workflow automation.
