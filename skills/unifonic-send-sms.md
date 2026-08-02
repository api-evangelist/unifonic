---
name: unifonic-send-sms
description: Send an SMS with the Unifonic SMS (NextGen) API and confirm its delivery status.
api: openapi/unifonic-sms-openapi-original.json
operations: [Sendmessage, GetMessageDetails]
generated: '2026-07-21'
method: generated
---

# Send an SMS and confirm delivery (Unifonic)

## Auth
Every SMS API request requires the `AppSid` query parameter (your application credential, provisioned by Unifonic support / the cloud.unifonic.com console). The spec also declares HTTP basic auth globally. Use HTTPS only. See `authentication/unifonic-authentication.yml`.

## Steps
1. **Send the message** — `POST https://el.cloud.unifonic.com/rest/SMS/messages` (operationId `Sendmessage`) with `AppSid`, `SenderID`, `Recipient` (international format without `00` or `+`, e.g. `9665xxxxxxxx`), and `Body`. Optionally set `CorrelationID` (unique per message — a repeat raises 409 "This message is duplicate") and `statusCallback` (HTTPS URL to receive the delivery-status webhook).
2. **Check the envelope** — a success returns `{"success": "true", "errorCode": "ER-00", ...}`. Any other `errorCode` or a non-200 status is a failure; map the status against `errors/unifonic-problem-types.yml` (e.g. 401 auth failed, 440 wrong sender format, 480 SenderID not allowed for this user).
3. **Confirm delivery** — either receive the DLR webhook on your `statusCallback` URL, or poll `POST /rest/SMS/Messages/GetMessagesDetails` (operationId `GetMessageDetails`) filtered by `MessageID` / `dateFrom` / `dateTo` / `Limit`.

## Rules
- Message body must be GSM7 or UCS2 (error 460); long bodies raise 412.
- Scheduling instead of immediate send: use `SendScheduledMessages` with `TimeScheduled` (`yyyy-mm-dd HH:mm:ss`, future — error 451); stop with `StopScheduledMessages`.
- 429 means too many requests for this user — back off before retrying.
- There is no documented idempotency contract; use `CorrelationID` for duplicate detection.
