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
