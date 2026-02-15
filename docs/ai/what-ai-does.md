# AI in Maysano

Maysano uses an LLM-powered assistant to support teams in creating, understanding, and governing data products aligned with the ODPS standard. The assistant combines conversational guidance with structured automation capabilities.

---

## What AI Does

The AI supports multiple workflows across the platform:

### Assistant Chat
Users can interact conversationally with the AI to:
- Clarify governance concepts
- Understand ODPS structures
- Explore best practices
- Get guidance on building data products

The assistant has contextual knowledge of ODPS and platform patterns to provide structured and consistent guidance.

---

### ODPS Specification Generation
Users can describe a data product in natural language, and the AI generates a structured YAML specification aligned with the ODPS standard.

Generated specifications:
- Follow structural templates
- Include required fields
- Respect validation rules
- Are designed for further refinement by users

---

### Specification Validation
The AI can review existing specifications and:
- Identify missing fields
- Detect structural inconsistencies
- Highlight potential governance gaps
- Suggest improvements

The goal is not replacement of governance review, but acceleration and support.

---

### Governance Component Suggestions
Based on the type and context of a data product, the AI can recommend:
- SLA profiles
- Data quality profiles
- Access profiles
- Reusable governance components

Suggestions are aligned with reusable governance patterns.

---

### Strategy Support
The AI helps teams articulate:
- Product objectives
- Outcome KPIs
- Internal KPIs
- Positioning and value propositions

This supports alignment between governance, product, and business roles.

---

### Schema Assistance (Optional Integrations)
When integrated with metadata or catalog systems, the AI can:
- Propose data schemas
- Assist with schema mapping
- Support column matching tasks

---

### Executive Summaries & Insights
The AI can generate:
- Executive summaries
- Product overviews
- Structured insights
- Proactive analytical commentary

These outputs are designed for stakeholder communication and decision support.

---

## How It Works (High-Level)

At a high level, Maysano uses an orchestration layer that:

1. Receives a user request.
2. Determines whether additional context is required.
3. Optionally calls internal tools to fetch or update data.
4. Produces a structured response streamed back to the user interface.

The system supports tool-enabled reasoning, allowing the assistant to both read and write within defined governance boundaries.

---

## Safety & Guardrails

To improve reliability and predictability, Maysano applies multiple layers of controls:

- **Input constraints** to prevent oversized or malformed requests
- **Timeout limits** to prevent stalled responses
- **Token and cost budgets** per feature
- **Failure handling mechanisms** to avoid repeated retries during upstream degradation
- **Observability metrics** for usage, latency, and cost tracking

AI suggestions are designed to support decision-making.  
Final accountability always remains with human users.

---

## Architecture Overview

```mermaid
flowchart TD
  subgraph FE["Frontend"]
    U[User] --> UI[AI Assistant UI]
    U --> Builder[Product Builder]
    U --> Validator[Specification Validator]
    U --> Mapper[Schema & Mapping (optional)]
  end

  subgraph API["Backend API"]
    UI --> A1["AI Orchestrator (streaming)"]
    Builder --> A2["Generate spec / strategy"]
    Validator --> A3["Validate specification"]
    Mapper --> A4["Schema helper / mapping"]
  end

  subgraph ORCH["AI Orchestration Layer"]
    A1 --> Loop[Tool-enabled reasoning loop]
    A2 --> Loop
    A3 --> Loop
    A4 --> Loop
  end

  subgraph SAFE["Safety & Guardrails"]
    Loop --> IV[Input validation]
    IV --> TB[Token & cost budgets]
    TB --> TO[Timeouts]
    TO --> FH[Failure handling]
  end

  subgraph LLM["LLM Provider"]
    FH --> GW[LLM gateway / provider]
    GW --> M[LLM model]
  end

  subgraph KB["Knowledge & Rules"]
    R[ODPS reference + templates + validation rules]
    R -.-> Loop
  end

  subgraph TOOLS["Internal Tools"]
    direction LR
    Read["Read context:\nproducts\nprofiles\nversions\nartifacts"]
    Write["Write actions:\ncreate/update\nprofiles\nartifacts"]
  end

  Loop --> Read
  Loop --> Write
  Read --> DB[(Data Store)]
  Write --> DB

  subgraph OBS["Observability"]
    MET[Usage, cost, latency metrics]
    Loop -.-> MET
  end
