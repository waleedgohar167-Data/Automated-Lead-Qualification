# Automated Lead Qualification and Routing Pipeline

An intelligent, AI-driven workflow that automatically qualifies inbound inquiries, parses critical lead data, routes prospects based on budget tiers, and dispatches targeted email responses.

## The Business Problem
Service-based businesses and digital marketing agencies lose critical hours manually reading, evaluating, and responding to contact forms. This manual triage creates response delays, which severely drops conversion rates for high-value leads. At scale, human qualification becomes a bottleneck, costing thousands of dollars in lost labor and missed premium prospects.

## The Solution
This automated pipeline captures inbound inquiries instantly, utilizing an LLM to evaluate the prospect's budget and intent. It categorizes the lead into Hot, Warm, or Cold tiers, logs the structured data directly into a CRM database, and immediately fires off a tailored, tier-specific email response. The client receives a fully automated, zero-touch sales triage system that operates 24/7.

## Workflow Architecture
*   **Webhook (Trigger):** Acts as the immediate entry point, listening for inbound JSON payloads from website forms or external applications.
*   **OpenAI (Extraction & Logic):** Evaluates the raw text, extracts the Name, Email, and Budget, and uses predefined rules to classify the tier.
*   **Switch (Routing):** Acts as the traffic controller, using precise rule matching (`.toLowerCase()` and `contains` logic) to route data down the appropriate path based on the AI's classification.
*   **Airtable (Database):** Serves as the lightweight CRM, permanently logging the prospect's details and maintaining integer-strict budget records.
*   **Send Email (Action):** Dispatches immediate, personalized follow-ups tailored to the lead's financial qualification.

## Tech Stack
*   **n8n:** The core workflow automation engine handling routing, logic, and API orchestration.
*   **GPT-3.5-TURBO:** The natural language processing engine responsible for data extraction and contextual budget classification.
*   **Airtable:** The cloud-based relational database acting as the client-facing CRM dashboard.
*   **SMTP:** The email transmission protocol used to ensure reliable, immediate outbound communication without OAuth expiration issues.

## Setup Instructions
1. Clone this repository and import the `workflow.json` file into your n8n instance.
2. Create an Airtable base with a table named "Leads". You must create exactly four columns: `Name` (Single line text), `Email` (Email), `Budget` (Number/Currency), and `Tier` (Single line text).
3. Connect your Airtable Personal Access Token in the n8n credentials menu.
4. Add your OpenAI API key to the n8n credentials. Ensure you have access to the `gpt-3.5-turbo` model.
5. Set up the SMTP node using your email provider's host (e.g., `smtp.gmail.com`), port (465), and an App Password (do not use your standard email login password).

## Example Inputs and Outputs
**Scenario 1: Hot Lead**
*   **Input:** "I urgently need a workflow. Budget is $8,000."
*   **LLM Output:** Tier: Hot, Budget: 8000
*   **Action:** Logged to Airtable. VIP fast-track email sent immediately.

**Scenario 2: Warm Lead**
*   **Input:** "Looking for automation help, budget is $2,500."
*   **LLM Output:** Tier: Warm, Budget: 2500
*   **Action:** Logged to Airtable. Standard onboarding email sent outlining mid-tier services.

**Scenario 3: Cold Lead**
*   **Input:** "Need a quick fix for $400."
*   **LLM Output:** Tier: Cold, Budget: 400
*   **Action:** Logged to Airtable. Polite rejection/down-sell email sent with links to DIY resources.

## Known Limitations and Future Improvements
1.  **Model Upgrade:** Upgrading from GPT-3.5-TURBO to GPT-4o would significantly improve classification accuracy for highly ambiguous, multi-paragraph inquiries, and reduce hallucinations when extracting poorly formatted contact data.
2.  **Calendar Integration:** The current system replies to Hot leads but does not secure a meeting. Adding a Calendly routing node for Hot leads would decrease friction.
3.  **Missing Data Handling:** If a user omits their budget entirely, the current prompt may fail or default. Future iterations require a specific error-handling path for missing variables.