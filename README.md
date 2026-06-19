🎙️ AI-to-AI Voice Agent Red Teaming
Stress-test your voice agents with dynamic, LLM-driven personas before your users do.

Most voice agent bugs only surface when conversations go off-script—interruptions, weird questions, and edge-case behaviors. This framework doesn't just ping your /voice endpoint; it deploys autonomous AI caller agents to actively probe, converse with, and evaluate your Twilio-powered bot.

By orchestrating OpenAI caller personas through a local Flask backend, you can simulate dozens of concurrent, unpredictable human interactions, record every transcript, and run automated QA analysis—all from your local machine in under 5 minutes.

What It Does:

LLM-Driven Caller Personas: Spin up unique AI callers with specific goals (e.g., "angry customer," "confused senior") to dynamically pressure-test your bot's conversational logic.

Concurrent Call Simulation: Spawn N simultaneous inbound calls to hammer your system and catch latency or state-management failures at scale.

Full Transcript Capture: Every AI-to-AI dialogue is saved to transcripts/{call_sid}.txt.

Call State Tracking: JSON snapshots of each call's lifecycle in data/calls/{call_sid}.json.

Automated Evaluation: Post-run analysis scores the target bot on quality, coherence, and goal-completion against the adversarial caller.

## Architecture

```
run_calls.py
    │
    ├─ Twilio API ──► dials your Twilio number
    │                       │
    │               Twilio webhook ──► ngrok tunnel
    │                                       │
    │                               localhost:5000
    │                               (server.py)
    │                                   │
    │                           /voice endpoint
    │                           (your AI agent)
    │
    └─ transcripts/ + data/calls/
            │
    analyze_calls.py ──► QA report
```

---

## Prerequisites

- Python 3.8+
- [Twilio account](https://twilio.com) (free tier works)
- [ngrok](https://ngrok.com) — `brew install ngrok` or download from ngrok.com

---

## Setup

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Configure environment variables

Create a `.env` file in the project root:

```env
PUBLIC_BASE_URL=https://your-ngrok-url.ngrok.io
TWILIO_ACCOUNT_SID=your_account_sid
TWILIO_AUTH_TOKEN=your_auth_token
TWILIO_FROM_NUMBER=+1234567890
OPENAI_API_KEY=your_openai_key
```

Also add your test line number to `config.py`.

<details>
<summary><strong>Where to find your Twilio credentials</strong></summary>

1. Log in to the [Twilio Console](https://www.twilio.com/console)
2. Your **Account SID** and **Auth Token** are on the main dashboard
3. Go to **Phone Numbers → Active Numbers** to find your number (or buy one for ~$1/month)

</details>

<details>
<summary><strong>Where to find your OpenAI API key</strong></summary>

1. Visit [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. Click **Create new secret key** and copy it immediately — it won't be shown again

</details>

### 3. Start the server

```bash
python server.py
```

The server starts at `http://localhost:5000`.

### 4. Open an ngrok tunnel

In a separate terminal:

```bash
ngrok http 5000
```

Copy the **Forwarding URL** (e.g., `https://abc123.ngrok.io`) and paste it as `PUBLIC_BASE_URL` in your `.env`.

### 5. Run test calls

```bash
python3 -u run_calls.py --n 5   # fires 5 concurrent calls
```

Change `--n` to however many simultaneous calls you want to simulate. Watch your server logs as calls roll in.

### 6. Analyze transcripts

Once calls complete:

```bash
python3 analyze_calls.py
```

This reads everything in `transcripts/` and produces a QA breakdown — response quality, turn coherence, drop-offs, and any error patterns.

---

## Output Files

```
data/calls/{call_sid}.json     ← call state + metadata per call
transcripts/{call_sid}.txt     ← full conversation transcript per call
```

---

## Tips

- **Start small.** Run `--n 1` first to confirm the pipeline is working end-to-end before blasting concurrent calls.
- **ngrok restarts reset your URL.** Update `PUBLIC_BASE_URL` each time you restart the tunnel.
- **Free Twilio accounts have concurrency limits.** Upgrade or use a test project if you're running large batches.
- **Keep the server running** between `run_calls.py` and `analyze_calls.py` — some transcript writes may still be in-flight.

---

## Contributing

PRs welcome. If you're adding a new analysis metric to `analyze_calls.py` or a new call scenario to `run_calls.py`, please include a sample transcript in `examples/` so others can see what it's testing.

---

## License

MIT
