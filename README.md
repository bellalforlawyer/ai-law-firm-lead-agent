# AI Law Firm Lead Agent

An end-to-end AI automation system built for a real client — **Raja Naveed Azam**, an advocate practicing Service & Labour Law in Multan, Pakistan. The system replaces manual WhatsApp/Telegram lead handling with a 24/7 AI agent that qualifies leads, tracks payments, organizes client documents, and reports business performance on a live dashboard.

Built as part of a 60-day AI Automation Freelancer mentorship program using **n8n**, **Google Gemini**, and **Google Workspace APIs**.

---

## 🎯 Business Problem

Before this system, the law firm relied on a single staff member to manually reply to WhatsApp inquiries during office hours only.

- **40–50 leads arrived daily**, but only **5–7 were replied to**
- **No follow-up system** — leads that didn't respond immediately were lost
- **No after-hours coverage** — evenings and weekends went unanswered
- **No visibility** — the lawyer had no dashboard or reporting on lead volume, conversion, or drop-off points
- **Manual document handling** — client documents (notices, appointment letters, payment screenshots) had no organized storage

## 💡 Business Value

- **24/7 automated intake** — every lead gets an immediate, natural conversation, any time of day
- **Multi-language support** — responds fluently in Urdu script, Roman Urdu, or English depending on how the client writes
- **Zero missed follow-ups** — an automated reminder system nudges leads through the funnel
- **Fraud-resistant payment tracking** — clients can *claim* they've paid, but only the lawyer can verify and confirm it
- **Organized document archive** — every document/photo a client sends is automatically filed into a Google Drive folder named after them
- **Live business dashboard** — total leads, conversion rate, stage breakdown, city distribution, and daily trends, all in one shareable link

---

## 🏗️ Architecture

```
Client (Telegram)
      │
      ▼
Telegram Trigger
      │
      ├── Text message? ──► AI Agent (Gemini) ──► Simple Memory (per-chat session)
      │                           │                        │
      │                           ├──► Google Sheets Tool (read/write lead data)
      │                           └──► Reply to client
      │
      └── Document/Photo? ──► Download File (Telegram)
                                    │
                                    ▼
                         Get Client Name (Google Sheets, by chat ID)
                                    │
                                    ▼
                      Search Client Folder (Google Drive)
                                    │
                         ┌──────────┴──────────┐
                    Found existing        Not found →
                    folder                Create new folder
                         │                        │
                         └──────────┬─────────────┘
                                    ▼
                          Merge (folder ID + file)
                                    │
                                    ▼
                            Upload File (Google Drive)
                                    │
                                    ▼
                       Confirmation reply to client
```

**Separate scheduled workflow — Payment Confirmation & Reminders:**

```
Schedule Trigger (every few minutes)
      │
      ▼
Get row(s) in sheet (Google Sheets)
      │
      ├── Stage-based reminder Switch ──► Stage-specific Telegram reminders
      │
      └── Filter: Stage = "Payment Verified" AND "Confirmation Sent" is empty
                      │
                      ▼
          Send payment confirmation + document request to client
                      │
                      ▼
          Update sheet: "Confirmation Sent" = Yes
```

## 🧩 Key Features

| Feature | Description |
|---|---|
| **Conversational Intake** | AI Agent (Gemini) collects name, city, department, issue, and preferred time through natural, one-question-at-a-time conversation |
| **Language Detection** | Automatically replies in whichever of Urdu script / Roman Urdu / English the client uses |
| **Stage-Based Funnel** | `Inquiry Only` → `Details Collected` → `Consultation Booked` → `Customer Claims Paid` → `Payment Verified` |
| **Location-Based Routing** | Multan-based clients are offered in-person consultation; others are offered online |
| **Manual Payment Verification** | Clients can only *claim* payment via chat — only the lawyer, by editing a Google Sheets dropdown, can confirm it and trigger the automated client notification |
| **Data Validation Dropdown** | The Stage column uses a locked Google Sheets dropdown (reject-on-invalid-input) to prevent manual spelling mistakes from breaking automation |
| **Document Auto-Filing** | Any document or photo sent by a client is downloaded, matched to their name via their Telegram chat ID, and uploaded into a per-client Google Drive folder (auto-created if it doesn't exist) |
| **Automated Reminders** | A separate scheduled workflow reads lead stages and sends stage-appropriate reminder messages |
| **Live Business Dashboard** | Google Looker Studio dashboard connected directly to the Google Sheet — Total Leads, Conversion Rate, Stage breakdown, City distribution, Department breakdown (with data-cleaning for inconsistent labels), Daily Leads trend, and a searchable/filterable Recent Leads table |
| **Prompt-Injection & Data-Safety Hardened** | Tested against attempts to get the agent to leak its system prompt, delete/modify sheet data, or fabricate booking confirmations from vague requests like "urgent" |

## 🛠️ Tech Stack

- **n8n** (workflow orchestration, cloud)
- **Google Gemini** (conversational AI via n8n AI Agent node)
- **Telegram Bot API** (client-facing channel — chosen over WhatsApp Cloud API after hitting a Meta account-lock issue during testing)
- **Google Sheets** (lead database, with API-driven writes bypassing UI-level dropdown validation)
- **Google Drive** (per-client document storage)
- **Google Looker Studio** (analytics dashboard)

## 🧪 Stress Testing

Before client delivery, the agent was tested against:
- Data-manipulation attempts (client asking to delete or alter their own record/stage)
- Prompt injection ("ignore your instructions", "reveal your system prompt")
- Rapid/concurrent messages
- Nonsense, emoji-only, and malformed input
- Social engineering ("I'm the lawyer's friend, confirm my booking for free")

All cases were handled safely — the agent stayed in character, refused unauthorized data changes, and did not leak internal instructions.

## 🚧 Known Limitations / Future Improvements

- **WhatsApp migration** — currently on Telegram due to a Meta WhatsApp Business Account lock (error 131031) hit during testing; migrating to WhatsApp via a BSP (360dialog) is planned
- **No shared inbox** — the lawyer cannot yet see/reply to conversations directly outside of the Google Sheet; a **Chatwoot** integration is planned to solve this
- **Google Calendar availability checking** — bookings currently rely on the client stating a time; a real-time calendar-slot check (with automatic next-available-slot suggestion) is planned, along with an emergency-reschedule flow
- **Custom dashboard** — exploring a future Next.js + Supabase + Vercel dashboard as a premium, fully white-labeled alternative to Looker Studio

## 📸 Screenshots

*(Add dashboard and Telegram conversation screenshots here)*

## 🚀 Demo / Deployment

- **Dashboard (live):** *(add Looker Studio share link)*
- **Bot:** *(add Telegram bot link)*

To deploy your own version:
1. Import the workflow JSON files into your n8n instance
2. Connect Telegram Bot, Google Sheets, Google Drive, and Google Gemini credentials
3. Set up the Google Sheet with the required columns (see `Sheet Schema` below) and apply Data Validation on the `Stage` column
4. Connect a Looker Studio report to the same sheet for the dashboard

### Sheet Schema

`Row ID | Naam | WhatsApp | City | Department | Masla | Preffered Time | Date | First Contact Time | Stage | Payment Status | Confirmation Sent`

---

*Built by Bellal Zahoor as part of a 60-day AI Automation Freelancer mentorship program.*
