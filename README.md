# WhatsApp-Driven Google Drive Assistant
[![n8n Version](https://img.shields.io/badge/n8n-1.34+-blue.svg)](https://n8n.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An n8n workflow that turns WhatsApp into a voice-controlled Google Drive assistant. Send commands via WhatsApp to list, move, delete, or summarize files in your Google Drive.

![Demo Screenshot](https://example.com/path/to/screenshot.png) *Replace with actual screenshot*

## Features
- 📁 **Drive Operations**: List, move, delete files via WhatsApp
- 📝 **AI Summarization**: Get GPT-4o summaries of documents
- 🔒 **Safety First**: Confirmation workflows for destructive operations
- 📊 **Audit Logging**: Full history of operations in Google Sheets
- 🤖 **Natural Language**: Understands commands like "LIST /ProjectX"

## Prerequisites
1. [Twilio Account](https://www.twilio.com/try-twilio) (WhatsApp Sandbox enabled)
2. [Google Cloud Project](https://console.cloud.google.com/) (Drive & Sheets API)
3. [OpenAI API Key](https://platform.openai.com/api-keys)
4. [Docker](https://docs.docker.com/get-docker/) installed

## Setup Guide

### 1. Twilio Configuration
1. Activate [Twilio WhatsApp Sandbox](https://console.twilio.com/us1/develop/sms/try-whatsapp)
2. Note your:
   - `ACCOUNT_SID`
   - `AUTH_TOKEN`
   - Sandbox number (e.g., +14155238886)

### 2. Google Cloud Setup
```bash
# Enable required APIs
gcloud services enable drive.googleapis.com sheets.googleapis.com

# Create OAuth consent screen
1. Go to APIs & Services > OAuth consent screen
2. Application type: External
3. Add scopes:
   - .../auth/drive.file
   - .../auth/spreadsheets
4. Add test users

# Create OAuth credentials
1. Go to Credentials > Create Credentials > OAuth client ID
3. OpenAI Setup
Create API key at platform.openai.com/api-keys

Set usage limits if needed

4. Environment Configuration
Create .env file:

ini
# Twilio
TWILIO_SID=your_account_sid
TWILIO_TOKEN=your_auth_token
TWILIO_FROM=+14155238886  # Sandbox number

# Google
GOOGLE_CREDENTIALS_PATH=/data/google-credentials.json

# OpenAI
OPENAI_API_KEY=sk-your-key

# Audit Log
AUDIT_LOG_SHEET_ID=your_google_sheet_id
5. Run with Docker
bash
# Start n8n with persistent storage
docker run -d --name drive-assistant \
  -p 5678:5678 \
  -v ./data:/home/node/.n8n \
  -v ./config/google-credentials.json:/data/google-credentials.json \
  --env-file .env \
  n8nio/n8n

# Follow setup logs
docker logs -f drive-assistant
6. Import Workflow
Access n8n at http://localhost:5678

Go to Settings > API to set up credentials:

Twilio API credentials

Google OAuth2 credentials (use downloaded JSON)

OpenAI API key

Import workflow.json from this repository

Command Reference
Command	Example	Description
LIST	LIST /ProjectX	List files in folder
DELETE	DELETE /ProjectX/report.pdf	Delete file (requires confirmation)
MOVE	MOVE /ProjectX/report.pdf /Archive	Move file to new location
SUMMARY	SUMMARY /ProjectX	Summarize documents in folder
HELP	HELP	Show command reference
Confirmation Workflow
text
You: DELETE /ProjectX/report.pdf
Bot: ❗ Reply CONFIRM 5T9B to delete this file
You: CONFIRM 5T9B
Bot: ✅ File deleted successfully
Audit Logging
The workflow automatically logs all operations to a Google Sheet with:

Timestamp

User phone number

Command executed

File path

Status (Pending/Success/Failed)

Confirmation token

Sample Audit Log

Troubleshooting
Common Issues
Twilio webhook not responding:

Verify ngrok/public URL is configured

Check WhatsApp sandbox "WHEN A MESSAGE COMES IN" webhook URL

Google Drive access denied:

Re-authenticate OAuth connection in n8n

Ensure test user is added in GCP consent screen

File not found errors:

Use exact folder names (case-sensitive)

Check file exists in specified path

Error Messages
Error	Solution
403: Google Drive access denied	Re-authenticate OAuth in n8n
File not found: /Projects	Check exact folder name capitalization
Confirmation token mismatch	Use the exact token sent by bot
Unsupported file type	Convert to PDF/DOCX/TXT first
Security
🔐 OAuth scopes limited to drive.file (not full drive access)

⏳ Confirmation tokens expire after 5 minutes

🧹 Automatic sanitization of file paths

🔒 Environment variables for sensitive data

Extending the Workflow
To add new commands:

Edit the Command Router JavaScript node

Add new command pattern:

javascript
// Example ADD command
if (cmd.startsWith('ADD')) {
  return { command: 'ADD', url: cmd.split(' ')[1] };
}
Create new sub-workflow for the command

Update HELP command reference

License
MIT License - see LICENSE for details

Demo Video
https://img.example.com/video-thumbnail.jpg

Note: Full 5-minute demo showing all features

text

## Key Sections Explained

### 1. Visual Identity
- Badges for quick recognition of tech stack
- Clean feature listing with emojis
- Placeholder for demo screenshot/video

### 2. Setup Guide
- Step-by-step instructions for each service
- Code blocks for terminal commands
- Environment variable examples
- Docker run command with volume mounts

### 3. Command Reference
- Table format for easy scanning
- Realistic examples
- Clear confirmation workflow example

### 4. Troubleshooting
- Common issues table
- Error/Solution mapping
- Focus on frequent pain points (auth, file paths)

### 5. Security Features
- Visual lock icon for attention
- Specific scope limitations
- Clean explanation of safety measures

### 6. Extensibility
- Concrete example for adding commands
- Clear pathway for customization
- Encourages further development

### 7. Additional Resources
- License file reference
- Demo video link (replace with actual)
- Clean separation of sections

## Recommended Additions
1. Create `LICENSE` file with MIT license text
2. Add actual screenshot to `/docs/images/screenshot.png`
3. Upload demo video to YouTube/Loom
4. Include partner setup guides:
   ```markdown
   ## Partner Setup Guides
   - [Google Cloud Setup Video](https://youtube.com/google-setup)
   - [Twilio WhatsApp Sandbox Guide](https://twilio.com/whatsapp-setup)
   - [n8n Installation Docs](https://docs.n8n.io/hosting/)


2. Application type: Web application
3. Add authorized redirect URI: https://your-n8n-domain.com/oauth2-callback
4. Download credentials as `google-credentials.json`
