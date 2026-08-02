---
name: unifonic-verify-otp
description: Verify a user with a one-time passcode over SMS, WhatsApp, or voice using the Unifonic Authenticate API.
api: openapi/unifonic-authenticate-openapi-original.json
operations: [CreateVerification, CheckVerification]
generated: '2026-07-21'
method: generated
---

# Verify a user with an OTP (Unifonic Authenticate)

## Auth
Send both headers on every request: `x-authenticate-app-id` (your Authenticate app id) and `Authorization` (bearer auth token). Both come from the Unifonic console's Authenticate app configuration. See `authentication/unifonic-authentication.yml`.

## Steps
1. **Start the verification** — `POST https://authenticate.cloud.api.unifonic.com/services/api/v2/verifications/start` (operationId `CreateVerification`) with form field `to` (E.164, e.g. `+966512345678`). Optional: `channel` (`sms` | `whatsapp` | `voice` — defaults to the channel priority configured in the Authenticate app), `locale` (`en`/`ar`), `length` (code length). The response is a `Verification` object with a uuid `id`.
2. **Collect the code from the user.**
3. **Check the code** — `POST /services/api/v2/verifications/check` (operationId `CheckVerification`) with `to`, `channel`, and `code`.
4. **Branch on the result** — `response_status` is `correct` or `incorrect`; `error_code` gives the reason: 101 correct, 107 incorrect, 108 attempts exceeded, 109 code expired, 110 already verified.

## Rules
- Treat 108 (attempts exceeded) and 109 (code expired) as terminal — start a new verification rather than re-checking.
- 110 (already verified) means the flow already succeeded; do not resend.
- Bodies are `application/x-www-form-urlencoded`, responses JSON.
