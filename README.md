# Feedback-Intelligence-Pipeline

An automated data processing and intelligence engine designed for SaaS technical consulting and product leadership teams. The pipeline bridges pre-sales friction (Salesforce Closed-Lost Deals) and post-sales friction (Zendesk Support Tickets) by aggregating raw feedback, normalizing unstructured complaints into canonical categories, extracting verbatim quotes with metadata, and surfacing cross-segment summaries to Slack.


🚀 **Deployment & Setup Guide**
1. Database Setup (Google Sheets)  
─ Create a spreadsheet named VoC_Feedback_Database.  
─ Rename the active sheet tab to Feedback_Log.  
─ Set Row 1 headers: Date, Source_Type, Deal_Size_ARR, Industry, Raw_Feedback_Text, AI_Processed, Normalized_Summary, Category, Direct_Quote.  
   ─ Note: The database utilizes a boolean flag (AI_Processed?) to enable incremental batching, ensuring that scheduled executions only pass unanalyzed rows to the LLM context window.
3. Pipeline Ingestion (Zapier, Salesforce, Zendesk)  
─ Salesforce: Configure a Record-Triggered Flow on Opportunity where StageName equals Closed Lost. Pass fields matching salesforce_payload.json.  
─ Zendesk: Create an Admin Webhook targeting your Zapier endpoint, triggered on ticket status Solved. Use body from zendesk_payload.json.  
─ Zapier: Insert the clean_input_text.js action step before writing to Google Sheets to ensure text length validation and initial PII redaction.  
4. Execution Schedule (Google Apps Script)  
─ Open Extensions $\rightarrow$ Apps Script in your sheet.  
─ Add voc_pipeline_engine.gs and slack_notifier.gs to the project.  
─ Set up a Time-driven Trigger: runVoCFeedbackPipeline $\rightarrow$ Week timer $\rightarrow$ Every Monday $\rightarrow$ 8am to 9am.  
  
  
🔑 **Environment & Credentials**  
Configure the following Script Properties within Google Apps Script (Extensions $\rightarrow$ Apps Script $\rightarrow$ Project Settings $\rightarrow$ Script Properties):      
____________________________________________________________________________________________  
  **Property Key** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - **Description** - &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **Example / Placeholder**      
  GEMINI_API_KEY &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Google AI Studio API Key - &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{GEMINI_API_KEY}      
  SLACK_WEBHOOK_URL - Incoming URL for channel - {SLACK_WEBHOOK_URL}    
____________________________________________________________________________________________
    

      
📁 **Repository Structure**   
  
├── src/  
│   ├── zapier/  
│   │   └── clean_input_text.js          # Ingestion sanitization & PII scrubber  
│   ├── payloads/  
│   │   ├── salesforce_payload.json      # Salesforce record-triggered flow payload  
│   │   └── zendesk_payload.json         # Zendesk webhook notification body  
│   └── google-apps-script/  
│       ├── voc_pipeline_engine.gs       # Core Apps Script logic & Gemini API handler  
│       └── slack_notifier.gs            # Slack Block Kit layout & webhook dispatcher  
├── docs/  
│   └── prompt_log.md                    # Historical prompt sequence used to design system  
└── README.md                            # Main technical documentation  
  
  
  
  
💻 **Code & Configuration Reference**
1. Zapier Input Sanitization
src/zapier/clean_input_text.js

2. Salesforce Webhook Payload
src/payloads/salesforce_payload.json

3. Zendesk Trigger Payload
src/payloads/zendesk_payload.jsonc

4. Core Pipeline & Gemini Integration
src/google-apps-script/voc_pipeline_engine.gs

5. Slack Block Kit Notifier
src/google-apps-script/slack_notifier.gs

