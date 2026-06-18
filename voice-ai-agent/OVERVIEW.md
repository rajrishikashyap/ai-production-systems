#  Voice AI Virtual Assistant - Technical Overview

> **Status:** Live in production · **Built:** Apr 2026 · **Company:** Geekynd Pvt Ltd
> Code is proprietary. This document covers architecture, decisions, and learnings.

---

## The Problem

Clients calling in for appointment bookings had to speak to a human every time. This meant:
- No coverage outside business hours
- Human time wasted on repetitive, resolvable queries
- No structured logging of call outcomes
- Inconsistent responses depending on who picked up

The goal: an AI that picks up every call, resolves what it can, and only escalates when it genuinely needs to.

---

## Architecture

```
Incoming Call (Twilio)
        ↓
    VAPI (Voice Agent Platform)
        ↓
DeepGram API ← Speech-to-Text (STT)
        ↓
Groq LLM ← Intent Detection + Response Generation
        ↓
Google Cloud TTS ← Text-to-Speech
        ↓
   ┌────┴────┐
   │         │
Resolved   Not Resolved
   │         │
Airtable   Call Forwarding → Human Agent
(log outcome)
```

---

## Tech Stack & Why Each Tool

| Tool | Role | Why This Choice |
|---|---|---|
| **VAPI** | Voice agent orchestration | Handles real-time audio streaming, manages call flow, connects all components |
| **Groq LLM** | Intent detection + response generation | Fastest inference available, critical for <2s latency target |
| **DeepGram** | Speech-to-text | Low latency, high accuracy on conversational speech |
| **Google Cloud TTS** | Text-to-speech | Natural-sounding voice output, easy API integration |
| **Twilio** | Phone number + call routing | Industry standard, reliable call infrastructure |
| **Airtable** | Database + operations interface | Non-technical team can view/manage call logs without a dashboard build |
| **Node.js** | Backend server | Handles webhooks, business logic, Airtable API calls |
| **Google Cloud Run** | Deployment | Serverless, scales to zero when idle, cost-efficient |
| **Docker** | Containerisation | Consistent environment across dev and production |

---

## Prompt Engineering for Voice

Voice prompts are fundamentally different from text prompts. Key principles I applied:

**1. Short sentences only.** Long LLM responses sound unnatural when spoken. Prompts explicitly constrained response length.

**2. Explicit escalation criteria.** The AI needed clear rules for when to forward, not a vague "if you can't help." Specific triggers: unrecognised request type, user says "speak to a human", third failed attempt at resolution.

**3. Conversational fillers.** Added brief acknowledgement phrases ("Got it", "One moment") to mask processing time and feel more natural.

**4. Structured output.** The LLM response included a JSON payload alongside the spoken text to intent classification, resolution status, and extracted data (e.g. appointment date/time) for Airtable logging.

---

## What Surprised Me

**Latency compounds.** STT + LLM inference + TTS = three sequential latency sources. Each one had to be optimised individually. Groq was the single biggest improvement, switching from a standard API to Groq cut LLM inference time by ~60%.

**Silence detection is hard.** The agent needed to know when the user had finished speaking. Too aggressive = interrupts mid-sentence. Too slow = awkward pauses. VAPI's built-in silence detection needed tuning for the specific call context.

**Prompt failures are invisible.** When a text chatbot fails, you see the bad output. When a voice agent fails, the user just hangs up. Airtable logging every call with full transcript was essential for debugging, without it, failures were silent.

---

## Results

- 30+ calls/day handled autonomously
- <2s average response latency
- Call forwarding triggered on <20% of calls
- 100% of call outcomes logged with full transcript to Airtable
- Zero missed calls during business hours

---

## What I'd Do Differently

- Add a confidence score threshold before escalating, currently rule-based, could be smarter
- Stream TTS output rather than waiting for full generation — would cut perceived latency further
- Build a monitoring dashboard on top of Airtable data rather than raw table views

---

> Part of the [Production AI Systems](../README.md) showcase
> **Rajrishi Kashyap** · raj01rishi@gmail.com
