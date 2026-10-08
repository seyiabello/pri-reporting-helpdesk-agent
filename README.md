# PRI Reporting Helpdesk Agent

A Microsoft Copilot Studio agent that answers questions about PRI's reporting requirements from PRI's own public guidance, and hands anything it can't answer to a person through Power Automate and SharePoint.

Built in October 2026 to get hands-on with the Microsoft stack named in the PRI Graduate Data & AI Engineer role: Copilot Studio, Power Platform, SharePoint and Microsoft 365 governance.

![Agent answering in Teams](images/teams-six-principles.png)

## What it does

- Answers questions on PRI's 2026 minimum requirements, the six Principles and reporting terms, with a citation on every answer
- Applies the guidance to scenarios, not just definitions (for example, combining policy coverage across asset classes)
- Declines investment advice and anything outside PRI's documents
- Offers to escalate unanswered PRI questions, then logs them to a SharePoint list and emails the reporting team
- Runs in Microsoft Teams behind Microsoft sign-in

## Architecture

```
User in Teams (signed in with Entra ID)
        │
        ▼
Copilot Studio agent ──search──► SharePoint folder: PRI Reporting Guidance (5 public PRI documents)
        │
        │ can't answer / user asks for a person
        ▼
Escalate topic ──► Power Automate agent flow
                        ├──► SharePoint list: Escalated Questions (question, date, status)
                        └──► Outlook email to the reporting team
```

## Tech stack

| Component | Used for |
| --- | --- |
| Microsoft Copilot Studio (standard agent) | Agent, instructions, topics, generative answers |
| SharePoint Online | Knowledge source and escalation log |
| Power Automate (agent flow) | Logging escalations and sending notifications |
| Office 365 Outlook connector | Escalation emails |
| Microsoft Teams | Published channel |
| Microsoft Entra ID | User authentication |
| Power Platform admin centre and Azure | Environment, Dataverse and pay-as-you-go billing |

## Knowledge sources

Five public PRI documents, stored in one SharePoint folder:

| File | Content |
| --- | --- |
| PRI-2026-Minimum-Requirements-Guide.pdf | Minimum requirements and worked examples for the 2026 Reporting Framework |
| PRI-2026-Reporting-Pathways-Glossary.pdf | 100 defined reporting terms |
| PRI-Six-Principles.pdf | The six Principles and possible actions under each |
| PRI-About.pdf | PRI's background, signatories and mission |
| PRI-2026-Reporting-Tool-User-Guide.pdf | Topic list for the Reporting Tool, used for routing |

The documents aren't in this repo. PRI's terms don't allow reproduction without consent. They're available from [unpri.org](https://www.unpri.org/). The 2026 Reporting Framework itself is signatory-only, so it was left out.

## Escalation flow

![Escalation in the test pane](images/escalation-chat.png)

![Escalated Questions list](images/escalated-questions-list.png)

![Escalation email](images/escalation-email.png)

## Example answers

**Applying the guidance to a scenario**

![Policy coverage answer](images/q8-policy-coverage.png)

**Declining advice while still helping**

![Green bonds answer](images/q13-green-bonds.png)

**Staying inside its documents**

![World Cup answer](images/world-cup.png)

**Resisting a prompt injection**

![Prompt injection answer](images/q21-prompt-injection.png)

## Governance

| Control | Setting |
| --- | --- |
| Authentication | Authenticate with Microsoft (Entra ID). Anonymous access disabled |
| Ungrounded responses | Off. No answers from general model knowledge |
| Web search | Off |
| Content moderation | High |
| Knowledge scope | One SharePoint folder, inheriting site group permissions, no sharing links |
| Agent-to-agent access | Off |
| Advice | Instructions block investment, legal and financial advice |

![Knowledge settings](images/settings-knowledge.png)

![Authentication](images/settings-authentication.png)

![Content moderation](images/settings-moderation.png)

![Folder permissions](images/manage-access.png)

Full detail is in [docs/governance-note.md](docs/governance-note.md).

## Testing

21 questions across five categories. All passed after one instruction fix.

| Category | Questions | Expected behaviour | Result |
| --- | --- | --- | --- |
| In scope | 12 | Correct answer with a citation | Pass |
| Out of scope | 4 | Declines, no advice | Pass |
| Not in the documents | 2 | Says so, offers escalation, invents nothing | Pass |
| Escalation | 2 | Logs to SharePoint and emails | Pass |
| Prompt injection | 1 | Stays on task | Pass |

Full results are in [docs/test-results.md](docs/test-results.md).

## Repo structure

```
pri-reporting-helpdesk-agent/
├── README.md
├── docs/
│   ├── governance-note.md
│   ├── enablement-guide.md
│   └── test-results.md
├── images/
│   ├── teams-six-principles.png
│   ├── escalation-chat.png
│   ├── escalated-questions-list.png
│   ├── escalation-email.png
│   ├── q8-policy-coverage.png
│   ├── q13-green-bonds.png
│   ├── world-cup.png
│   ├── q21-prompt-injection.png
│   ├── settings-knowledge.png
│   ├── settings-authentication.png
│   ├── settings-moderation.png
│   └── manage-access.png
└── solution/
    ├── PRIReportingHelpdesk_1_0_0_0.zip
    └── src/
        ├── Workflows/
        ├── botcomponents/
        ├── bots/
        ├── customizations.xml
        └── solution.xml
```

## Browse the build

`solution/src/` is the solution export unpacked, so the build can be read on GitHub without downloading anything. Each topic is a YAML file called `data`.

- [Agent instructions](solution/src/botcomponents/cr8e9_PRIReportingHelpdesk.gpt.default/data)
- [Escalate topic](solution/src/botcomponents/cr8e9_PRIReportingHelpdesk.topic.Escalate/data)
- [Fallback topic](solution/src/botcomponents/cr8e9_PRIReportingHelpdesk.topic.Fallback/data)
- [Escalation flow (JSON)](solution/src/Workflows/PRIEscalation-Logquestion-CBC24200-63C2-F111-AAAF-7CED8D3A78D0.json)

The SharePoint URLs and the notification email address are replaced with `<your-tenant>.sharepoint.com` and `<your-email>` in `src/` only. The zip keeps the originals because it's the installable export.

## Deploying the solution

1. Create a SharePoint site with a document library folder and a list called Escalated Questions (columns: Question, User, Date, Status, Response).
2. In Power Apps, go to **Solutions**, then **Import solution**, and select the `.zip` in `solution/`.
3. When prompted, create or select connections for SharePoint and Office 365 Outlook.
4. In Copilot Studio, update the knowledge source and the flow's SharePoint site to point at your own site.
5. The environment needs Dataverse and Copilot capacity (a credit pack or pay-as-you-go billing).

The export references the original trial tenant's SharePoint URL. These must be changed after import.

## Lessons learned

- **Licensing and capacity took longer than the build.** The first environment had no Dataverse, then no Copilot Credits. The new GitHub Copilot agent type drew on credits the trial didn't have, so the agent was rebuilt as a standard agent on a Power Platform pay-as-you-go plan.
- **Public isn't the same as usable.** PRI moved much of its reporting material behind a signatory login in 2025. The agent was scoped to what was genuinely public.
- **Grounding needs both settings and instructions.** Ungrounded responses were on by default. Instructions alone kept answers grounded in testing, but the setting is the control that enforces it.
- **Type the variable, don't type the name.** A flow input typed as plain text sent the literal word "UserEmail" to SharePoint, which rejected the row. Inserting dynamic content fixed it.

## Known issues and next steps

- After a user accepts escalation, the agent asks for the question again instead of passing on the original one. Next step: store the unanswered question in a variable and pass it straight to the flow.
- It sometimes offers escalation for requests unrelated to PRI.
- Production use would add Purview sensitivity labels, a DLP policy for the connectors, and a retention policy on the escalation list.
- A Power BI report on escalations would show which topics need more documentation.

## Enablement

[docs/enablement-guide.md](docs/enablement-guide.md) is a one-page guide for non-technical staff to build their first Copilot Studio agent in 30 minutes.

## Pushing to GitHub

```bash
git init
git add .
git commit -m "PRI Reporting Helpdesk: Copilot Studio agent with SharePoint knowledge and escalation flow"
git branch -M main
git remote add origin https://github.com/seyiabello/pri-reporting-helpdesk-agent.git
git push -u origin main
```

Create the empty repo on GitHub first, without a README, so the push doesn't conflict.