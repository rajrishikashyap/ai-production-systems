# 🎯 AI Lead Generation Machine — Technical Overview

> **Status:** Live in production · **Built:** Apr 2026 · **Company:** Geekynd Pvt Ltd
> Code is proprietary. This document covers architecture, decisions, and learnings.

---

## The Problem

Finding and reaching potential clients manually was the team's biggest time sink:
- Scraping leads from platforms like Clutch, LinkedIn, and directories: hours per week
- Verifying contact info, enriching company data: more hours
- Writing personalised outreach emails: more time, inconsistent quality
- Managing email warmup to avoid spam folders: specialised knowledge most teams don't have

The goal: a system that does all of this autonomously, 24/7, with no human in the loop for routine prospecting.

---

## Architecture

```
MyClaw Agent (runs 24/7)
        ↓
Apollo ← Lead Scraping (Clutch, directories, etc.)
        ↓
Claude API ← Lead Enrichment + Qualification
        ↓
Airtable ← Central Database + CRM Interface
        ↓
Instantly ← Email Warmup + Outreach Sequencing
        ↑
Virtual Mailbox + Custom Domain ← Email Infrastructure
```

---

## Tech Stack & Why Each Tool

| Tool | Role | Why This Choice |
|---|---|---|
| **MyClaw** | Main AI agent — runs the pipeline 24/7 | Persistent agent that orchestrates the full workflow autonomously |
| **Apollo** | Lead scraping and contact data | Structured access to company + contact data across industries |
| **Claude API (Anthropic)** | Lead enrichment, qualification, personalisation | Best-in-class reasoning for nuanced qualification decisions and personalised copy |
| **Airtable** | Central database + live CRM interface | Non-technical team can see pipeline status, update records, manage follow-ups |
| **Instantly** | Email warmup + outreach sequencing | Manages sending reputation, warmup schedules, and multi-step sequences |
| **Virtual Mailbox** | Physical address for domain credibility | Improves email deliverability and domain trust scores |
| **Custom Domain** | Outreach email infrastructure | Dedicated sending domain to protect main domain reputation |
| **Google Cloud Run** | Deployment | Serverless, scales with volume, cost-efficient |
| **Docker** | Containerisation | Consistent environment, easy redeployment |

---

## How the Pipeline Works

**Step 1 — Scrape**
Apollo scrapes target companies from Clutch and similar directories based on defined criteria (industry, size, location, tech stack). Raw lead data flows into Airtable.

**Step 2 — Enrich + Qualify**
Claude API processes each lead: enriches company context, scores qualification based on ideal customer profile criteria, and flags low-quality leads for exclusion. Qualified leads are tagged and moved to the outreach queue in Airtable.

**Step 3 — Personalise**
Claude API generates personalised first-line copy for each outreach email — referencing the company's specific work, recent projects, or positioning. Generic blasts have poor reply rates; personalisation at scale is the core value of this step.

**Step 4 — Outreach**
Instantly sends warmed-up emails through the custom domain, managing sending limits, follow-up sequences, and reply detection. Virtual mailbox and domain setup ensure high deliverability and credibility.

**Step 5 — Log + Track**
All outcomes — sent, opened, replied, bounced — sync back to Airtable. The team has a live view of pipeline health without touching any of the underlying tools.

---

## Email Infrastructure — Why It Matters

Most teams underestimate email deliverability. Key decisions:

**Separate sending domain:** Never send cold outreach from your main company domain. A single spam complaint can blacklist your entire domain. Dedicated sending domain = isolated risk.

**Warmup before sending:** Instantly's warmup feature sends and receives emails between real inboxes to build sender reputation before outreach begins. Skipping this = landing in spam.

**Virtual mailbox:** Adds a physical address to email footers and domain registration — a trust signal for spam filters and recipients.

---

## What Surprised Me

**Qualification logic is the bottleneck, not scraping.** Apollo can pull thousands of leads. The hard problem is deciding which ones are worth reaching. Claude's reasoning ability — evaluating a company's fit against nuanced criteria — was what made the qualification step actually useful rather than just filtering by keyword.

**Personalisation at scale requires constraints.** Claude generating completely freeform personalisation produced inconsistent quality. I had to design a structured prompt with explicit boundaries — what to reference, what tone to use, maximum length — before the output was reliable enough for production.

**Airtable as the source of truth.** Every tool in the stack has its own dashboard. Having Airtable as the single interface the team actually uses — with automated views, filtered pipelines, and status tracking — was what made the system usable by non-technical people.

---

## Results

- 150+ qualified leads/week generated autonomously
- ~70% reduction in manual prospecting time
- Personalised outreach at scale with consistent quality
- Full pipeline visibility in Airtable for the whole team
- System runs 24/7 with zero manual intervention for routine prospecting

---

## What I'd Do Differently

- Add a reply classification step — Claude reading replies and categorising them (interested / not now / unsubscribe) to trigger automated follow-up logic
- Build a scoring model on top of historical reply data to improve qualification criteria over time
- Add A/B testing on subject lines and first-line copy through Instantly to continuously improve reply rates

---

> Part of the [Production AI Systems](../README.md) showcase
> **Rajrishi Kashyap** · raj01rishi@gmail.com
