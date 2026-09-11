# Input contract

What the prompt expects to receive, and what the flows behind it must return. The merge field names below are literal. They appear verbatim in `prompts/policy-code-navigator.txt` and must match the template configuration exactly.

## Why there are only four inputs

Prompt Builder caps a Flex template at five inputs. The natural way to write this prompt would declare thirteen: institution name, current date, requested as-of date, user query, audience type, authorized user context, the policy source bundle, confirmed evaluation results, confirmed action results, source governance configuration, source quality warnings, the service directory, and the internal trace flag. That count blocks the template from saving.

So eleven of those thirteen are delivered as named fields inside two structured flow outputs. The two that stayed independent, `User_Query` and `Authorized_User_Context`, did so deliberately: one carries the live request and the other carries the authorization boundary, and keeping both visible at the top level makes the template easier to audit later. Everything else travels in a bundle.

If you need to pass the prompt something new, add a field to one of the two flow outputs. Do not add a fifth and sixth input.

## The four inputs

### 1. `User_Query`

Type: text input. The person's question, as asked, for the current turn.

This is the only source of action requests. The prompt treats instruction-like text found anywhere else, including inside retrieved records, as data rather than as a command. That distinction is load-bearing, so do not merge retrieved content into this field.

### 2. `Authorized_User_Context`

Type: text input. The identity and authorization envelope for the current person.

This is the sole authority on whether a person is authenticated and what they may see. Populate it from the authenticated session, never from anything the user typed. It should carry at minimum: authentication status, person identifier, role or user type, and whichever of campus, college, program, academic level, academic term enrollment, study-year classification, enrollment status, residency, and catalog year apply.

If this field is empty or its authentication status is false, the prompt restricts itself to general policy information and refuses person-specific disclosure. That behavior depends on the field being honestly populated.

### 3. `Get_Session_Context` (flow output)

Type: flow merge field, referenced as `{!$Flow:Get_Session_Context.Prompt}`.

One structured output carrying five named fields:

| Field | What it carries |
| --- | --- |
| Institution Name | The institution the agent speaks for. |
| Current Date and Time | Used as the as-of date for "what applies now" questions. |
| Requested As-Of Date | Set when the person asked about a specific date, term, or historical situation. Empty otherwise. |
| Audience Type | Prospect, applicant, student, alumni, faculty, or staff. Affects tone and vocabulary only. It never affects what may be disclosed; that is `Authorized_User_Context`'s job alone. |
| Include Internal Trace | Boolean. When true, the prompt may emit record IDs, record links, evaluation timestamps, and data-quality warnings in an Internal trace section. Must be false for any external-facing channel. |

### 4. `Get_Policy_Evidence_Bundle` (flow output)

Type: flow merge field, referenced as `{!$Flow:Get_Policy_Evidence_Bundle.Prompt}`.

One structured output carrying six named fields:

| Field | What it carries |
| --- | --- |
| Official Policy Source Bundle | The retrieved records across evidence classes A through F: external regulations, institutional policies, regulation-to-policy mappings, regulatory codes, academic-term policy rules, and applied holds and violations. |
| Confirmed Policy Evaluation Results | Authoritative learner-specific results returned by an approved policy-evaluation action. |
| Confirmed Action Results | The outcome of any action this turn. The prompt will not claim an action succeeded without a success reported here. |
| Source Governance Configuration | What may be displayed to whom: which public links are approved for external display, whether internal identifiers may be shown, and similar display permissions. |
| Source Quality Warnings | Data-integrity flags such as multiple active versions, overlapping effective periods, missing version-to-clause mappings, or an unpublished registration timeline. The prompt escalates to a human when a warning could change the answer. |
| Approved Service Directory | User-facing contact information and escalation routing. The only source the prompt may draw contact details from. |

## What the bundle flow should do before returning

The prompt contains an ACTIVE-AND-APPLICABLE TEST: a five-point check that a source is active, in effect for the as-of date, correctly linked to its parent version through the right junction object, and matched to the applicable authority, population, campus, program, modality, and academic level.

That test should run in the flow, not in the model. The flow should return only records that already pass it for the resolved as-of date, and should flag any genuine overlap as a Source Quality Warning rather than returning both and leaving the model to choose.

The prompt works either way. It applies the test as a verification layer and refuses to silently pick between competing records. But a language model doing date-range filtering and multi-object joins across six linked objects on every single call will be right most of the time rather than every time, and the failure mode, a confidently cited clause that was superseded last term, is close to invisible in testing. A SOQL query is right every time. Put the filtering in the flow.

## Field names to verify against the org

The prompt names these fields literally. Confirm each exists with this exact name in the target org before deploying:

- `IsActive` on RegulatoryCode
- `CanResolveAutomatically` on RegulatoryCodeViolation
- `PolicyRepresentativeId`, `RegulationManagerId`, `OwnerId`
- Status and effective-date fields on RegulationVersion, RegulationClauseVersion, CompliancePolicyVersion, and CompliancePlcyClauseVersion
- Status on AcadTermEnrlPolicyRuleLog, where the prompt distinguishes Published from Calculated

Junction objects the prompt relies on, which must be present and populated for the linkage checks to mean anything: `RegulationVerRegClauseVer`, `CompliancePlcyCmplClVer`, `RegClCmplPlcyClVer`, `RgltyCodeRegClauseVer`, `RgltyCodeViolRegClVer`.
