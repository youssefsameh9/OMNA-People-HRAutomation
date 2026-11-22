# OMNA People

OMNA People is an HR automation system built with n8n. Candidates apply with a PDF CV and get AI-scored screening, employees ask a policy assistant that answers only from company documents, and anyone can request a CV coaching report by email.

**Live site:** https://youssefsameh9.github.io/OMNA-People-HRAutomation/

The site talks to real n8n workflows through an ngrok tunnel, so it works only while the host computer, Docker, n8n and ngrok are running. When they are off, the status badge shows Offline.

## Workflows

| File | Trigger | What it does |
|---|---|---|
| `recruitment-1-intake.json` | Form | Candidates apply with a PDF CV. The form is validated, duplicates are caught, the CV is saved to Drive and the application is logged to a Google Sheet. |
| `recruitment-2-ai-screening.json` | Every hour | Reads new applications, scores each CV with AI, then emails an invitation or a decline. Borderline cases go to HR. |
| `recruitment-3-error-handler.json` | Error trigger and schedules | Logs failures, emails alerts, sends a daily digest and checks that the queue is moving. |
| `job-application-coach.json` | Form | Reads a CV against a job post and emails a coaching report. |
| `rag-hr-assistant.json` | Chat, n8n start, daily at 7:00, manual | HR policy assistant. It loads policy documents from Google Drive into an in-memory vector store and answers only from them. |

## Services needed

- Docker Desktop and n8n
- ngrok (free static domain)
- Google Workspace: Sheets, Drive and Gmail
- Google Gemini API and Groq API (free tiers)
- GitHub Pages for the website

## Import steps

1. Run n8n with Docker and set its public address (`WEBHOOK_URL`, `N8N_HOST` and `N8N_EDITOR_BASE_URL`) to your ngrok domain.
2. In n8n, create a folder and import each file from `workflows/` with **Import from File**.
3. Create credentials for Google Sheets, Drive and Gmail (OAuth2), Gemini and Groq, and select them on every node that shows a warning.
4. Create your own Google Sheets for applications and errors, and a Drive folder for CVs. Open the Google Sheets nodes to see the column names to use as the first row. Select your own files in every Sheets and Drive node.
5. Create a Drive folder with your policy documents and select it in **List Policy Files** in the RAG workflow.
6. Set the error workflow (Recruitment 3) in the settings of the other workflows.
7. In the RAG chat trigger, turn on **Make Chat Publicly Available**, choose Hosted Chat and put your GitHub Pages address in Allowed Origins.
8. Activate the workflows, then copy each form and chat **Production URL** into the `CONFIG` block at the top of `index.html`.
