# multiagent_protocols_framework_survey
Exploring modern multi-agent protocols: MCP, ACP, and A2A

## Overview

This repository provides a comprehensive survey of modern multi-agent protocols, focusing on:

- **Model Context Protocol (MCP)**
- **Agent Communication Protocol (ACP)**
- **Agent-to-Agent (A2A) Protocol**

The goal is to analyze their architectures, use cases, and performance in various multi-agent systems.

### Why?
- Multi-agent systems are increasingly prevalent in AI applications, requiring robust protocols for effective communication and coordination.
- Understanding these protocols is crucial for developing scalable and efficient multi-agent systems.
- Interoperability (the ability of distinct agents and systems to discover capabilities, exchange context, and coordinate actions seamlessly) is essential for modular, reusable, and resilient multi-agent workflows.
- Standardized protocols reduce development overhead, improve security, and enable cross-platform collaboration. 


---

## Protocols

### 1. MCP
- open-source standard for connecting AI applications to external systems.
- A JSON-RPC client–server interface for secure context ingestion and structured tool invocation.
- MCP streamline the integration of large language models (LLMs) with external data sources and tools.
- MCP addresses the challenges of fragmented and custom-built integrations by providing a universal, model-agnostic interface for AI systems to access and interact with diverse resources.
- Using MCP, AI applications like Claude or ChatGPT can connect to 
  - **data sources** (e.g. local files, databases)
  - **tools** (e.g. search engines, calculators)
  - **workflows** (e.g. specialized prompts)—enabling them to access key information and perform tasks.

#### Architecture
- MCP follows a client-server architecture
- **Participants:**
  - **MCP host**: An AI application (e.g., Claude, ChatGPT or Your custom application)
  - **MCP server**: External systems (e.g., databases, search engines, tools)
  - **MCP client**: A component within the MCP host that manages communication with MCP servers.
- **MCP Host:**
  - serves as the **initiator** of interactions in the MCP ecosystem
  - Responsible for managing connections to one or more MCP Servers and orchestrating communication workflows in accordance with protocol specifications
  - The client initializes sessions, requests and processes the four core primitives Resources, Tools, Prompts, and Sampling, and handles asynchronous notifications related to server-side events.
- **MCP Server:**
  - functions as the provider of data, services, and interaction templates that the client can utilize to enrich LLM-based workflows.
  - It exposes and manages 
    - contextual Resources
    - executes external operations via Tools
    - defines reusable Prompts for consistent interaction patterns
    - delegates text-generation tasks through Sampling.
- **Core Components:**
  - **Resources**: Structured data objects that provide context to the LLM, such as documents, database records. They provide the LLM with tailored, task-specific inputs and enable context-aware completions.
  - **Tools**: External functions or services that the LLM can invoke to perform specific tasks, such as web searches, calculations, or data retrieval. This facilitates seamless integration with third-party systems and streamlines access to
real-world data and operations.
  - **Prompts**: User-controlled templates defined by the server but selected by end-users through the client interface. These reusable prompts promote consistency, reduce redundancy, and support repeatable interaction patterns.
  - **Sampling**: Server-controlled and allows the MCP Server to delegate the task of generating LLM completions to the client. This supports sophisticated agentic workflows and enables fine-grained oversight over the model’s generative process.


### 2. ACP
- ACP is a general-purpose protocol for agent communication that uses RESTful HTTP interfaces to support MIME-typed multipart messages and both synchronous and asynchronous interactions.
- This REST-based communication model enables lightweight, runtime-independent agent invocation, making ACP well-suited for scalable system integration.
- ACP includes structured session management, message routing, and a flexible authentication model that integrates with role-based access control (RBAC) and decentralized identity (DID) systems.
- It is compatible with a wide range of agent frameworks and deployment models, from lightweight stateless utilities to long-running, stateful services.
- ACP also supports agent discovery through runtime APIs, offline packaging, and manifest-based metadata

#### Architecture
- Modular, HTTP-based system designed to facilitate interaction between clients and agents.
- **Primary components:**
  - **ACP Client:** Initiates communication by submitting requests in ACP-compliant format. Supports message composition using ordered message parts, session-based interactions for multi-turn workflows, and both synchronous and streaming execution modes.  Error responses follow a unified structure, and standard HTTP authentication methods such as Bearer tokens, Basic Auth, and JWTs are supported
  - **ACP Server:** Acts as a middleware component, translating external HTTP requests into internal agent executions.The server is stateless by default, making it compatible with load balancers and orchestration platforms such as Kubernetes.Secure communication is achieved via TLS, and observability is supported through OpenTelemetry-based tracing and metrics.
  - **ACP Agents:** The execution component defined using a decorator-based configuration. Agents process structured requests consisting of ordered message parts and generate responses that conform to ACP’s message format.They support both stateless and session-aware operation, including features such as await/resume for interactive use cases.

### 3. A2A
- Enable secure, structured, and interoperable collaboration between AI agents across platforms, vendors, and environments.
- A2A is designed to support peer-to-peer agent interactions using capability-based representations known as Agent Cards, which describe what an agent can do and how it can be securely invoked.
- A2A supports asynchronous, event-driven communication through HTTP and Server-Sent Events (SSE), making it suitable for distributed, scalable agent ecosystems.

#### Architecture
- Agent-to-Agent (A2A) architecture facilitates communication and collaboration between distinct agentic systems to accomplish tasks.
- **Primary Actors:**
  - **User**: initiates a task or request, typically without needing to understand or directly interact with the underlying agentic systems.
  - **Client Agent**: receives this request, analyzes its intent, and identifies a suitable Remote Agent(Server) by inspecting the advertised capabilities through its Agent Card.
  - **Server (Remote) Agent**: Another autonomous entity that provides services, data, or functionalities to client agents. the Client Agent engages the Remote Agent to execute the task, coordinating message exchanges and retrieving results—termed Artifacts—which are then delivered back to the User.
  
  ---

- **"User" Interaction Models:**
  - Direct end-users: interact with the client agent via chat or voice, submitting tasks and seeing results immediately.
  - Indirect end-users: rely on higher-level products (dashboards, orchestration tools) that quietly delegate their requests to A2A agents.
  - Systems or services as users: back-end components invoke A2A autonomously for continuous jobs like monitoring or data transforms.
  - Agents as users: higher-tier agents hand tasks to downstream agents, enabling hierarchical workflows.
  - *Takeaway: A2A stays neutral about who or what the “user” is; its priority is delivering a consistent handshake between client and remote agents across all these scenarios.*
  
- **"Client Agent" Flow:**
  1. Agent discovery – collect and eval Agent Cards that describe Remote Agents skills, I/O specs, auth needs; choose the best remote agent for the user request.
  2. Task initiation – build a structured task object with user intent, metadata, and formatted inputs; dispatch it via a protocol-compliant message.
  3. Message & artifact exchange – keep bidirectional dialogue alive, send refinements, receive artifacts and interim status updates.
  4. Session context upkeep – track identifiers so long or stateful exchanges stay grouped under one workflow.
  5. Error handling – interpret remote failures, retry, pivot to another agent, or alert the user when needed.
  6. Result presentation – shape returned artifacts into user-ready output and surface them in the surrounding app/UI.
  7. Asynchronous channels (optional) – stream progress over SSE or subscribe to push notifications when the platform supports it.
  *Role – the client agent orchestrates execution, translates data, and bridges communications, turning user intent into coordinated A2A interactions.*

- **Remote Agent:**
  - the service endpoint that executes tasks delegated by the Client Agent.
  - It provides one or more Skills, which represent discrete operations it can perform ranging from simple data retrieval to complex computations or orchestrations involving external APIs or databases.
  - To make these capabilities discoverable, the Remote Agent publishes an Agent Card—a structured metadata document that includes a list of available skills, usage instructions, input/output formats, supported protocols, and authentication requirements.
  - Remote Agent must also manage its internal Resource Usage, ensuring fair allocation of compute, memory, network, and storage resources during task execution
  - It is responsible for enforcing Security and Access Control mechanisms like authenticating Clients, verifying message integrity, and authorizing access to specific skills based on access policies or token scopes.

- **Core Components:**
  - **Agent Cards:** a self-description and discovery mechanism. It is a JSON-formatted document that publicly declares the agent’s metadata, including its name, version, description, supported skills, and authentication requirements. Client Agents rely on Agent Cards to discover and evaluate Remote Agents that can fulfill specific task criteria.
  - **Skills:** represent the actionable capabilities offered by an agent.Each skill is described by a name, purpose, expected input parameters, and output format.Skills are invoked via Tasks and encapsulate the core utility the agent provides.
  - **Tasks:**  It specifies the skill to be executed, along with input parameters and contextual metadata.Tasks are issued by Client Agents and processed by Remote Agents, enabling asynchronous or synchronous collaboration.By structuring intention and invocation in a standardized format, Tasks allow A2A agents to operate interoperably across diverse systems.
  - **Messages:** serve as the primary communication channel between agents.These encapsulate data exchange and coordination activities such as task submission, intermediate status updates, or artifact delivery.
  - **Artifacts:** the outputs or results produced by Remote Agents in response to Tasks.Artifacts can take various forms, including structured data (JSON, XML), binary files (images, documents), or simple text messages.Artifacts are returned to Client Agents for further processing or presentation to end-users.

  
---

## References

- *A Survey of Agent Interoperability Protocols: Model Context Protocol (MCP), Agent Communication Protocol (ACP), Agent-to-Agent Protocol (A2A), and Agent Network Protocol (ANP)*