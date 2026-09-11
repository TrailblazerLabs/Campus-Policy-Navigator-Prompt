# Prompt guide

How to deploy the Policy and Code Navigator prompt, what platform limits constrain it, and how to change it without breaking something that matters.

## What is in this repository

| Path | What it is |
| --- | --- |
| `prompts/policy-code-navigator.txt` | The prompt itself. Paste verbatim into the Prompt Builder template body. Nothing in this file is commentary; every line is model-facing. |
| `docs/input-contract.md` | The four inputs the prompt expects and the named fields each flow output must return. Read this before building or changing the flows. |
| `docs/implementation-checklist.md` | The Salesforce build work this prompt depends on, none of which a prompt can do on its own. |
| `docs/review-2026-09-10.md` | The review that produced version 2, kept as the rationale record for why the prompt is shaped the way it is. |
| `docs/prompt-guide.md` | This file. |
| `CHANGELOG.md` | Version history. |

## Current status

Version 2.0.0. Not yet deployed. The prompt text is complete, but the two flows it depends on, `Get_Session_Context` and `Get_Policy_Evidence_Bundle`, do not exist yet, so the template will not resolve in Prompt Builder until they are built. See `implementation-checklist.md`.

## Deploying

1. Build the two flows described in `input-contract.md`.
2. In Setup, open Prompt Builder and create a Flex template.
3. Paste the entire contents of `prompts/policy-code-navigator.txt` into the template body.
4. Configure the four inputs exactly as named in `input-contract.md`. The merge field names in the prompt text are literal and must match.
5. Test against the five scenarios in the prompt's WORKED EXAMPLES section, plus one genuine conflicting-source case and one genuine no-source case, using Plan Tracer.
6. Pilot with a small internal group before opening to students.

## Platform constraints this prompt is built around

These are Prompt Builder limits, not preferences. Changing the prompt in ways that violate them will block the template from saving.

- **Five inputs maximum** in a Flex template. This prompt uses four. Adding a fifth is possible; adding a sixth is not. If you need more data, add a field to one of the existing flow outputs rather than adding an input.
- **Five flow merge fields maximum.** This prompt uses two.
- **128,000 characters maximum** template size. This prompt is roughly 27,700, so there is substantial headroom.
- Fifty template versions maximum, so the version history in this repository matters more than Prompt Builder's own.

## Changing the prompt safely

This prompt governs answers about holds, financial aid, registration blocks, immigration status, and disability accommodations. A careless edit has real consequences for real students, so treat changes the way you would treat changes to a policy document, not to marketing copy.

- Open a branch and a pull request. Do not commit prompt changes straight to `main`.
- Say in the pull request description which behavior you are trying to change and why the current text produces the wrong behavior. "Tightening the wording" is not a reason.
- Re-test the five worked-example scenarios before merging. Prompt edits have non-local effects; a change to the citation rules can alter refusal behavior.
- If a change touches the ACTIVE-AND-APPLICABLE TEST, the ACTION RULES, the PRIVACY AND ACCESS section, or the two verbatim fallback strings, get a second reviewer. Those four sections are what stand between the agent and a bad disclosure or a fabricated answer.
- Bump the version in `CHANGELOG.md` and note what changed.

## A note on the data model

The prompt references two families of objects. `RegulatoryAuthority`, `Regulation`, `RegulatoryCode`, and `RegulatoryCodeViolation` are standard Salesforce Public Sector Solutions objects, not Education Cloud objects. The `CompliancePolicy` and `AcademicTerm` families appear to be custom objects built for this institution. Confirm both the licensing and the exact field names against the target org before deploying. Details in `implementation-checklist.md`.
