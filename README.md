# AI Law Firm Lead Agent

An AI-powered conversational agent that handles client intake for a law firm via Telegram (with WhatsApp Business Cloud API integration also implemented). Built for **Raja Naveed Azam**, a Service & Labour Law expert whose target audience is Pakistani government employees.

## Business Problem
Law firms lose potential clients due to slow response times, missed follow-ups, and no systematic way to track inquiries from first contact to paid consultation. Manual intake also means no visibility into where leads drop off.

## Solution
A conversational AI agent that:
- Naturally gathers client details (issue, department, city, name, phone) one question at a time — no robotic forms
- Recommends in-person consultation for Multan-based clients, online consultation for others
- Encourages a paid consultation (Rs. 3,000) without being pushy
- Shares payment details and tracks payment status
- Remembers conversation history (no repeated questions)
- Automatically logs every lead into Google Sheets with a live "Stage" (Inquiry Only → Details Collected → Consultation Booked → Payment Pending/Claimed)
- Runs a scheduled follow-up workflow that reminds leads stuck at any stage, including unpaid consultations

## Architecture

**Main Conversational Workflow:**
Telegram/WhatsApp Trigger → IF (text message check) → AI Agent (Gemini + Memory + Google Sheets Tool) → Send Reply


**Follow-Up Workflow (runs on a schedule):**

Schedule Trigger → Google Sheets (Read All Leads) → Switch (by Stage) →
Stage-specific reminder messages sent via Telegram/WhatsApp


## Tech Stack
- n8n (workflow automation)
- Google Gemini AI (via AI Agent node)
- n8n Simple Memory (conversation context per user)
- Google Sheets (lead database + stage tracking)
- Telegram Bot API (primary channel for development/testing)
- WhatsApp Business Cloud API (Meta) — implemented, pending account verification

## Key Features
- **Natural, sequential questioning** — one question per message, never a form-style dump
- **Location-aware consultation routing** (in-person vs online)
- **Stage-based lead tracking** for full funnel visibility
- **Automated payment reminders** for stuck leads
- **Manual payment verification** — the AI never marks a payment as verified; it only flags "customer claims paid," leaving final confirmation to the business owner (fraud-prevention by design)
- **Multi-language support** — responds in Urdu script, Roman Urdu, or English depending on how the client writes

## Known Limitations / Notes
- WhatsApp Business Cloud API integration was fully built and tested, but delivery was blocked mid-testing by a Meta-side "Business Account locked" error (code 131031) — a Meta account review issue, not a workflow/technical error.
- Image/voice messages are not yet processed (text-only for now); the bot responds with a polite fallback if a non-text message is received.
- Currently business-owner has no dedicated inbox to view/reply to conversations directly — a future iteration will add Chatwoot (self-hosted) as a proper shared inbox layer.

## Future Improvements
- Chatwoot integration for a full business-owner-facing inbox
- Voice note transcription (Speech-to-Text) support
- Payment screenshot analysis (image understanding)
- Migration to a paid, dedicated WhatsApp BSP (e.g. 360dialog) for production reliability
