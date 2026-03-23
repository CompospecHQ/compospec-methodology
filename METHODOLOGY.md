<img width="1580" height="714" alt="image" src="https://github.com/user-attachments/assets/d57edb47-e347-4da7-9640-804db95b33cc" /># The Compospec Methodology: Deep Dive

**How structured context blocks enable specification intelligence**

---

<img width="1580" height="714" alt="image" src="https://github.com/user-attachments/assets/0def0bfb-6900-4280-9424-24ef10f5fb25" />

---

## Table of Contents

1. [The Structural Foundation](#1-the-structural-foundation)
2. [The Workflow](#2-the-workflow)
3. [AI-Ready Architecture](#3-ai-ready-architecture)
4. [Implementation: Compospec Platform](#4-implementation-compospec-platform)
5. [Comparison to Other Approaches](#5-comparison-to-other-approaches)

---

## 1. The Structural Foundation

### 1.1 Why Hierarchy?

UI components nest. Code modules nest. API endpoints nest. Requirements should too.

**The 6-Level System:**
```
📦 Product
  └─ 📂 Module
      └─ 🖥️ User Interface
          └─ 📐 Section
              └─ 🧩 Component
                  └─ ⚛️ Element
```

**Why this maps to development:**

| Level | Frontend Analog | Backend Analog | Architecture Analog |
|-------|----------------|----------------|---------------------|
| **Product** | Application root | Service mesh | System architecture |
| **Module** | Feature slice | Microservice | Bounded context |
| **User Interface** | Page/Route | API endpoint | Interface contract |
| **Section** | Layout region | Controller | Business logic layer |
| **Component** | React component | Method/Function | Domain object |
| **Element** | HTML element | Parameter | Data field |
```mermaid
graph TB
    subgraph "Frontend Mental Model"
    F1[App] --> F2[Feature]
    F2 --> F3[Page]
    F3 --> F4[Layout]
    F4 --> F5[Component]
    F5 --> F6[Element]
    end
    
    subgraph "Backend Mental Model"
    B1[Service Mesh] --> B2[Microservice]
    B2 --> B3[API Endpoint]
    B3 --> B4[Controller]
    B4 --> B5[Method]
    B5 --> B6[Parameter]
    end
    
    subgraph "Compospec Hierarchy"
    C1[Product] --> C2[Module]
    C2 --> C3[User Interface]
    C3 --> C4[Section]
    C4 --> C5[Component]
    C5 --> C6[Element]
    end
    
    F1 -.-> C1
    F2 -.-> C2
    F3 -.-> C3
    F4 -.-> C4
    F5 -.-> C5
    F6 -.-> C6
    
    B1 -.-> C1
    B2 -.-> C2
    B3 -.-> C3
    B4 -.-> C4
    B5 -.-> C5
    B6 -.-> C6
    
    style C1 fill:#7c3aed,stroke:#5b21b6,color:#fff
    style C2 fill:#8b5cf6,stroke:#6d28d9,color:#fff
    style C3 fill:#a78bfa,stroke:#7c3aed,color:#fff
    style C4 fill:#c4b5fd,stroke:#8b5cf6,color:#000
    style C5 fill:#ddd6fe,stroke:#a78bfa,color:#000
    style C6 fill:#ede9fe,stroke:#c4b5fd,color:#000
```

### 1.2 The Parent-Child Link

Every card (except Product) has **exactly one parent**. This single constraint creates:

#### 1. Dependency Graph
```mermaid
graph TD
    P[Product: E-commerce Platform]
    P --> M1[Module: Checkout]
    P --> M2[Module: User Profile]
    
    M1 --> U1[UI: Payment Page]
    M1 --> U2[UI: Order Confirmation]
    
    U1 --> S1[Section: Credit Card Form]
    U1 --> S2[Section: Order Summary]
    
    S1 --> C1[Component: CVV Input]
    S1 --> C2[Component: Expiry Date]
    
    C1 --> E1[Element: 3-Digit Field]
    C1 --> E2[Element: Validation Icon]
    
    style P fill:#7c3aed,stroke:#5b21b6,color:#fff
    style M1 fill:#8b5cf6,stroke:#6d28d9,color:#fff
    style U1 fill:#a78bfa,stroke:#7c3aed,color:#fff
    style S1 fill:#c4b5fd,stroke:#8b5cf6,color:#000
    style C1 fill:#ddd6fe,stroke:#a78bfa,color:#000
    style E1 fill:#ede9fe,stroke:#c4b5fd,color:#000
```

**Queryable relationships:**
- What depends on this card? (children)
- What does this card depend on? (ancestors)
- What's the full context path? (root to current)

#### 2. Context Inheritance

Child cards inherit constraints from parent chain:
```
Product: Mobile Banking App
  → Security: 2FA required
  → Compliance: GDPR, PCI-DSS
  
  Module: Money Transfer
    → Inherits: 2FA, GDPR, PCI-DSS
    → Adds: Transaction limits, anti-fraud checks
    
    UI: Transfer Dashboard
      → Inherits: All above
      → Adds: Accessibility AA standard
      
      Component: Amount Input
        → Inherits: All above
        → Implements: Masked input, decimal validation
```

When AI generates code for "Amount Input", it has the full context chain — not just the component spec.

#### 3. Traceability
```mermaid
graph LR
    A[Business Goal] --> B[Feature Requirement]
    B --> C[UI Spec]
    C --> D[Component Spec]
    D --> E[Implementation]
    
    E -.Traces back to.-> D
    D -.Traces back to.-> C
    C -.Traces back to.-> B
    B -.Traces back to.-> A
    
    style A fill:#7c3aed,stroke:#5b21b6,color:#fff
    style E fill:#10b981,stroke:#047857,color:#fff
```

"Why does this button exist?" → Follow parent chain to Product-level business goal.

---

## 2. The Workflow

### 2.1 Card Creation
```mermaid
sequenceDiagram
    actor User
    participant Modal as Card Editor
    participant System as Compospec
    
    User->>Modal: Click "+Create"
    Modal->>User: Show card type selector
    User->>Modal: Select "Component"
    Modal->>User: Show editor with typed fields
    User->>Modal: Write title + content
    User->>Modal: (Optional) Add attachments
    User->>Modal: Click "Create"
    Modal->>System: Save card
    System->>User: Card created (unlinked)
```

**Key insight:** Cards start **unlinked**. Linking is explicit, not automatic.

### 2.2 Linking Strategy
```mermaid
graph TB
    A[Unlinked Card] --> B{Click Footer Area}
    B --> C[Overlay: Parent Selector]
    C --> D{Drag-Drop or Click?}
    D -->|Drag| E[Drop on Parent Row]
    D -->|Click| F[Click Parent Row]
    E --> G[Confirm Link Dialog]
    F --> G
    G --> H{User Confirms?}
    H -->|Yes| I[Link Created]
    H -->|No| A
    I --> J[Flow Diagram Auto-Generated]
    
    style A fill:#f59e0b,stroke:#92400e,color:#fff
    style I fill:#10b981,stroke:#047857,color:#fff
    style J fill:#3b82f6,stroke:#1e40af,color:#fff
```

**Why explicit linking matters:**

- **Prevents orphan cards:** System flags unlinked cards
- **Flexible hierarchy:** Child cards can link to any parent level (Element → Product is valid)
- **Single parent rule:** Each card has exactly one parent, preventing ambiguity
- **Maintains graph integrity:** No circular dependencies

### 2.3 Flow Diagram Generation

When a link is created:
```mermaid
graph LR
    A[Link Created] --> B[Traverse Parent Chain]
    B --> C[Build Tree Structure]
    C --> D[Render as Nested Diagram]
    D --> E[Diagram Saved as Flow]
    
```

**Flow diagram = visual rendering of card relationships**

Not a separate artifact. Not manually drawn. Derived from data.

---

## 3. AI-Ready Architecture

### 3.1 Structured Context Blocks

Traditional spec:
```
"The login page should have a username field, password field, 
and a submit button. The username must be an email. 
Password should be masked. Show error if fields are empty."
```

Compospec equivalent:
```yaml
Card Type: User Interface
Title: Login Page
Parent: Authentication Module

Card Type: Component
Title: Username Input
Parent: Login Page
Constraints:
  - Type: email
  - Validation: required

Card Type: Component
Title: Password Input
Parent: Login Page
Constraints:
  - Display: masked
  - Validation: required

Card Type: Component
Title: Submit Button
Parent: Login Page
Behavior:
  - on_click: validate_and_submit
  - on_error: show_inline_error
```

**Why this matters for AI:**

| Traditional Spec | Compospec Card |
|-----------------|----------------|
| Unstructured prose | Typed fields |
| Implicit relationships | Explicit parent-child |
| Context scattered | Context inherited via chain |
| One-time snapshot | Version-controlled, persistent |

### 3.2 The "Prompt Debt" Problem
```mermaid
graph TB
    subgraph "Traditional Workflow"
    T1[BA writes 74-page Word doc] --> T2[Developer reads page 23]
    T2 --> T3[Developer asks AI to generate code]
    T3 --> T4[AI has no context from pages 1-22, 24-74]
    T4 --> T5[Hallucinated output]
    end
    
    subgraph "Compospec Workflow"
    C1[BA creates hierarchical cards] --> C2[Developer selects card]
    C2 --> C3[System provides card + parent chain]
    C3 --> C4[AI receives full structured context]
    C4 --> C5[Accurate output]
    end
    
    style T5 fill:#ef4444,stroke:#991b1b,color:#fff
    style C5 fill:#10b981,stroke:#047857,color:#fff
```

**Compospec eliminates prompt debt by:**

1. **Atomic context units:** Each card = one semantic scope
2. **Explicit dependencies:** Parent chain = complete context
3. **Persistent structure:** Cards update, context stays linked

### 3.3 Future: Cards as AI Prompts

*(Roadmap: Q2 2026)*
```mermaid
graph LR
    A[Component Card] --> B[Auto-Generated Questions]
    B --> C{Rules Defined?}
    C -->|No| D[Prompt BA for Rules]
    C -->|Yes| E[Generate State Machine]
    E --> F[Suggest IA Structure]
    F --> G[Populate Design Templates]
    
    style A fill:#7c3aed,stroke:#5b21b6,color:#fff
    style G fill:#10b981,stroke:#047857,color:#fff
```

Each card becomes a **queryable semantic unit** that AI agents can:

- Ask clarifying questions (BPMN-style rule extraction)
- Suggest information architecture
- Generate UI mockups from templates
- Validate implementation against spec

---

## 4. Implementation: Compospec Platform

### 4.1 Dashboard
```mermaid
graph LR
    A[Dashboard] --> B[Card Count by Type]
    A --> C[Flow Diagram Count]
    A --> D[Quick Actions]
    
    B --> B1[Product: 3]
    B --> B2[Module: 12]
    B --> B3[UI: 47]
    B --> B4[Section: 98]
    B --> B5[Component: 203]
    B --> B6[Element: 451]
    
    C --> C1[Total Flows: 47]
    C --> C2[Click to Navigate]
    
```

### 4.2 Card Management

**Filters:**

- **Group:** Linked cards in tree view
- **Date:** Cards created in date range
- **Type:** Stack view by card type
- **Unlinked:** Orphan cards flagged
```mermaid
graph TB
    A[All Cards] --> B{Apply Filter}
    B -->|Group| C[Linked Card Trees]
    B -->|Date| D[Timeline View]
    B -->|Type| E[Type Stack View]
    B -->|Unlinked| F[Orphan Cards Alert]
    
```

**Actions:**

- View card content
- Edit card
- Export as .md or .pdf
- Share link (public or private)
- Navigate to flow diagram

### 4.3 Flow Management
```mermaid
graph LR
    A[Flows Menu] --> B[Grid View of All Flows]
    B --> C{Select Flow}
    C --> D[Expand Top Parent Node]
    D --> E[Reveal Full Tree]
    E --> F{User Actions}
    F --> G[Zoom In/Out]
    F --> H[Export Flow]
    F --> I[Share Link]
    F --> J[Re-link Parent]


```

### 4.4 Templates

*(Coming Q1 2026)*

Industry-specific starter packs:

- **Fintech:** Payment flows, KYC, transaction histories
- **CRM:** Contact management, pipeline views, reporting
- **E-commerce:** Product catalog, cart, checkout, order tracking

Import templates → customize → link to your product structure.

### 4.5 Screenshot Annotation

*(Coming Q1 2026)*
```mermaid
sequenceDiagram
    actor User
    participant Browser as Screen Capture API
    participant System as Compospec
    
    User->>Browser: Trigger screenshot
    Browser->>User: Select region
    User->>Browser: Capture
    Browser->>System: Upload screenshot
    System->>User: Show annotation editor
    User->>System: Draw boxes + add notes
    User->>System: Auto-create cards from annotations
    System->>User: Cards linked to UI parent
```

Turn legacy UI screenshots into structured specs. No manual typing.

### 4.6 MCP Integration

*(Coming Q1 2026)*
```mermaid
graph TB
    A[Compospec MCP Server] --> B[Claude Desktop]
    A --> C[Cursor]
    A --> D[Figma MCP]
    A --> E[Code Connect]
    
    B --> F[Query Card Context]
    C --> F
    D --> F
    E --> F
    
    F --> G[Generate Code with Full Spec]
    

```

Direct protocol integration → AI tools query Compospec cards in real-time.

---

## 5. Comparison to Other Approaches

| Approach | Structure | AI-Ready | Developer-Friendly | Human-First | Auto-Flows |
|----------|-----------|----------|-------------------|-------------|-----------|
| **Word/Confluence** | ❌ Prose | ❌ No | ❌ No | ✅ Yes | ❌ No |
| **Notion/Linear** | ⚠️ Flat | ⚠️ Partial | ⚠️ Partial | ✅ Yes | ❌ No |
| **GitHub SpecKit** | ✅ YAML | ✅ Yes | ⚠️ Code-first | ❌ No | ❌ No |
| **AWS Kiro** | ✅ Structured | ✅ Yes | ⚠️ AWS-locked | ❌ No | ⚠️ Manual |
| **Figma/FigJam** | ❌ Canvas | ❌ No | ❌ No | ✅ Yes | ❌ No |
| **Compospec** | ✅ Hierarchy | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Auto |

### Why Compospec Wins

**vs Word/Confluence:**
- Structured vs prose → AI can parse
- Linked vs scattered → Context preserved
- Auto-flows vs static → Always synced

**vs GitHub SpecKit:**
- Human-first vs code-first → BA/PO can use it
- Hierarchy vs flat → Matches mental model
- Visual flows vs YAML → Stakeholder-friendly

**vs AWS Kiro:**
- Platform-agnostic vs AWS-locked
- Pre-development vs post-code → Upstream focus
- Human-writable vs generated → No AI dependency

**vs Figma/FigJam:**
- Structured vs freeform → Query-able
- Persistent vs ephemeral → Version-controlled
- Auto-flows vs manual → Effort-free maintenance

---

## Next Steps

**📖 See real-world examples:** [EXAMPLES.md →](./EXAMPLES.md)

**🔍 Detailed comparison:** [COMPARISON.md →](./COMPARISON.md)

**💻 Try Compospec Beta:** [app.compospec.com](https://app.compospec.com)

---

**Built with conviction in London 🇬🇧**
