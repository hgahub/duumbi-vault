---
intake_id: "13f38963-750a-47b6-b807-7b64ae511eca"
intake_status: captured
source: grok
intake_owner: "hgahub"
intake_updated_at: "2026-09-12T20:37:36Z"
---
# Provider Models and Reasoning Effort Support

## Source
- Conversation: Duumbi Lead Grok Bot chat (2026-09-12); explicit  capture
- Evidence run referenced by submitter as live validation tied to "#780" (provider HTTP 400)

## Raw input
Duumbi does not support newer models or effort usage. Concrete failure:

"The live #780 validation used OPENAI_API_KEY with gpt-5.6-luna. DUUMBI sent the current OpenAI Chat Completions request with function tools and reasoning enabled. The provider returned HTTP 400 before graph generation:

Function tools with reasoning_effort are not supported for gpt-5.6-luna in /v1/chat/completions.
To use function tools, use /v1/responses or set reasoning_effort to 'none'."

Support available effort levels for:
- OpenAI: GPT-5.6 Luna, GPT-5.6 Terra, GPT-5.6 Sol, GPT-6 Astra
- Anthropic: Claude Sonnet 5, Claude Opus 5, Claude Haiku 4.5, Claude Fable 5.1
- xAI: Grok 4.6, Grok Build 0.1

## Problem
DUUMBI's OpenAI Chat Completions client can send function tools together with non-none . For models such as , the provider rejects that combination on  (HTTP 400) before graph generation. Newer catalog models and provider-native effort controls are therefore unusable or only usable if effort is forced off / the wrong API surface is used.

## Affected user
Owners and operators running live provider-backed intent/mutation/bench validation with current OpenAI (and planned Anthropic/xAI) models that expose reasoning effort.

## Desired outcome
DUUMBI can call the listed OpenAI, Anthropic, and xAI models with the effort levels those providers actually support, without HTTP 400 when tools and reasoning are both required—e.g. by using  (or equivalent) when tools + reasoning_effort are needed, or by mapping effort correctly per model/API.

## Out of scope
- User-facing session/intent  product UX already sketched in the older Effort Levels note (cost/team/verification levers), except where it must map onto provider  / model tier.
- Changing Stage 7/9 workflow gates or unrelated bench process-evidence work in GitHub #780 (HTTP/SQLite process evidence), unless that run is only the reproduction vehicle.
- Inventing unsupported effort values beyond what each provider documents.

## Interpreted intent
Make provider integrations (starting with OpenAI Chat Completions vs Responses) compatible with current reasoning-capable models and function/tool use, and extend model/effort support matrices for the named OpenAI, Anthropic, and xAI models so live validation and authoring can use them.

## Classification
feature / bug (provider API compatibility) / execution

## Related context
- Related (not duplicate):  — user-facing effort/cost levers, not Chat Completions vs Responses + tools.
- Related (not duplicate):  — catalog/routing advisor; does not fix the HTTP 400 tools+reasoning_effort path.
- GitHub #780 () is a closed bench process-evidence issue; submitter cited a live validation using that label/context with  — treat as reproduction evidence, not as an exact duplicate ticket.
- Not inspected: full  OpenAI client implementation beyond the reported error text.

## Open questions
- Should OpenAI tool+reasoning calls move primarily to , or stay on Chat Completions with  when tools are present (degraded mode)?
- Exact supported effort enum per listed model (provider docs) for OpenAI / Anthropic / xAI?
- Ship OpenAI path first, then Anthropic and xAI in the same change set, or phased?

## Requested follow-up
Stage 3b prepare → Stage 4 triage for execution (provider client + model catalog / effort mapping). Do not implement from this capture alone.

## Notes
- Facts: Provider error explicitly forbids function tools with  on  via ; suggests  or .
- Assumptions: Listed model names are the desired near-term catalog set; "effort" here means provider reasoning effort as well as usable model selection.
- Recommendations: Prefer Responses (or provider-correct surface) when tools and reasoning are both required; keep a documented matrix of model → allowed effort → API surface; link but do not merge with the older Effort Levels product note without triage.
