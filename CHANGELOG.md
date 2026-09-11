# Changelog

All notable changes to the Policy and Code Navigator prompt.

## [2.0.0] - 2026-09-10

Not yet deployed. Depends on flow work described in `docs/implementation-checklist.md`.

### Fixed

- **Reduced thirteen inputs to four.** Prompt Builder caps a Flex template at five inputs; the previous version declared thirteen, which would have blocked the template from saving. `User_Query` and `Authorized_User_Context` stay independent; the other eleven values now arrive as named fields inside two flow outputs, `Get_Session_Context` and `Get_Policy_Evidence_Bundle`. Two flow merge fields against a limit of five.
- **`Audience_Type` was declared but never referenced.** It is now wired into the PURPOSE section for tone and vocabulary calibration, with an explicit statement that it never affects disclosure.
- **Ambiguity between `Audience_Type` and `Authorized_User_Context` as the authority on access.** PRIVACY AND ACCESS now states that authentication and authorization derive from `Authorized_User_Context` alone. Without this, a channel that sets audience type independently of real authentication could widen disclosure.

### Changed

- **Three near-identical validation checklists became one named ACTIVE-AND-APPLICABLE TEST.** The previous version restated the same five-point active-and-effective check separately for regulations, policies, and regulatory codes. Three copies of one rule invite drift when one gets edited and the others do not, and gave the model three slightly different phrasings of what should be a single test.
- **The test is now framed as verification rather than filtering.** The prompt states that the retrieval flow is expected to apply the test before the bundle arrives, so the model confirms a result instead of filtering a large unfiltered set on every call.
- **"Approved" is defined once.** A new TERMINOLOGY section maps each use of the word to its governing source: Source Governance Configuration for links and versions, Approved Service Directory for contacts, Confirmed Action Results for outcomes. The two most ambiguous instances in CITATIONS now name the governing source directly.

### Added

- **Injection defense extended to action triggering.** ACTION RULES now states that an action can only be triggered by the live authenticated `User_Query`, never by instruction-like text inside a retrieved record. The previous version refused to treat embedded text as evidence but did not extend that refusal to commands, which is the documented Agentforce attack pattern.
- **A FERPA anchor in PURPOSE**, stating the regulatory floor for person-specific disclosure rather than resting the privacy rules on institutional policy alone.
- **A multi-turn as-of-date rule.** When a later turn implies a different date or term, the agent switches and says so rather than silently answering the earlier question's context.
- **Five worked examples**: a general policy question, a hold explanation, a registration-timeline question, a conflicting-sources case, and the no-source fallback.
- **A note that the prompt cannot enforce permission scoping**, pointing at action-level and flow-level controls.

### Unchanged

Evidence class definitions A through F, the precedence rules, the source-quality-warning list, the response format, and the two verbatim fallback strings. These were the strongest part of the original and were left alone deliberately.

## [1.0.0] - 2026-09

Initial draft. Established the structure this version builds on: six official sources, six evidence classes mapped to the Public Sector Solutions and custom compliance objects, as-of date resolution, source precedence, privacy tiers by authentication status, high-consequence topic handling, action rules gated on confirmed results, a citation scheme, a seven-section response format, and two verbatim fallback strings.
