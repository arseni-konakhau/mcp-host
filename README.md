# MCP HOST

## High-Level Application Architecture Summary

### Core Components Breakdown

1. **Business Client (User Interface Layer)**
   - Thin UI application for user interaction
   - Contains MCP-Host as local runtime
   - Lightweight orchestration (DAG execution without ML)
   - Visual DAG renderer and progress display
   - Configuration manager for LLM providers

2. **MCP-Host (Local Runtime)**
   - Tool registry and management
   - MCP client orchestration (1:1 connections to MCP servers)
   - Security and permission layer
   - Local tool execution coordination
   - Interface to external LLM providers

3. **Server Layer (External Services)**
   - Main LLM Provider (configurable: cloud/local)
   - Mini-LLM for semantic processing (tool matching, intent analysis)
   - DAG Engine (structure generation and mutation)
   - MCP Registry (trusted server catalog)

### Component Relationships & Communication

```
User Input
    ↓
Business Client
├── UI Layer (displays DAG, progress, results)
├── MCP-Host (local runtime)
│   ├── Tool Registry (maps MCP server capabilities)
│   ├── Client Manager (connects to MCP servers)
│   └── Security Layer (permission validation)
└── Mini-Orchestrator (executes DAG nodes)
    ↓
Server Layer
├── LLM Provider (interprets requests, generates plans)
├── Mini-LLM (semantic matching, tool selection)
└── DAG Engine (builds/mutates graph structures)
    ↓
MCP Servers (external tools: file ops, APIs, databases, etc.)
```

### Independent Development Units

You can build these as separate modules:

**Client-Side Units:**
- MCP-Host module (tool orchestration)
- UI Rendering Engine (DAG visualization)
- Local Config Manager (user preferences)
- Mini-Orchestrator Runtime (DAG execution)

**Server-Side Units:**
- LLM Provider Interface (pluggable models)
- Semantic Processor (mini-LLM wrapper)
- DAG Builder Service (graph construction)
- MCP Registry Service (server catalog)

**Communication Protocols:**
- JSON-RPC/WebSocket for client-server comms
- MCP protocol for Host-to-Server tool calls
- GraphQL/JSON for DAG structures
- Event streaming for real-time updates

### Key Integration Points

1. **User Input Flow:** UI → Mini-Orchestrator → LLM Provider → DAG Engine → Mini-Orchestrator → MCP-Host → MCP Servers
2. **Tool Discovery:** MCP Registry → MCP-Host → Tool Registry (semantic indexing)
3. **Execution Flow:** DAG Engine → Mini-Orchestrator → MCP-Host → Results → UI
4. **Mutation Flow:** LLM Provider → DAG Engine → Diff Operations → Mini-Orchestrator

### Architecture Principles

This architecture ensures the heavy LLM components are external, while keeping the core orchestration local for performance and privacy. The MCP-Host acts as a secure bridge between user requests and external tools, with DAG-based task orchestration providing flexible, parallel execution of complex workflows.
