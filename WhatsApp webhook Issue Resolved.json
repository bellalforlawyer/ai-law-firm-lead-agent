## 🔧 Debugging Log: WhatsApp Cloud API Webhook Not Triggering (Solved after 1 week)

### Problem
Meta's own "Check test webhooks" panel (App Dashboard → API Setup) showed incoming
message events successfully — but the n8n Executions tab stayed completely empty.
Callback URL was verified, webhook fields (`messages`) were subscribed, and the
Production webhook URL matched exactly. Still, nothing reached n8n.

### Root Cause
Configuring the Callback URL and subscribing to webhook fields is **not enough**.
Meta requires a separate, explicit API call that subscribes your **App** to the
specific **WhatsApp Business Account (WABA)** you're sending/receiving on. Without
this step, Meta generates the event internally (which is why it shows in the test
panel) but never forwards it to your configured webhook.

### Fix
Run this once per WABA (get the WABA ID from Meta Business Settings → WhatsApp Accounts):

```bash
curl -X POST "https://graph.facebook.com/v25.0/{WABA-ID}/subscribed_apps" -H "Authorization: Bearer {ACCESS-TOKEN}"
```

**Note:** On Windows CMD, this must be run as a single line. Using `\` for line
continuation (like on Mac/Linux Terminal) breaks the command and produces a
misleading "bad hostname" error.

Expected response: `{"success":true}`

### Secondary Issue: Status Payloads Breaking the AI Agent
Once the webhook started firing, a second issue appeared: every real user message
triggered 3–4 executions, and all but the first threw `"No prompt specified"` in
the AI Agent node.

**Cause:** WhatsApp sends a webhook not just for incoming messages, but also for
every delivery status change (`sent`, `delivered`, `read`) on messages *you* send.
These status payloads contain a `statuses` field but no `messages` field — so any
expression like `{{ $json.messages[0].text.body }}` resolves to `undefined`.

**Fix:** Add an **IF node** between the WhatsApp Trigger and the AI Agent:
- Condition: `{{ $json.messages }}` → type **Array** → **exists**
- (or, more robustly: `{{ $json.messages ? true : false }}` → type **Boolean** → **is true**)

Only the `true` branch connects to the AI Agent. Status-update executions now hit
the `false` branch and stop silently — no errors, no wasted AI calls.

### Key Takeaway
Webhook "connected" ≠ webhook "subscribed to your app." And any messaging webhook
that fires on both inbound messages *and* delivery receipts needs an explicit
filter before hitting your AI logic — otherwise your agent will choke on payloads
it was never meant to process.