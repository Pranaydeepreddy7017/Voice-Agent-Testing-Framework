
# Setup Guide

## Prerequisites
- Python 3.8+
- Twilio account (free tier works)
- ngrok installed (`brew install ngrok` or download from ngrok.com)

## Step 1: Install Dependencies
```bash
pip install -r requirements.txt
```
## Step 1.5: Start the Server
```bash
python server.py
```
Server runs on `http://localhost:5000`

## Step 2: Set Up ngrok Tunnel
```bash
ngrok http 5000
```
Copy the **Forwarding URL** (e.g., `https://abc123.ngrok.io`) to .env


## Step 3: Configure Environment Variables
Add the test line number (+1-805-xxx-xxxx) to .env as well as config.py file 
```bash
PUBLIC_BASE_URL=https://your-ngrok-url.ngrok.io
TWILIO_ACCOUNT_SID=your_account_sid
TWILIO_AUTH_TOKEN=your_auth_token
TWILIO_FROM_NUMBER=+1234567890
OPENAI_API_KEY=your_openai_key
```

### Getting Twilio Credentials
1. Log in to [Twilio Console](https://www.twilio.com/console)
2. Find **Account SID** and **Auth Token** on the dashboard
3. Go to **Phone Numbers** → **Active Numbers** to find your number (or buy one)

### Getting OpenAI API Key
1. Visit [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. Create a new secret key and copy it

## Step 4: Start the Server
```bash
python server.py
```
Server runs on `http://localhost:5000`

## Step 5: Initiate a Test Call

```bash
python3 -u run_calls.py --n 5  # Initiates 5 concurrent calls
```
Change `--n` to any number of simultaneous test calls you want to run.
This dials your Twilio number and connects to the `/voice` endpoint.

## Step 6: Analyze Call Transcripts
After generating multiple call transcripts 

```bash
python3 analyze_calls.py
```
Generates QA analysis for completed calls in `transcripts/`

---

**Output locations:**
- Call state: `data/calls/{call_sid}.json`
- Transcripts: `transcripts/{call_sid}.txt`
