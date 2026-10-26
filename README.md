# Game Production AI Agent

**Checkpoint 1 — AI Automation | n8n**

An AI-assisted game production workflow that receives natural-language tasks through n8n Chat, classifies game-development work, and writes authorized task records to Google Sheets. A Gmail node sends a human-readable execution report.

## Workflow

`Chat Trigger → AI Agent (Tools Agent) → Gmail report`

The AI Agent uses two lateral connections:
- **OpenAI Chat Model:** `gpt-4o-mini`, temperature `0.2`.
- **Google Sheets Tool:** append a task row when the user explicitly requests registration.

**Guardrail:** 7 maximum agent iterations. The System Message defines role, scope, objectives, prohibited actions, escalation, and output rules. It instructs the agent not to invent deadlines or assignees and not to claim success when tool writes fail.

## Task schema

`Titulo`, `Descripcion`, `Disciplina`, `Prioridad`, `Estado`, `Responsable`, `Fecha_limite`, `Origen`.

## Test evidence

The manually tested instance successfully processed a task about fixing drone damage, appended one row to the `Tareas` worksheet, and sent an email report. The exported template does not include execution history; evidence was checked in n8n and Google Sheets.

## Import and configure

1. Import [`checkpoint1_camila_liendro.json`](./checkpoint1_camila_liendro.json) into n8n.
2. Connect your OpenAI, Google Sheets, and Gmail credentials.
3. Select your Google Sheets document and `Tareas` tab; remap columns if n8n asks for it.
4. Replace the placeholder destination email in the Gmail node.
5. Execute a manual chat test and inspect n8n's execution history before publishing.

The public workflow removes private account references. It is an importable **template**, not a connected deployment. Keep the original, working n8n export privately for future modules.

## Planned extensions

- M2: manager/worker agents
- M3: session memory and context
- M4: integrations with project-management tools
- M5: retrieval over production documentation (RAG)
- M6: voice interaction

## Security and scope

Do not commit API keys, OAuth tokens, credential IDs, private email addresses, or private spreadsheet IDs. The Gmail message reports the input and final result; detailed tool calls and errors are inspected in n8n Executions, rather than exposing private model reasoning.
