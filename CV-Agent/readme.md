# Cloud-Native Conversational AI: Executive CV Agent

## Executive Summary
This repository details the architecture, configuration parameters, and operational guardrails of a cloud-native Conversational AI agent deployed on the ElevenLabs infrastructure. Designed to function as an interactive professional surrogate, the system processes inquiries regarding career trajectory, technical expertise in IT leadership, and contact center systems management. It leverages Retrieval-Augmented Generation (RAG) against a deterministic knowledge base to ensure zero-hallucination responses.

## System Architecture & Core Components

The pipeline integrates low-latency Speech-to-Text (ASR), a specialized Large Language Model (LLM), and high-fidelity Text-to-Speech (TTS) synthesis.

| Component | Specification | Strategic Function |
| :--- | :--- | :--- |
| **LLM Engine** | `qwen36-35b-a3b` | Orchestrates conversational logic strictly within the boundaries of the ingested document. Temperature locked at `0` for absolute output predictability. |
| **Voice Synthesis** | `eleven_v4_turbo` | Ensures highly expressive, low-latency audio streaming (Optimization Level: 3) critical for mimicking natural human cadence. |
| **RAG Pipeline** | `e5_mistral_7b_instruct` | Executes semantic vector search across `Ferhat Ozdag - 2026 v4.pdf` with a rigid distance threshold of `0.6`. |
| **ASR Input** | `scribe_realtime` | Processes 16kHz PCM audio, optimized for dynamic user interruption handling. |

## Operational Guardrails & Compliance

To mitigate reputational risk and ensure corporate alignment, the agent operates under strict platform-level constraints defined in the JSON configuration.

* **Boundary Enforcement:** The system prompt explicitly restricts responses to the documented knowledge base. Ambiguous or out-of-scope inquiries are met with a standardized, professional refusal.
* **Content Moderation:** Automated guardrails are active across multiple vectors (Violence, Harassment, Profanity, Religion/Politics, Medical/Legal). Any breach of the 'medium' threshold triggers an immediate `end_call` termination to protect brand integrity.
* **Data Privacy:** Voice recording (`record_voice: true`) is enabled for analytic retention, requiring explicit user consent via the frontend widget before initialization. 

## Strategic Risk Assessment

Deploying a fully managed SaaS AI voice architecture introduces specific structural and operational dependencies that must be continuously monitored.

* **Vendor Lock-in & SLA Dependencies:** The monolithic reliance on the ElevenLabs ecosystem for ASR, LLM routing, and TTS synthesis limits infrastructure agility. A shift in vendor API tiering or an unexpected outage directly impacts widget availability.
* **Latency and Protocol Variability:** End-to-end response time is highly susceptible to the user's local network jitter and routing to edge nodes. For potential future scale into enterprise PBX or SIP trunking for IVN systems, this cloud-dependency requires rigorous latency benchmarking (< 800ms).
* **Data Sovereignty:** Processing user interactions and PII through a third-party cloud necessitates ongoing compliance audits to align with regional data protection regulations.

## Deployment Integration

The agent (`agent_7001m491ma8dfqhsmcnmzcweg2hx`) is configured for seamless web embedding via a localized shadow DOM widget, bypassing the need for complex frontend state management.

```html
<!-- Integration Payload -->
<elevenlabs-convai agent-id="agent_7001m491ma8dfqhsmcnmzcweg2hx"></elevenlabs-convai>
<script src="[https://elevenlabs.io/convai-widget/index.js](https://elevenlabs.io/convai-widget/index.js)" async type="text/javascript"></script>