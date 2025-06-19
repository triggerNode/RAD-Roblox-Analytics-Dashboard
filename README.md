# RAD-Roblox-Analytics-Dashboard
Technical Document: Roblox Analytics Dashboard (Stage 1)
Overview & Context
This document serves as a comprehensive guide for building the MVP version (Stage 1) of a Roblox Analytics Dashboard using OpenAI Codex for code generation. The main goal is providing real-time analytics, funnel visualization, and economy monitoring for solo and small Roblox developers, addressing critical pain points caused by delays and limited insights in existing solutions (such as GameAnalytics and Roblox built-in analytics).
Stage 1 Goals
Instant Event Timeline: Real-time tracking (<5 sec latency)


Funnel Analysis: Immediate visualization of player drop-offs


Economy Monitoring: Live tracking of currency sources and sinks


Easy Setup: Less than 5-minute integration via Roblox Studio


User Flow
Developer Onboarding:


Signs up and creates a project


Receives a unique API Key


Copies the Lua Module into Roblox Studio with API Key


Event Tracking:


Roblox game sends events via HttpService:PostAsync


n8n Webhook receives and processes events


Data stored in Supabase and broadcasted via Supabase realtime


Analytics Dashboard:


Developer views real-time data via Next.js frontend


Accesses Live Feed, Funnel, and Economy visualizations


Technical Architecture
Frontend (Next.js + Tailwind CSS)
Minimalist, clean UI (white background, muted palette, clear typography)


Live feed: displays recent events, searchable/filterable


Funnel tab: intuitive, real-time funnel chart visualization


Economy tab: clear charts of currency flows and recent purchases


Backend (Supabase & n8n)
Supabase PostgreSQL: structured data storage with Row-Level Security


Supabase realtime channels for broadcasting new events


n8n automation workflows for data processing:


Webhook receives JSON events


JavaScript function nodes validate/enrich data


Data insertion into Supabase tables


Database Schema (Supabase)
projects: id, name, api_key_hash, owner_id


events: id, project_id, player_id, event_type, ts, json


funnels: id, project_id, name, steps


purchases: id, project_id, player_id, item, price_robux, ts


currency_flows: id, project_id, player_id, delta, source_sink, balance_after, ts


daily_aggregates: project_id, date, active_users, new_users, d1_retention, total_revenue


n8n Workflow Structure
ingestEvents Workflow
Webhook Node: Receives POST requests from Roblox games.


JavaScript Transform Node: Validates API-Key (HMAC), enriches data (session tracking, funnel steps).


Supabase Insert Node: Bulk insertion into the events table.


dailyAgg Workflow
Cron Trigger Node: Daily aggregation at 05:00 UTC.


SQL Query Node: Aggregates daily analytics.


Insert/Upsert Node: Updates daily_aggregates table.


Conditional Node: Alerts via Discord webhook if key metrics fall below thresholds.


Lua Module Integration (Roblox)
Single Module (AnalyticsModule.lua) integrated into Roblox Studio.


Handles event queuing, batching (20 events or 5-second intervals), and HTTP retries.


Requires minimal scripting knowledge from the developer.


Hosting & Infrastructure
DigitalOcean Droplet: Docker-compose setup for n8n.


Supabase: Managed database and authentication.


GitHub Actions: CI/CD pipeline to auto-deploy updates.


Security & Scalability
Authentication: Supabase email-magic-link, secure API keys.


Rate Limiting: Implemented via Nginx reverse proxy for n8n.


Data Isolation: Row-Level Security in Supabase.


Scaling Path: Easy migration to higher-capacity plans and horizontally scaling n8n services.


Prompting Guide for Codex
Clearly specify one task per prompt.


Example Prompt:


"Generate an Express route /webhook/:projectKey that validates HMAC SHA256, handles JSON input, and inserts data into Supabase. Include error handling."


"Create a Roblox Lua module that queues analytics events, sending them via HttpService with batching every 5 seconds or every 20 events."


"Write SQL for computing Day-1 retention from event data in Supabase."


Codex should always be guided by clearly defined tasks and the schema provided. Outputs should be tested directly after generation to ensure correctness.
UX/UI Guidelines
Replicate the minimalist, intuitive UI from provided GameAnalytics images.


Maintain consistent color, typography (Inter font), and responsive design.


Clear visual distinction for interactive elements (charts, dropdowns, event lists).


Deliverables & Validation
Functional Lua integration module.


Operational n8n workflows handling real-time event ingestion.


Fully structured Supabase database with enforced RLS.


User-ready frontend built with Next.js, providing clear, actionable analytics.


Initial user feedback via dedicated Discord community and integrated feedback forms.


Final Notes for Codex Prompting
Codex should treat this document as the foundational reference for all coding activities in Stage 1. Given the novice context of the user prompting Codex, natural language instructions should produce clear, concise, and immediately deployable code snippets, always returning to this guide for reference on architectural context and detailed technical specifications.

