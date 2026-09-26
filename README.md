# AI Salon Support Bot (n8n)

A Telegram bot that answers customer questions for a beauty salon using
retrieval-augmented generation (RAG) — it searches the salon's FAQ knowledge
base for an answer instead of guessing, and hands off to a human when it
isn't confident.

## What it does
1. Customer messages the Telegram bot
2. An AI Agent searches a vector-embedded FAQ knowledge base for relevant context
3. It answers using only what it finds — never guesses prices, hours, or services
4. If nothing relevant is found, it tells the customer a team member will follow up, and pings Slack so a human actually sees it
5. Includes a fallback AI model in case the primary model is rate-limited or briefly unavailable

## Stack
n8n · Google Gemini (chat + embeddings) · Simple Vector Store · Google Sheets · Telegram · Slack

## Two workflows
- `glow-salon-index-faqs.json` — run once (or whenever the FAQ sheet changes) to load Q&A pairs from Google Sheets into the vector store
- `glow-salon-telegram-bot.json` — the live bot: listens for Telegram messages, retrieves relevant FAQs, answers, and escalates to Slack when unsure

## Setup
1. Import both workflow JSONs into n8n
2. Add credentials: Google Gemini API key, Google Sheets OAuth, Telegram Bot API token, Slack OAuth
3. Create a Google Sheet with `Question` and `Answer` columns and point the indexing workflow at it
4. Replace `YOUR_SHEET_ID_HERE` in the indexing workflow with your Sheet's ID
5. Replace `YOUR_SLACK_CHANNEL_ID` in the Telegram bot workflow with your Slack channel
6. Run the indexing workflow once to populate the knowledge base
7. Activate the Telegram bot workflow

## Notes
- Runs on Gemini's free tier for this demo. A production deployment would use the paid tier (roughly $0.75 / $3.75 per million input/output tokens), which comfortably covers real business volume for a few dollars a month.
- The vector store is in-memory, which resets if the n8n instance restarts — fine for a demo; a production version would use a persistent store like Supabase or Pinecone.
- Configured with a fallback AI model to handle rate limits or brief provider outages automatically.

## Result
Customers get accurate answers in seconds, hallucination-prone questions
get escalated instead of guessed at, and a human is looped in automatically
when the bot doesn't know something.
