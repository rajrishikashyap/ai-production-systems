#  Production AI Systems - Built at Geekynd Pvt Ltd

> These systems are live in production. Code is confidential (proprietary to Geekynd Pvt Ltd) but architecture, decisions, and learnings are documented here.
>
> Built and deployed by **Rajrishi Kashyap** - AI Implementation Associate @ Geekynd Pvt Ltd (Apr 2026 – Present)

---

## System 1: Voice AI Virtual Assistant

**The problem:** Clients were calling in for appointment bookings and general queries. A human had to be available 24/7 to pick up, respond, and log outcomes - slow, expensive, error-prone.

**The solution:** An AI voice agent that picks up every call, handles the conversation autonomously, logs outcomes to a database, and only forwards to a human when the AI cannot resolve the request.

**Stack:** `VAPI` · `Groq LLM` · `DeepGram` · `Google Cloud TTS` · `Twilio` · `Airtable` · `Node.js` · `Google Cloud Run` · `Docker`

 **30+ calls/day handled autonomously**
 **<2s end-to-end response latency**
 **Smart call forwarding to human agents when needed**
 **Every call outcome logged to Airtable automatically**

[Read the full technical breakdown →](./voice-ai-agent/OVERVIEW.md)

---

## System 2: AI Lead Generation Machine

**The problem:** Finding and reaching potential clients manually, scraping websites, verifying contacts, warming up email accounts, writing personalised outreach was taking the team dozens of hours per week with inconsistent results.

**The solution:** A fully autonomous AI pipeline that runs 24/7: scrapes leads from platforms like Clutch, enriches and qualifies them using AI, then sends warmed-up outreach emails automatically, all without human intervention.

**Stack:** `MyClaw` · `Apollo` · `Claude API (Anthropic)` · `Airtable` · `Instantly` · `Virtual Mailbox` · `Custom Domain` · `Google Cloud Run` · `Docker`

 **150+ qualified leads/week generated autonomously**
 **Warmed-up email infrastructure for high deliverability**
 **Runs 24/7 with zero manual prospecting**
 **~70% reduction in manual sales operations**

[Read the full technical breakdown →](./lead-gen-machine/OVERVIEW.md)

---

## What I Learned Across Both Systems

Building these two systems back to back taught me things no tutorial covers:

**On Voice AI:** Latency is everything. Users tolerate a 1.5s pause in human conversation, they don't tolerate 3s from an AI. Every architectural decision (Groq for fast LLM inference, DeepGram for low-latency STT) was driven by that constraint. Prompt engineering for voice is also fundamentally different from text that you're writing for ears, not eyes.

**On Autonomous Pipelines:** The hardest part isn't building the pipeline. It's making it reliable at 3AM when nobody is watching. Error handling, retry logic, and Airtable as a live audit trail were what separated a demo from a production system.

**On both:** The real skill is translating a messy business problem ("we spend too much time on calls / finding clients") into a precise technical architecture. That translation, not the code, is where most of the value is created.

---

>  **Rajrishi Kashyap** · raj01rishi@gmail.com · [LinkedIn]([https://linkedin.com/in/rajrishi](https://www.linkedin.com/in/rajrishikashyap/)) · [GitHub](https://github.com/rajrishikashyap)
