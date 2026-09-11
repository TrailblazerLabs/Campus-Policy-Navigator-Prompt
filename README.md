# Education Trailblazers Campus Policy Navigator Prompt

## Description

Salesforce Education Cloud has a robust data model that requires significant configuration and record creation for some of its functionality to work properly. The introduction of Advanced Academic Operations – also known as Student Information System – to Agentforce Education means SIS basics like Calculating GPA, Academic Standing, and Satisfactory Academic Progress as well as the application and removal of Holds rely on a large number of objects to have appropriate records created plus the use of Business Rules Engine, which requires a large number of records, itself.

For Agentforce Education and any AI access its data must have these records in place, this prompt can be used by any higher education institution to allow students, faculty, and staff to engage with an agent, determine which policy applies to their situation, explain the policy in plain language, provide next steps, cite the evidence, and offer a safe path to a human when the answer is uncertain or consequential.

While this prompt doesn't provide the records for agents to reference verified institutional sources and authorized user context to answer questions from prospective students, current students, alumni, and faculty and staff, it assumes the records are in place.

That means, the prompt won't work without the correct records. To that end, schools will need to understand the Business Rules Engine functionality. 

## Contents

- `prompts/policy-code-navigator.txt` - the prompt. Paste verbatim into Prompt Builder.
- `docs/prompt-guide.md` - how to deploy it, what Prompt Builder limits constrain it, how to change it safely.
- `docs/input-contract.md` - the four inputs and the flow outputs behind them. Read before touching the flows.
- `docs/implementation-checklist.md` - the Salesforce build work this depends on.
- `docs/review-2026-09-10.md` - why the prompt is shaped the way it is.

## Tags

`Agentforce Education` `Policies` `Agent Prompt`

## Track Topic

Strategist

## License

Apache License 2.0
