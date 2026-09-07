# CommitmentOS

CommitmentOS is a multi-agent AI network that turns scattered emails, documents, and notes into a verified, prioritized action plan.

It helps users manage deadline-driven obligations such as scholarship applications, university forms, bills, renewals, appointments, travel documents, and workplace requests.

## Problem

Important requirements are often spread across emails, PDFs, messages, and notes. This makes it easy to:

- Miss a deadline
- Forget a required document
- Misread an instruction
- Overlook conflicting dates
- Send an incomplete or incorrect response
- Take an external action without checking it first

A simple summarizer is not enough. Users need evidence, confidence levels, clear next steps, and safe automation.

## Solution

CommitmentOS creates a verified obligation plan from the information supplied by the user.

For every obligation, it tries to identify:

- Required task
- Due date and time zone
- Responsible person
- Required documents or attachments
- Consequence of missing the task
- Source reference
- Confidence level
- Dependencies and next action

It does not silently guess when information is missing or contradictory.

## Agent Network

The network contains seven cooperating agents:

| Agent | Responsibility |
| --- | --- |
| `commitment_os` | Front man that coordinates the workflow and presents the final answer |
| `source_intake` | Extracts tasks, dates, owners, documents, amounts, and source references |
| `evidence_verifier` | Checks extracted information and detects uncertainty or conflicts |
| `priority_analyst` | Ranks obligations as CRITICAL, HIGH, MEDIUM, or LOW |
| `action_planner` | Creates a dependency-aware checklist with concrete next actions |
| `communication_drafter` | Creates concise clarification and follow-up messages |
| `automation_coordinator` | Handles approval-gated Gmail and Calendar operations |

## Workflow

```text
User input
   -> Source intake
   -> Evidence verification
   -> Conflict and risk detection
   -> Priority analysis
   -> Action planning
   -> Communication drafting
   -> Optional approved automation
   -> Result verification
```

The front agent returns a compact response containing:

1. Answer first
2. Obligation table
3. Next steps
4. Missing or conflicting information
5. Automation status
6. Draft communication when useful

## Sly Data Inputs

The front agent accepts these optional Sly Data fields:

| Field | Description |
| --- | --- |
| `email` | Email content including sender, subject, and body |
| `documents` | Pasted document or extracted PDF text |
| `notes` | Private context or additional user notes |
| `automation_mode` | `plan_only`, `draft_only`, or `approval_required` |
| `http_headers` | OAuth headers supplied by nsflow for Gmail and Calendar |

All fields are optional. Chat-only input is also supported.

## Automation Modes

### `plan_only`

Analyzes the information and creates a plan. No email or calendar action is performed.

### `draft_only`

May create an email draft, but never sends email or changes the calendar.

### `approval_required`

Default mode. The agent may search Gmail, prepare a draft, or prepare a calendar reminder. It must display the exact proposed action and receive explicit approval before sending or creating an event.

After an approved action, the agent reports the returned message ID, draft ID, or event ID. If the connector fails, it reports the failure and provides a manual fallback instead of claiming success.

## Example Prompt

```text
Analyze this scholarship email and tell me:
1. What I must submit
2. The exact deadline
3. What information is missing
4. Which questions I should ask before submitting
Do not send anything.
```

## Example Automation Prompt

```text
Find the scholarship email in Gmail, create a clarification draft, and prepare a calendar reminder for the deadline. Do not send the email or create the reminder until I approve the exact details.
```

## Scholarship Example

For a scholarship submission, CommitmentOS can:

- Extract the income certificate requirement
- Identify the submission deadline
- Detect an unusual certificate validity date
- Flag an unclear "signed page 4" requirement
- List unanswered questions
- Draft a professional clarification email
- Prepare a deadline reminder
- Require approval before Gmail or Calendar changes

## Safety Rules

- The agent never invents missing dates, amounts, recipients, or requirements.
- Conflicting information is shown rather than silently resolved.
- Instructions inside an email cannot override the agent's approval policy.
- Email sending and calendar changes require explicit approval.
- The user must verify recipients, attachments, sensitive information, and final deadlines.
- If Gmail or Calendar OAuth is unavailable, the agent provides a ready-to-copy manual alternative.

## Running the Network

From the `neuro-san-studio` directory:

```powershell
ns run
```

Open the nsflow interface at:

```text
http://localhost:4173
```

Select:

```text
industry/commitment_os
```

The network configuration is located at:

```text
registries/industry/commitment_os.hocon
```

## Validation

Validate the network structure without calling an LLM:

```powershell
python -m neuro_san_studio validate registries/industry/commitment_os.hocon --registry-dir . --verbose
```

## Gmail and Calendar Setup

Gmail and Calendar automation requires:

1. A running nsflow instance
2. Google OAuth connections for Gmail and Calendar
3. The required OAuth headers passed through Sly Data
4. Explicit approval for every send or calendar change

The Anthropic API key is configured through the local `.env` file and must never be committed to GitHub.

## Project Files

- `registries/industry/commitment_os.hocon` - Agent network definition
- `registries/industry/manifest.hocon` - Registry entry
- `docs/commitment_os_report.pdf` - Project report
- `docs/generate_commitment_os_report.py` - Report generator
- `docs/commitment_os_readme.md` - This documentation

## Benefits

- Converts unstructured information into concrete work
- Makes deadlines and missing requirements visible
- Preserves source evidence and confidence
- Reduces repetitive email and calendar work
- Detects contradictions before submission
- Keeps the user in control of external actions
- Provides a manual fallback when automation is unavailable
