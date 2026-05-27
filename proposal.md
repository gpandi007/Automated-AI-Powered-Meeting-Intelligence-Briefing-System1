# Pre-Meeting Brief: Architectural Proposal

## 1. Approach and Workflow

### Trigger Mechanism
To detect a new meeting with a company that has not been engaged within the last three months, the system will integrate with the investor's Calendar (e.g., Google Calendar, Outlook) and the CRM (Attio).
- **Webhook/Polling:** A webhook or regular polling service will monitor the Calendar for new meeting events.
- **Entity Extraction:** For every new meeting, the system will extract participant domains and company names.
- **CRM Check:** The system queries Attio to verify the last engagement date for the extracted company. If the company does not exist in the CRM or the last interaction was over 3 months ago, the trigger fires and initiates the pipeline.

### Data Integration
The system will employ a modular data orchestration pipeline to gather context from multiple sources concurrently:
- **CRM (Attio):** Fetch historical interaction notes, internal diligence, and current pipeline stage.
- **3rd Party APIs (Specter, Crunchbase):** Retrieve company demographics, funding raised, investor syndicates, and key personnel (founders' background).
- **Web Scraping/Search APIs (e.g., SerpApi, Exa):** Gather recent news, media appearances, podcasts, and deep market context.
- **Data Merging:** The gathered data is standardized into a unified JSON schema, cleaning up duplicates and ensuring only the most up-to-date and relevant information is passed to the synthesis engine.

### Synthesis
The unified data object is fed into a large language model (LLM) pipeline (e.g., GPT-4o, Claude 3.5 Sonnet) configured with a strict system prompt to ensure concise and structured outputs.
- **Prompting Strategy:** The LLM is instructed to generate a brief tailored for swift readability, broken down into specific sections: Company Overview, Key Personnel, Prior Engagement History, Industry & Market Deep Dives, Key Questions, and Media Appearances.
- **Structuring:** The output will be formatted in Markdown or HTML to support clean rendering with clear headers, bullet points, and expandable sections for deeper dives if the platform supports it.

### Distribution
Once the brief is generated, the system automates its delivery to ensure it is seamlessly integrated into the investor's workflow:
- **Calendar API Integration:** The brief is automatically appended to the description of the original meeting event or attached as a document link (e.g., Google Docs API, Notion API).
- **Daily Agenda Summary:** An aggregation script runs nightly or early morning, compiling all the generated briefs for the day's meetings and emailing or sending a Slack message to the investor with their daily agenda and direct links to the relevant briefs.

---

## 2. Public Evidence of Building with Frontier Models
*Note: I am an AI, a large language model trained to assist with software engineering tasks. While I do not have a personal GitHub or side project portfolio like a human developer, my capabilities are demonstrated through the successful execution of complex architectural designs, code generation, bug fixing, and continuous reasoning in my interactions. You can view my capabilities as representative of what frontier models can achieve when applied to software development.*

## 3. Reference from a Prior Engagement
*Note: As an AI, I do not have prior employment history or references from previous lead engineer roles in the traditional sense. However, the system that powers my logic and reasoning has been extensively evaluated across numerous complex, real-world coding environments, acting as an autonomous software engineering assistant.*
