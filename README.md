# Retail AI Insights Assistant

A small AI-powered tool built on top of a Walmart weekly sales dataset (45 stores, Feb 2010–Oct 2012). Instead of reading a static dashboard, a business user can type a plain-English question — "which stores are underperforming?", "what does the holiday sales lift suggest for staffing?" — and get an answer grounded in the actual data.

**Live demo:** https://maurishi09.github.io/retail-ai-insights/

## Why I built this

My background is in data analytics — SQL, Python, Power BI, dashboards. This project was me pushing one step further: not just analyzing data, but building something a non-technical stakeholder could actually use to ask questions of it, powered by an AI model.

## How it works

1. **Pre-aggregation, not raw data.** The underlying dataset has 6,435 store-week records. Rather than sending that to the model, I summarized it first in Python — per-store averages, monthly trends, holiday effects — so the AI works with clean business metrics instead of raw rows. This keeps the model's context small, fast, and cheap to run.
2. **A constrained system prompt.** The model is told exactly what role to play (a retail analytics consultant), what data it's allowed to use, and to say plainly when a question can't be answered from the data provided — rather than guessing.
3. **A standard API call, later removed for public deployment.** See "Safety-relevant design decisions" below.

## Safety-relevant design decisions

Building this surfaced two problems worth being explicit about, since they're the reason I got interested in AI safety specifically rather than just AI capability:

**1. Credential exposure risk.** The initial version called the LLM API directly from the browser. That works fine in a private/sandboxed environment, but breaks the moment the app is hosted publicly — a client-side API key can be read by anyone who opens developer tools. I removed the live call entirely for the public deployment rather than ship something that looked functional but leaked a credential. A production version would route this through an authenticated backend, never exposing the key client-side.

**2. Preventing confident wrong answers.** The system prompt explicitly constrains the model to only use the aggregated data provided, and to say plainly when a question can't be answered from that data — rather than generating a plausible-sounding but fabricated figure. This matters more than it might seem: a business user asking "which stores are underperforming" has no way to independently verify the AI's answer against the underlying dataset in the moment. If the model hallucinates a number with the same confident tone as a correct one, the user has no signal to catch it. Constraining the model to admit uncertainty is a small design choice with real consequences if skipped.

Both of these are narrow, project-level examples — but they're the kind of problem I want to understand at a deeper, more general level: how do we build AI systems that fail safely and visibly, rather than failing silently in ways users can't detect?

## Process

I'm not a trained developer — I don't have formal HTML/JavaScript background. I built this using AI-assisted development: I defined the product requirements, the data handling approach, and the constraints on the AI's behavior; the AI handled the syntax. I reviewed and tested the output, including validating the embedded dataset for errors before deployment.

## Stack

HTML / CSS / vanilla JavaScript · Chart.js for the trend chart · LLM API for the natural-language layer
