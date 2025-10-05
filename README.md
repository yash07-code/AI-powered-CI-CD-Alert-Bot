# AI-powered-CI-CD-Alert-Bot


An intelligent CI/CD monitoring system that automatically analyzes pipeline failures using OpenAI and sends detailed alerts via Telegram. When a Jenkins pipeline fails, this system captures the error logs, processes them through AI for expert analysis, and delivers actionable insights directly to your team's Telegram chat.
Features

Automatic Failure Detection: Monitors Jenkins pipelines for failures
AI-Powered Analysis: Uses OpenAI GPT-3.5-turbo to analyze error logs and suggest fixes
Instant Notifications: Sends formatted alerts to Telegram with build links and AI explanations
Rich Formatting: Markdown-formatted messages with emojis for better visibility
Comprehensive Logging: Detailed error logs and build information
Scalable Architecture: Easy to extend with additional notification channels

Table of Contents

Prerequisites
System Requirements
Installation
Configuration
Usage
API Documentation
Troubleshooting
Contributing
License

Prerequisites
Software Requirements
SoftwareVersionPurposeDocker20.10+Container runtime for n8nDocker Compose2.0+Multi-container orchestrationJenkins2.400+CI/CD pipeline automationNode.js18+(Optional) For local developmentGit2.30+Version control
Service Accounts & APIs

OpenAI API Key

Create account at OpenAI
Generate API key from API settings
Ensure billing is set up for API usage


Telegram Bot Token

Create bot via @BotFather
Get bot token and chat ID
Add bot to your target chat/group


Jenkins Access

Admin access to Jenkins instance
Ability to install plugins
Permission to modify job configurations



💻 System Requirements
Minimum Hardware Requirements
ComponentMinimumRecommendedCPU2 cores4 coresRAM4GB8GBStorage10GB20GBNetwork10Mbps100Mbps
Network Requirements

Ports: 5678 (n8n), 8080 (Jenkins)
Outbound Internet: Access to OpenAI API and Telegram API
Internal Network: Jenkins → n8n communication

📦 Installation
1. Clone the Repository
bashgit clone https://github.com/your-username/ai-cicd-alerts.git
cd ai-cicd-alerts
2. Set Up Environment Variables
Create a .env file in the project root:
bash# n8n Configuration
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=your_secure_password
WEBHOOK_URL=http://localhost:5678/

# OpenAI Configuration
OPENAI_API_KEY=your_openai_api_key_here

# Telegram Configuration
TELEGRAM_BOT_TOKEN=your_telegram_bot_token_here
TELEGRAM_CHAT_ID=your_telegram_chat_id_here

# Jenkins Configuration
JENKINS_URL=http://localhost:8080
JENKINS_USER=admin
JENKINS_PASSWORD=your_jenkins_password
3. Start n8n with Docker Compose
bash# Start n8n service
docker-compose up -d

# Check if n8n is running
docker-compose ps
4. Access n8n Web Interface
Open your browser and navigate to:

URL: http://localhost:5678
Username: admin
Password: your_secure_password

5. Import the Workflow

In n8n, go to Workflows → Import from File
Upload the n8n-workflow.json file
Click Import

⚙️ Configuration
1. Configure n8n Credentials
OpenAI API Credentials

Go to Credentials → Add Credential
Select OpenAI
Enter your API key
Name it OpenAI API
Save

Telegram Bot Credentials

Go to Credentials → Add Credential
Select Telegram
Enter your bot token
Name it Telegram Bot API
Save

2. Update Workflow Settings
Update Chat ID

Open the imported workflow
Click on Send Telegram Alert node
Replace YOUR_TELEGRAM_CHAT_ID with your actual chat ID
Save the workflow

Activate Webhook

Click on Jenkins Failure Webhook node
Note the webhook URL: http://localhost:5678/webhook/jenkins-failure-webhook
Activate the workflow

3. Configure Jenkins
Install Required Plugins
bash# Via Jenkins CLI (if available)
java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin http_request
java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin pipeline-stage-step
java -jar jenkins-cli.jar -s http://localhost:8080 restart
Or install via Jenkins UI:

Go to Manage Jenkins → Manage Plugins
Install these plugins:

HTTP Request Plugin
Pipeline Plugin
Build Failure Analyzer (optional)


🎯 Usage
Starting the System

Start n8n:
bashdocker-compose up -d

Activate the workflow in n8n UI
Configure Jenkins jobs with the pipeline script
Test the integration:
bash# Test webhook manually
curl -X POST http://localhost:5678/webhook/jenkins-failure-webhook \
  -H "Content-Type: application/json" \
  -d '{
    "job_name": "test-job",
    "build_status": "FAILURE", 
    "build_url": "http://jenkins.local/job/test-job/123/",
    "build_number": "123",
    "error_log": "Error: Test failed in TestClass.testMethod()",
    "timestamp": "2025-01-01 12:00:00"
  }'


Monitoring
n8n Execution Logs

Go to Executions in n8n UI
Monitor workflow runs and debug issues

Jenkins Console Output

Check Jenkins job console output for webhook calls
Look for HTTP request success/failure messages

Telegram Notifications
Expected message format:
🚨 CI/CD Pipeline Failure Alert 🚨

Job: my-application-pipeline
Status: FAILURE
Build #: 123
Build URL: View Build

🤖 AI Analysis:
The error indicates a test failure in the TestClass.testMethod(). 
This appears to be an assertion error where the expected value was 5 
but the actual value was 3. To fix this:

1. Check the test logic in TestClass.testMethod()
2. Verify the expected value (5) is correct
3. Debug why the actual value is 3
4. Update either the test or the code being tested

Generated by AI-powered DevOps Assistant
📚 API Documentation
Webhook Endpoint
URL: POST /webhook/jenkins-failure-webhook
Request Body:
json{
  "job_name": "string",
  "build_status": "string",
  "build_url": "string", 
  "build_number": "string",
  "error_log": "string",
  "timestamp": "string"
}
Response:
json{
  "status": "success",
  "message": "Jenkins failure alert processed successfully",
  "jobName": "string",
  "timestamp": "string"
}
Environment Variables
VariableRequiredDefaultDescriptionN8N_BASIC_AUTH_ACTIVEYestrueEnable basic auth for n8nN8N_BASIC_AUTH_USERYesadminn8n usernameN8N_BASIC_AUTH_PASSWORDYes-n8n passwordWEBHOOK_URLYes-Base webhook URLOPENAI_API_KEYYes-OpenAI API keyTELEGRAM_BOT_TOKENYes-Telegram bot tokenTELEGRAM_CHAT_IDYes-Telegram chat ID
🔍 Troubleshooting
Common Issues
1. n8n Not Accessible
Problem: Cannot access n8n at http://localhost:5678
Solutions:

Check if container is running: docker-compose ps
Check logs: docker-compose logs n8n
Verify port mapping in docker-compose.yml
Check firewall settings

2. Jenkins Webhook Calls Failing
Problem: Jenkins cannot reach n8n webhook
Solutions:

Test connectivity: curl -X POST http://localhost:5678/webhook/jenkins-failure-webhook
Check network configuration
Verify webhook URL in Jenkins configuration
Check n8n logs for incoming requests

3. OpenAI API Errors
Problem: AI analysis not working
Solutions:

Verify API key is valid and has credits
Check OpenAI API status
Review rate limits
Check n8n execution logs for API responses

4. Telegram Messages Not Sent
Problem: No notifications received
Solutions:

Verify bot token is correct
Check chat ID is accurate
Ensure bot is added to the target chat
Test bot manually: send /start to your bot

Debug Commands
bash# Check n8n logs
docker-compose logs -f n8n

# Test webhook manually
curl -X POST http://localhost:5678/webhook/jenkins-failure-webhook \
  -H "Content-Type: application/json" \
  -d '{"job_name":"test","build_status":"FAILURE","error_log":"test error"}'

# Check container status
docker-compose ps

# Restart services
docker-compose restart

# Check network connectivity
docker network ls
docker network inspect ai-cicd-alerts_default
🚀 Advanced Configuration
Custom AI Prompts
Modify the OpenAI prompt in the workflow:
json{
  "role": "user",
  "content": "You are a senior DevOps engineer. Analyze this CI/CD failure: {{$json[\"errorLog\"]}}. Provide: 1) Root cause 2) Fix steps 3) Prevention measures"
}
Multiple Notification Channels
Add Slack, email, or other notification nodes:

Add new credential for Slack/Email
Add corresponding nodes to workflow
Connect after OpenAI analysis
Configure message formatting

Error Categorization
Add logic to categorize errors:
javascript// In a Function node
const errorLog = items[0].json.errorLog;
let category = 'unknown';

if (errorLog.includes('test')) {
  category = 'test-failure';
} else if (errorLog.includes('compile')) {
  category = 'build-failure';
} else if (errorLog.includes('deploy')) {
  category = 'deployment-failure';
}

return [{ json: { ...items[0].json, category } }];
🔒 Security Considerations
Production Deployment

Use HTTPS: Configure SSL/TLS certificates
Secure Credentials: Use environment variables or secret management
Network Security: Implement firewall rules and VPN access
Authentication: Enable strong authentication for all services
Rate Limiting: Implement rate limiting for webhooks
Audit Logs: Enable comprehensive logging

Security Checklist

 Change default passwords
 Enable HTTPS for all services
 Restrict network access
 Use secret management for API keys
 Enable audit logging
 Regular security updates
 Implement backup strategies

📈 Monitoring & Metrics
Key Metrics to Track

Failure Rate: Number of failures per day/week
Response Time: Time from failure to notification
Resolution Time: Time to fix issues
AI Accuracy: Quality of AI-generated suggestions

Monitoring Tools

n8n Execution History: Built-in execution tracking
Jenkins Build History: Historical build data
Custom Dashboards: Create dashboards for metrics
Log Aggregation: Centralized log collection

🤝 Contributing
Development Setup

Fork the repository
Create a feature branch
Make your changes
Test thoroughly
Submit a pull request

Contribution Guidelines

Follow existing code style
Add tests for new features
Update documentation
Ensure all tests pass
Write clear commit messages

📄 License
This project is licensed under the MIT License - see the LICENSE file for details.
🆘 Support
Getting Help

GitHub Issues: Report bugs and request features
Documentation: Check this README and inline comments
Community: Join discussions in GitHub Discussions
Email: Contact maintainers for critical issues

Resources

n8n Documentation
Jenkins Documentation
OpenAI API Documentation
Telegram Bot API

📊 Project Status

✅ Core Features: Complete
✅ Documentation: Complete
✅ Testing: Manual testing complete
🔄 Automated Tests: In progress
🔄 Performance Optimization: Planned
🔄 Additional Integrations: Planned

🗺️ Roadmap
Short Term (1-2 months)

 Automated testing suite
 Performance optimizations
 Additional notification channels (Slack, Email)
 Better error categorization

Medium Term (3-6 months)

 Web dashboard for monitoring
 Historical analytics
 Custom AI model fine-tuning
 Multi-Jenkins support

Long Term (6+ months)

 Machine learning for failure prediction
 Advanced reporting capabilities
 Integration with monitoring tools
 Enterprise features
