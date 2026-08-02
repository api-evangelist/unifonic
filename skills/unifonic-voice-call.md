---
name: unifonic-voice-call
description: Place an outbound voice call with the Unifonic Voice API and track its status.
api: openapi/unifonic-voice-openapi-original.json
operations: [CreateCall, retrieveStatusOfCall, createsWebhook]
generated: '2026-07-21'
method: generated
---

# Place a voice call and track it (Unifonic Voice)

## Auth
The Voice API uses a custom `AppsId` credential from the Unifonic Console's Voice Applications tab (the published OpenAPI declares no securitySchemes). See `authentication/unifonic-authentication.yml`.

## Steps
1. **(Optional) Register a status webhook** — `POST https://voice.unifonic.com/v1/providers/webhook` (operationId `createsWebhook`) so call-status events are pushed to your HTTPS endpoint.
2. **Create the call** — `POST https://voice.unifonic.com/v1/calls` (operationId `CreateCall`). The Voice API supports TTS messages, playing audio, collecting responses, OTP-by-voice, and conference bridges (see the docs recipes under docs.unifonic.com api-documentation).
3. **Track status** — `GET /v1/providers/voice-call-log/{callId}` (operationId `retrieveStatusOfCall`) with the `callId` returned by the create step, or consume the webhook events.

## Related flows
- **Number masking** — two-way: `createTwoWayNumberMaskingSession` (`POST /providers/masks`); one-way: `createOneWayNumberMaskingSession` (`POST /providers/config/masks`); inspect with `retrieveSpecificMaskingSession` / `retrieveAllMaskingSessions`; end with `deactivatesNumberMaskingSession` (`DELETE /providers/{maskId}`).
- **Call queues** — `getSpecificQueue` (`GET /providers/queues/{queueName}`), `dequeueFIFO` (`POST /providers/queues/{queueName}/dequeue`).

## Rules
- Responses are JSON; use HTTPS only.
- Map failures against `errors/unifonic-problem-types.yml`.
