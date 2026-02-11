# Technical Specification: FDA 510(k) Agentic Reviewer v3.0
**Project Name:** FDA Agentic Regulatory Suite
**Version:** 3.0 (The "Fashion & Intelligence" Update)
**Date:** October 27, 2023
**Status:** Live / Architecture Frozen

---

## 1. Executive Summary

The **FDA 510(k) Agentic Reviewer** is a state-of-the-art web application designed to transform the regulatory submission review process. It combines the rigorous data processing capabilities of the **Google Gemini API (Models 2.5 & 3.0)** with a bespoke, high-fashion aesthetic user interface.

Unlike traditional enterprise tools, this application acknowledges the "human in the loop" by providing a cognitively ergonomic and visually stimulating environment. It features a proprietary "Jackslot" theming engine, dynamic AI agent generation, and strategic regulatory intelligence tools.

---

## 2. Design System & UX Architecture

### 2.1. The "Fashion-First" Philosophy
The application rejects standard Bootstrap/Material design patterns in favor of a **Variable-Injection Theming Engine**. 
*   **Core Concept:** The UI adapts to the user's emotional state or aesthetic preference to reduce fatigue during long review sessions.
*   **Implementation:** `App.tsx` and `Sidebar.tsx` control a set of CSS Custom Properties (`--color-primary`, `--font-secondary`, etc.) defined in `constants.ts`.

### 2.2. The "Jackslot" Mechanism
A gamified style switcher located in the Sidebar.
*   **Function:** Rapidly cycles through 20 distinct high-fashion themes (e.g., 'Orange Box', 'Red Sole', 'Cyberpunk') before settling on a random choice.
*   **Technical:** Updates the root React state `settings.style`, which triggers a `useEffect` to repaint the DOM instantly without reloading.

### 2.3. Layout Structure
*   **Glassmorphism Sidebar:** Fixed left navigation with backdrop blur filters (`backdrop-filter: blur(12px)`) to blend with the dynamic background blobs.
*   **Ambient Background:** Two large, animated color blobs (`brand-primary` and `brand-accent`) float behind the main content area, providing subtle depth that shifts with the active theme.

---

## 3. Functional Modules

### 3.1. Dashboard
*   **Purpose:** Operational analytics.
*   **Visualization:** Uses `Recharts` to display token consumption and agent activity.
*   **Theming:** Charts inherit CSS variables, ensuring data visualization colors match the active fashion theme automatically.

### 3.2. 510(k) Intelligence (New)
*   **Purpose:** Strategic regulatory planning.
*   **Input:** Natural language device description (e.g., "AI-powered radiology triage software").
*   **Output:** Structured JSON containing:
    *   Product Code & Regulation Number.
    *   Review Panel.
    *   Common Predicates.
    *   Consensus Standards (ISO/ASTM).
    *   Testing Requirements (Bench/Clinical).
*   **AI Model:** `gemini-2.5-flash-latest` (optimized for speed and structured retrieval).

### 3.3. Summary Studio
*   **Purpose:** Deep analysis of specific 510(k) summary texts.
*   **Features:**
    *   **Parsing:** Extracts unstructured text into a strict `K510Summary` TypeScript interface.
    *   **Visualization:** Renders "KPI Cards" for device class and risk factors.
    *   **Contextual Chat:** A RAG-lite implementation where the parsed summary is injected into the system prompt, allowing users to "chat" with the document.

### 3.4. Dynamic Agent Factory (New)
*   **Purpose:** On-demand generation of specialized AI personas.
*   **Workflow:**
    1.  User describes a role (e.g., "A harsh toxicology reviewer").
    2.  Gemini generates a full `AgentConfig` object, including a complex System Prompt, Temperature setting, and Model selection.
    3.  The new Agent is added to the global `agents` state and becomes available for task execution.

### 3.5. Agents Configuration (New)
*   **Purpose:** Fleet management for AI Agents.
*   **Features:**
    *   **Editor:** Modify system prompts, temperature, and categories of existing agents.
    *   **Test Environment:** A sandbox chat interface to verify an agent's persona before deploying them to real tasks.
    *   **Persistence:** Currently managed in React State (ephemeral).

### 3.6. Orchestrator
*   **Purpose:** Project management.
*   **Function:** Generates a step-by-step Markdown plan for a 510(k) submission based on high-level inputs.

### 3.7. Utility Tools
*   **PDF → Markdown:** Cleans OCR text into semantic markdown.
*   **Entity Extraction:** Identifies Organizations, Dates, Standards, and Anatomical Sites.
*   **Comparator:** Analyzes Subject vs. Predicate devices for substantial equivalence.
*   **Checklist:** Generates a gap analysis report with a "Compliance Score".
*   **Note Keeper:** An AI-assisted text editor with "Magic" buttons (Fix Grammar, Make Professional).

---

## 4. Technical Architecture

### 4.1. Tech Stack
*   **Runtime:** Browser-native ES Modules (No-Build capable).
*   **Frontend Framework:** React 19.
*   **Styling:** Tailwind CSS (via CDN) + CSS Variables.
*   **AI SDK:** `@google/genai` (v1.40.0).
*   **Icons:** `lucide-react`.
*   **Animation:** `framer-motion`.

### 4.2. Service Layer (`geminiService.ts`)
The application creates a clean abstraction over the Gemini API using distinct patterns:

1.  **Structured Extraction Pattern:**
    *   Uses `responseSchema` and `responseMimeType: "application/json"`.
    *   Used in: `parse510kSummary`, `extractEntities`, `getRegulatoryIntelligence`.

2.  **Agentic Chat Pattern:**
    *   Uses system instructions to enforce personas.
    *   Used in: `runAgent`, `DynamicAgents`, `AgentsConfig`.

3.  **Creative Generation Pattern:**
    *   Uses higher temperature settings for brainstorming.
    *   Used in: `generateOrchestrationPlan`, `createAgentFromDescription`.

### 4.3. Data Models (`types.ts`)
Strict TypeScript interfaces ensure type safety between the AI output and the UI rendering components.
*   `K510Summary`: Deeply nested object representing an FDA summary.
*   `AgentConfig`: Configuration object for dynamic personas.
*   `RegulatoryIntelligence`: Strategic data structure.

---

## 5. Security & Compliance (Non-Functional Requirements)

*   **API Key Security:** The API key is stored in React state memory only. It is never persisted to `localStorage` or sent to a third-party backend (other than Google's API endpoint).
*   **Data Privacy:** The application is client-side only. Data flows directly from Browser → Google Gemini. No intermediate server logging exists.
*   **Permissions:** `metadata.json` requests zero hardware permissions (camera/mic), ensuring a low attack surface.

---

## 6. Future Roadmap (v4.0 Considerations)

1.  **Multi-Modal PDF Analysis:** Integration of Gemini 1.5 Pro's native PDF ingestion to replace text-paste workflows.
2.  **Persistent Database:** Integration with Supabase or Firebase to save Agent Fleets and Chat History.
3.  **Real-Time Collaboration:** Websocket layer to allow multiple Regulatory Affairs users to edit the same 510(k) plan simultaneously.
4.  **FDA Database Integration:** Direct API calls to `open.fda.gov` to validate Product Codes and Predicate K-numbers hallucinated by the LLM.
