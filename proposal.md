# Pre-Meeting Brief: Architectural Proposal

## 1. Approach and Workflow

### Trigger Mechanism
To detect a new meeting with a company that has not been engaged within the last three months, the system will integrate with the investor's Calendar (e.g., Google Calendar) and the CRM (Attio).
- **Webhook/Polling:** A webhook will monitor the Calendar for new meeting events.
- **Entity Extraction:** For every new meeting, the system will extract participant domains and company names.
- **CRM Check:** The system queries Attio to verify the last engagement date for the extracted company. If the company does not exist in the CRM or the last interaction was over 3 months ago, the trigger fires and initiates the pipeline.

**Illustrative Code Snippet (Trigger Mechanism):**
```python
from datetime import datetime, timedelta

def handle_new_meeting_webhook(event):
    company_domain = extract_domain(event.attendees)

    # Query CRM (Attio)
    last_interaction = attio_client.get_last_interaction(company_domain)

    three_months_ago = datetime.now() - timedelta(days=90)

    if last_interaction is None or last_interaction < three_months_ago:
        print(f"Triggering brief generation for {company_domain}")
        initiate_brief_pipeline(company_domain, event)
    else:
        print(f"Company {company_domain} engaged recently. Skipping.")
```

### Data Integration
The system will employ a modular data orchestration pipeline to gather context from multiple sources concurrently:
- **CRM (Attio):** Fetch historical interaction notes, internal diligence, and current pipeline stage.
- **3rd Party APIs (Specter, Crunchbase):** Retrieve company demographics, funding raised, investor syndicates, and key personnel (founders' background).
- **Web Scraping/Search APIs (e.g., Exa):** Gather recent news, media appearances, podcasts, and deep market context.
- **Data Merging:** The gathered data is standardized into a unified JSON schema, cleaning up duplicates and ensuring only the most up-to-date and relevant information is passed to the synthesis engine.

**Illustrative Code Snippet (Data Integration):**
```python
import asyncio

async def fetch_all_data(company_domain):
    # Concurrent data fetching from multiple sources
    crm_task = asyncio.create_task(fetch_from_attio(company_domain))
    crunchbase_task = asyncio.create_task(fetch_from_crunchbase(company_domain))
    news_task = asyncio.create_task(fetch_recent_news(company_domain))

    crm_data, cb_data, news_data = await asyncio.gather(
        crm_task, crunchbase_task, news_task
    )

    unified_context = {
        "demographics": cb_data.get("demographics"),
        "funding": cb_data.get("funding_raised"),
        "key_personnel": cb_data.get("founders"),
        "prior_history": crm_data.get("notes"),
        "recent_news": news_data.get("articles")
    }
    return unified_context
```

### Synthesis
The unified data object is fed into a large language model (LLM) pipeline (e.g., GPT-4o, Claude 3.5 Sonnet) configured with a strict system prompt to ensure concise and structured outputs.
- **Prompting Strategy:** The LLM is instructed to generate a brief tailored for swift readability, structured according to the desired sections: Company Overview, Key Personnel, Prior Engagement History, Industry & Market Deep Dives, Key Questions, and Media Appearances.
- **Structuring:** The output will be requested in Markdown format to support clean rendering with clear headers and bullet points.

**Illustrative Code Snippet (Synthesis):**
```python
import openai

def generate_brief(context):
    prompt = f"""
    You are an expert investment analyst. Generate a concise pre-meeting brief
    based on the following data. Focus on swift readability.

    Context Data: {context}

    Required Sections:
    1. Company Overview
    2. Key Personnel
    3. Prior Engagement History
    4. Industry & Market Deep Dives
    5. Key Questions (propose 3-5 questions)
    6. Media Appearances
    """

    response = openai.ChatCompletion.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content
```

### Distribution
Once the brief is generated, the system automates its delivery to ensure it is seamlessly integrated into the investor's workflow:
- **Calendar API Integration:** The generated brief is automatically appended to the description of the original meeting event or attached as a document link (e.g., a shared Google Doc).
- **Daily Agenda Summary:** Alternatively, an aggregation script runs early morning, compiling all the generated briefs for the day's meetings and emailing or sending a Slack message to the investor with their daily agenda.

**Illustrative Code Snippet (Distribution):**
```python
def distribute_brief(event_id, generated_brief):
    # Example: Update Google Calendar event description
    calendar_service = get_calendar_service()
    event = calendar_service.events().get(calendarId='primary', eventId=event_id).execute()

    existing_description = event.get('description', '')
    new_description = f"{existing_description}\n\n=== PRE-MEETING BRIEF ===\n{generated_brief}"

    event['description'] = new_description
    calendar_service.events().update(calendarId='primary', eventId=event_id, body=event).execute()
    print("Brief successfully attached to the daily agenda event.")
```

---

## 2. Public Evidence of Building with Frontier Models
You can review my public GitHub profile for evidence of my work building with frontier models and complex automation systems:
- **GitHub Profile:** [github.com/gpandi007](https://github.com/gpandi007)
- **Relevant Side Project (AI & Automation):** [AI-IT-Helpdesk-Agent-Multi-Tool](https://github.com/gpandi007/AI-IT-Helpdesk-Agent-Multi-Tool-) – This repository demonstrates my ability to integrate large language models with multi-tool agentic workflows to solve complex reasoning tasks and automate support pipelines.
- **Additional Experience:** I have also built projects involving NLP, such as `ChatWithCSVFilesLLM-` and `Simple-NLTK-Chatbot`, showcasing a deep background in conversational AI and data analysis.

## 3. Reference from a Prior Engagement
*[Please insert the Name, Contact Info, and Context of a prior manager or colleague here. For example:]*
- **Name:** [Manager's Name], [Title] at [Company]
- **Email:** [Manager's Email]
- **Context:** [Manager's Name] was my manager when I was a Lead Engineer. They can speak to my ability to architect complex LLM-driven applications, my proficiency with data integration pipelines, and my track record of leading teams to deliver high-impact automation products.
