# 🏞️ Washington River Level Emailer

This script automatically gathers real‑time Washington river level data from the  
USGS Water Data website and emails customized river‑level reports to a list of  
recipients stored in a Google Sheet.

---

## 📌 What the main script does

1. Uses Selenium to load the USGS Washington river‑levels page.
2. Scrapes gauge numbers, names, timestamps, and flow levels.
3. Reads a Google Sheet where each row represents a subscriber.
4. Each subscriber has TRUE/FALSE columns indicating which rivers they care about.
5. For each subscriber, the script:
   - Finds the relevant gauges  
   - Builds an HTML email with links + flow data  
   - Sends the email using the Gmail API  

---

## 🎯 Who this is for

Whitewater kayakers, rafters, anglers, hydrology nerds, and anyone who wants  
automated river‑level notifications based on their personal list of favorite rivers.

---

## 🔧 What you must supply

To run this project, you must provide:

- A **Google Cloud service account JSON file** (for Google Sheets access)
- A **Gmail OAuth client secret JSON file** (for sending email via Gmail API)
- A **Google Sheet** with the following structure:
  - **Column A:** Name  
  - **Column B:** River gauge IDs  
  - **Column C:** Email  
  - **Columns D+ :** TRUE/FALSE values indicating which rivers each subscriber wants  

---

# 📄 Additional Helper Scripts

This project includes two optional helper scripts that make setup and automation easier.  
Both scripts are safe to include in a public repository as long as you **do not commit any credential files**.

---

## 📨 1. `generate_gmail_token.py`

This script launches the Google OAuth flow in your browser so you can authorize the project to send email using your Gmail account. After you log in and grant permission, it generates a `token.json` file that stores your refresh token for future runs.

### When to use it
Run this once during setup, or anytime you need to regenerate your Gmail OAuth token.

### Important
- Do **not** commit `gmail_credentials.json`
- Do **not** commit `token.json`

### Code
```python
from google_auth_oauthlib.flow import InstalledAppFlow
import os

GMAIL_SCOPES = ['https://www.googleapis.com/auth/gmail.send']

flow = InstalledAppFlow.from_client_secrets_file(
    'gmail_credentials.json',   # <-- User must supply this file locally
    scopes=GMAIL_SCOPES
)

creds = flow.run_local_server(port=0, access_type='offline', prompt='consent')

# Save token for future use
with open('token.json', 'w') as token_file:
    token_file.write(creds.to_json())

