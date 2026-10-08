# Build your first agent in 30 minutes

A guide for staff with no technical background. You'll build a Copilot Studio agent that answers one type of question your team gets often, using your own documents.

## Before you start

- You need a work account with Copilot Studio access.
- Pick documents your team is allowed to share internally. Don't use anything confidential, personal or signatory-only.

## 1. Pick one question (5 minutes)

Choose a question your team answers every week, such as "What are the reporting deadlines?" or "What does this term mean?" Start narrow. One good answer beats ten weak ones.

## 2. Gather the documents (5 minutes)

Find the two to five documents that answer it. Put them in one SharePoint folder, and give each file a clear name. The agent shows file names in its citations.

## 3. Create the agent (5 minutes)

1. Go to copilotstudio.microsoft.com.
2. Under **Other ways to build**, create a standard agent.
3. Give it a plain name, such as "Reporting Deadlines Helper."

## 4. Connect your documents (5 minutes)

1. Open **Knowledge**, then **Add knowledge**, then **SharePoint**.
2. Paste the folder's address.
3. Wait until the status says **Ready**.

## 5. Write three lines of instructions (5 minutes)

Paste something like this, then adjust it:

```
Answer only from the documents in the connected folder, and cite the source.
If the documents don't answer the question, say so and suggest who to contact.
Keep answers short and in plain English.
```

Then go to **Settings**, then **Generative AI**, and turn off **Allow ungrounded responses** and **Use information from the Web**.

## 6. Test with five real questions (5 minutes)

Ask three questions the documents should answer and two they shouldn't. Check that:

- Every answer has a citation
- The agent admits when it doesn't know, rather than guessing

If an answer is wrong, check the document first, then the instructions.

## 7. Share it

Publish the agent and add it to Teams. Tell your team what it covers and what it doesn't.

## Common problems

| Problem | Fix |
| --- | --- |
| No answer, or "no information found" | Check the knowledge status says Ready and the folder address is right |
| Answers without citations | Add "cite the source" to the instructions |
| Answers things outside your documents | Turn off ungrounded responses and web search |
| Says you need credits | Ask your admin. The environment needs Copilot capacity |

Need help? Bring your agent to the drop-in session and we'll fix it together.
