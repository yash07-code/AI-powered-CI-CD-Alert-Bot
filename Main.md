Create an AI-powered CI/CD alert workflow that integrates Jenkins, n8n, OpenAI, and Telegram.
Requirements:
1. Jenkins should send a POST request to an n8n webhook when a pipeline fails. The payload must include:
   - Job name
   - Build status
   - Build URL
   - Error log or failure message
2. The n8n workflow should have:
   - A Webhook node that listens for Jenkins CI failure events.
   - A function (or set node) to extract and prepare data for processing.
   - An HTTP Request node that sends the error log to OpenAI (gpt-3.5-turbo).
     - Prompt: "You are a DevOps expert. Here is a CI/CD pipeline error log: [insert error]. Please explain the issue and suggest a fix."
   - A Telegram node that sends the job name, status, build link, and the AI-generated explanation to a predefined chat ID.
3. The final Telegram message should be formatted with:
   - A CI/CD Failure alert title
   - Job name
   - Status
   - Build link (as a hyperlink)
   - AI explanation (OpenAI response)
   - Markdown formatting
Make the workflow in n8n-style format. Assume n8n is running locally on Docker Desktop. Include variable usage ({{$json["..."]}}) where needed.
