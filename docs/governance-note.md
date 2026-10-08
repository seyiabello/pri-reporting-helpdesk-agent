# Governance note: PRI Reporting Helpdesk agent

Oluwaseyi Bello, October 2026

## Purpose

The agent answers questions about PRI's reporting requirements, the six Principles and reporting terms. It answers only from approved PRI documents and passes anything it can't answer to a person.

## Knowledge sources

- Five public PRI documents: the 2026 minimum requirements guide, the 2026 Reporting & Pathways Glossary, the six Principles page, the About page and the Reporting Tool user guide.
- The agent reads from one SharePoint folder, PRI Reporting Guidance. Other content on the site is out of its reach.
- The 2026 Reporting Framework itself is signatory-only behind MyPRI, so it was left out.
- PRI's documents state they can't be reproduced without consent. They stay inside the tenant and are not published to GitHub. The repo links to them instead.

## Access

- Users must sign in with Microsoft (Entra ID). Anonymous access is disabled.
- The agent answers as the signed-in user, so it only uses documents that user can already open in SharePoint.
- The knowledge folder inherits the site's Owners, Members and Visitors groups. It has no individual shares and no sharing links.
- Other agents in the tenant can't call this agent.

## Response controls

- Ungrounded responses are off. The agent can't answer from the model's general knowledge.
- Web search is off.
- Content moderation is set to High.
- Instructions require a citation for every answer and block investment, legal or financial advice.
- Questions outside PRI are declined. Unanswered PRI questions get an offer to escalate.

## Escalation and data handling

- When a user asks for a person, or accepts the escalation offer, a Power Automate flow logs the question to the Escalated Questions list in SharePoint and emails the reporting team.
- The list stores the question text, a timestamp and a status (New, In progress, Answered). It doesn't store the user's identity in this build.
- The list sits on the same site, under the same group permissions as the documents.

## Testing

21 test questions covered in-scope answers, out-of-scope requests, questions not covered by the documents, escalation and a prompt injection attempt. All passed after one instruction fix. Full results are in the test log.

## Before production use

- Apply Purview sensitivity labels to the knowledge folder and the escalation list.
- Add a data loss prevention policy covering the SharePoint and Outlook connectors.
- Set a retention policy on the escalation list.
- Put signatory-only content behind permissions that match MyPRI access.
- Review usage analytics and escalated questions monthly, and add documents where escalations show gaps.
