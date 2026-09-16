# Evidence pack: #817 from job v1-817-5655972051 attempt 2

- Created: 2026-09-16T20:32:19.697019+00:00
- Prior decision: `5655972051` (do not continue this decision)
- Prior job/attempt: `v1-817-5655972051` / 2
- Research date: 2026-09-16
- Research outcome: interactive

## Binding note for renewed Spec

- This pack is the durable research evidence handoff for a **new** Stage 5 Accept / new decision ID.
- Prior Stage 7 approval is **historical evidence only**; run fresh Stage 7 and Stage 9 after the new Accept.
- Do not treat the old VM job directory alone as sufficient; use this pack (URLs + retrieval dates below).

## Official sources (URL + retrieved_on)

- `2026-09-16` — https://developers.openai.com/api/docs/models/gpt-5.6-sol
- `2026-09-16` — https://developers.openai.com/api/docs/models/gpt-5.6-terra
- `2026-09-16` — https://developers.openai.com/api/docs/models/gpt-5.6-luna
- `2026-09-16` — https://developers.openai.com/api/docs/guides/latest-model?model=gpt-5.6
- `2026-09-16` — https://developers.openai.com/api/docs/models/gpt-6-astra
- `2026-09-16` — https://developers.openai.com/api/docs/guides/latest-model
- `2026-09-16` — https://platform.claude.com/docs/en/models/overview
- `2026-09-16` — https://platform.claude.com/docs/en/models/fable-5-1/overview
- `2026-09-16` — https://platform.claude.com/docs/en/models/opus-5/overview
- `2026-09-16` — https://platform.claude.com/docs/en/models/sonnet-5/overview
- `2026-09-16` — https://platform.claude.com/docs/en/models/haiku-4-5/overview
- `2026-09-16` — https://platform.claude.com/docs/en/build-with-claude/effort
- `2026-09-16` — https://platform.claude.com/docs/en/build-with-claude/thinking-troubleshooting
- `2026-09-16` — https://docs.x.ai/developers/models/grok-4.6
- `2026-09-16` — https://docs.x.ai/developers/model-capabilities/text/reasoning
- `2026-09-16` — https://docs.x.ai/developers/models/grok-build-0.1
- `2026-09-16` — https://docs.x.ai/developers/release-notes
- `2026-09-16` — https://docs.x.ai/developers/model-capabilities/text/comparison
- `2026-09-16` — https://docs.x.ai/developers/tools/function-calling

## Research summary

Research date: 2026-09-16. Classification: V = verified support, N = documented non-support, U = unverified in the checked official public documentation.

1. GPT-5.6 Sol
- ID/lifecycle: V `gpt-5.6-sol`; current API-listed flagship model. No retirement date is announced.
- Effort: V `none`, `low`, `medium`, `high`, `xhigh`, `max`; omission defaults to `medium`; explicit `none` is supported.
- Tools/surface: V function calling, streaming, and non-tool generation. Although both Chat Completions and Responses are listed, OpenAI instructs reasoning/tool workflows to use `/v1/responses`.
- Mapping/response: V Responses `reasoning.effort`; Chat Completions `reasoning_effort`. Responses returns typed output items and supports streaming.
- Prerequisites: OpenAI API key and paid usage tier; Free is not supported for this model.

2. GPT-5.6 Terra
- ID/lifecycle: V `gpt-5.6-terra`; current API-listed model. No retirement date is announced.
- Effort: V `none`, `low`, `medium`, `high`, `xhigh`, `max`; omission defaults to `medium`; explicit `none` is supported.
- Tools/surface, mapping, response, and prerequisites: same verified contract as GPT-5.6 Sol; use Responses for reasoning plus tool calling.

3. GPT-5.6 Luna
- ID/lifecycle: V `gpt-5.6-luna`; current API-listed model. No retirement date is announced.
- Effort: V `none`, `low`, `medium`, `high`, `xhigh`, `max`; omission defaults to `medium`; explicit `none` is supported.
- Tools/surface, mapping, response, and prerequisites: same verified contract as the other GPT-5.6 variants. This resolves the reported incompatibility by selecting Responses for tools plus reasoning rather than degrading effort.

4. GPT-6 Astra
- ID/lifecycle: V `gpt-6-astra`; currently rolling out through the OpenAI API. No retirement date is announced.
- Effort: V `low`, `medium`, `high`, `xhigh`, `max`; N explicit `none`.
- Omitted effort: U. The checked Astra pages do not state the effective effort when `reasoning.effort` is omitted.
- Tools/surface: V non-tool use through Responses or Chat Completions; V tool calling requires Responses.
- Mapping/response: V Responses `reasoning.effort`; Chat Completions `reasoning_effort` for compatible non-tool calls; Responses typed output items and streaming.
- Prerequisites: OpenAI API key and Tier 1 or higher; Free is not supported.

5. Claude Sonnet 5
- ID/lifecycle: V `claude-sonnet-5`; Active (latest), released 2026-06-30, retirement not sooner than 2027-06-30.
- Effort: V `low`, `medium`, `high`, `xhigh`, `max`; omission equals `high`.
- Explicit none: V native disabled-reasoning mapping `thinking: {"type":"disabled"}`. This is distinct from omission, which leaves adaptive thinking on at high effort.
- Tools/surface: V tool and non-tool Messages API requests; adaptive thinking and tools are compatible. Manual `budget_tokens` is N and returns HTTP 400.
- Mapping/response: `output_config.effort`; Messages content blocks (`thinking`, `text`, `tool_use`) with streaming support.
- Prerequisites: Claude API access, API key, and adequate `max_tokens`; tools and prior thinking blocks must be round-tripped correctly.

6. Claude Opus 5
- ID/lifecycle: V `claude-opus-5`; Active (latest), released 2026-07-24, retirement not sooner than 2027-07-24.
- Effort: V `low`, `medium`, `high`, `xhigh`, `max`; omission equals `high`.
- Explicit none: V through `thinking: {"type":"disabled"}` only at omitted/high, medium, or low effort. N when combined with `xhigh` or `max` (HTTP 400).
- Tools/surface: V Messages tools and non-tool requests. Official docs warn that thinking-disabled tool-heavy requests can occasionally emit a tool call as text instead of a structured `tool_use` block; this is an implementation and live-validation risk, not documented non-support.
- Mapping/response/prerequisites: `output_config.effort`, Messages content blocks and streaming; Claude API access and sufficient output allowance.

7. Claude Haiku 4.5
- ID/lifecycle: V pinned ID `claude-haiku-4-5-20251001`, alias `claude-haiku-4-5`; Active (latest), released 2025-10-15, retirement not sooner than 2026-10-15.
- Effort: N enumerated effort values; `output_config.effort` is not supported. It uses manual extended thinking with `thinking: {"type":"enabled","budget_tokens":N}`.
- Omitted/none: V omission leaves thinking off. V `thinking: {"type":"disabled"}` is accepted because the configuration table rejects only `adaptive`; no literal `none` effort exists.
- Tools/surface: V Messages tools and non-tool requests. N interleaved thinking; forced tool use is incompatible while manual extended thinking is enabled.
- Mapping/response: no effort mapping is valid. DUUMBI must not translate named effort levels into approximate token budgets. Responses use Messages content blocks; thinking blocks appear only when enabled.
- Prerequisites: Claude API access; `budget_tokens` must be at least 1,024 and below `max_tokens` when extended thinking is independently requested.

8. Claude Fable 5.1
- ID/lifecycle: V `claude-fable-5-1`; Active (latest), released 2026-09-01, retirement not sooner than 2027-09-01.
- Effort: V `low`, `medium`, `high`, `xhigh`, `max`; omission equals `high`.
- Explicit none: N. Thinking is always on and `thinking.type: disabled` returns HTTP 400.
- Tools/surface: V Messages tools and non-tool requests using automatic tool choice. N forced `any` or named-tool selection on every request; official guidance is automatic choice with strict tools or structured outputs.
- Mapping/response: `output_config.effort`; optional adaptive-thinking display; Messages typed content blocks and streaming. Per-message effort is beta and needs `mid-conversation-output-config-2026-07-01`.
- Prerequisites: Claude API access. Zero-data-retention use requires express authorization; preserved thinking imposes strict conversation-prefix rules.

9. Grok 4.6
- ID/lifecycle: V `grok-4.6`; current xAI API model, available in the documented API regions. No retirement date is announced.
- Effort: V `low`, `medium`, `high`, `xhigh`; omission defaults to `high`; N explicit `none` because reasoning cannot be disabled.
- Tools/surface: V function calling and non-tool use. Responses is the recommended surface; legacy Chat Completions supports function calls but not returned reasoning content.
- Mapping/response: Responses `reasoning: {"effort":"..."}`; xAI SDK/Chat `reasoning_effort`. Responses returns typed `output` items, including `function_call`; streaming is supported.
- Prerequisites: `XAI_API_KEY`, model access, and a long timeout for reasoning workloads. Batch is not supported.

10. Grok Build 0.1
- ID/lifecycle: V `grok-build-0.1`; official release notes classify it as early access. The model page lists `grok-code-fast-1`, `grok-code-fast`, and `grok-code-fast-1-0825` as aliases and documents `us-east-1` and `us-west-2`.
- Tools: V function-calling capability and non-tool text generation; V the model is described as reasoning-capable.
- Effort: U supported values, U omitted-effort behavior, U explicit-none acceptance/rejection. The official reasoning page explicitly documents `reasoning_effort` only for `grok-4.6` and `grok-4.5`; it neither assigns values to Grok Build 0.1 nor explicitly says that the parameter is rejected.
- Surface/mapping/response: U model-specific support for Responses versus Chat Completions, U native effort field mapping, and U model-specific response-mode restrictions. Generic xAI documentation recommends Responses and marks Chat Completions deprecated, but applying that generic contract to this early-access model would be an inference.
- Prerequisites: V xAI API access and key; Batch is N. Any additional early-access entitlement requirement is U.

Cross-provider conclusions:
- OpenAI GPT-5.6 is sufficiently documented for a Responses-based tools-and-effort implementation, with distinct omitted (`medium`) and explicit-none behavior.
- Anthropic's matrix is sufficiently documented. Its controls are not uniform: Fable cannot disable thinking, Opus/Sonnet can, and Haiku has no effort enum and must not receive invented low/medium/high-to-budget mappings.
- Grok 4.6 is sufficiently documented for Responses-based tools and `reasoning.effort`.
- The authoritative matrix is not complete enough for Stage 8/9 because GPT-6 Astra's omitted-effort behavior and Grok Build 0.1's effort/API contract remain unverified. Under AC-817-01, AC-817-04, AC-817-08, and BDD-817-13, Ready for Build remains blocked.

## Research question at stop

Two exact public-documentation gaps remain: (1) what effective reasoning effort does `gpt-6-astra` use when `reasoning.effort` is omitted; and (2) does `grok-build-0.1` support `/v1/responses` function tools with an effort control, and if so, what values, default, explicit-none behavior, request field, and response shape apply? Recommended next action: obtain written official OpenAI and xAI confirmation for those points. If confirmation cannot be obtained, the owner must renew acceptance with an explicit scope decision to remove or alter the affected omitted-effort/live-matrix obligations; DUUMBI must not infer either contract.
