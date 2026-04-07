# ScamShield Bot

ScamShield Bot is an AI-assisted scam awareness chatbot project designed for Malaysian users. The chatbot helps users assess suspicious scam situations, messages, and links across several common scam categories using a hybrid approach:

```text
AI-assisted interpretation + production-rule risk assessment + practical safety advice
```

The project is designed for implementation in Botpress Studio and is supported by scam-specific knowledge base files, flowcharts, methodology documentation, and a Botpress build guide.

## Project Purpose

The main goal of ScamShield Bot is to guide users through structured scam checks and provide a preliminary risk assessment. The bot is not intended to verify links, websites, companies, or individuals with absolute certainty. Instead, it identifies warning signs and gives safety-focused next steps based on curated scam-prevention knowledge and explicit assessment rules.

## Scam Types Covered

The chatbot covers six scam categories:

- Phishing links and fake account-verification requests
- Fake parcel-delivery messages
- Fake bank alerts and bank-impersonation scams
- Fake job offers
- Investment scams
- Romance scams

The first three scam types support message/link input because they commonly involve a suspicious SMS, email, WhatsApp message, URL, phone number, or delivery/banking alert. The remaining scam types use guided questions because they usually depend more on user context and conversation history.

## Repository Contents

```text
knowledge_base/
  Scam-specific knowledge base files and input-pattern research files.

BOTPRESS_BUILD_GUIDE.md
  Step-by-step Botpress Studio implementation guide.

Methodology.md
  Project methodology, knowledge-base construction notes, and question/source mapping.

scamshield_resources.md
  Source bank and resource mapping used to support the knowledge base.

*.drawio
  Flowchart and technical architecture diagrams for the chatbot design.

package.json
  Minimal Node package metadata used by the project folder.
```

## Key Knowledge Base Files

The main scam knowledge files are:

- `knowledge_base/kb_01_phishing.txt`
- `knowledge_base/kb_02_parcel_scam.txt`
- `knowledge_base/kb_03_bank_alert.txt`
- `knowledge_base/kb_04_job_scam.txt`
- `knowledge_base/kb_05_investment_scam.txt`
- `knowledge_base/kb_06_romance_scam.txt`
- `knowledge_base/kb_07_redflag_table.txt`
- `knowledge_base/kb_general_faq.txt`

Additional input-pattern research files were created for the three message/link-based scam types:

- `knowledge_base/kb_input_patterns_01_phishing.txt`
- `knowledge_base/kb_input_patterns_02_parcel_scam.txt`
- `knowledge_base/kb_input_patterns_03_fake_bank_alert.txt`

## Botpress Build Approach

The current Botpress implementation plan uses:

- `Standard Node` for controlled conversation flow
- `Capture Information` cards for user input
- `Single Choice` for the main scam type menu
- `Raw Input` for pasted suspicious messages or links
- `Confirmation` for yes/no action questions
- `Autonomous Node` for AI-assisted result generation using strict production-rule instructions
- `workflow.transition` to move from the result node to the follow-up menu step

The recommended result flow is:

```text
Scam branch
-> Result node
-> Check_Another_Scam
-> Scam Type Menu or End
```

See `BOTPRESS_BUILD_GUIDE.md` for the full node names, card types, prompts, variables, and test cases.

## Assessment Design

The chatbot uses a rule-informed risk model. For input-based scams, the user first pastes the suspicious message or link, then answers a small number of action questions that the AI cannot reliably infer from the message alone.

Example phishing flow:

```text
Phishing_Intro
-> Phishing_Input_Text
-> Phishing_Action_Questions
-> Phishing_Result
-> Check_Another_Scam
```

The AI result node is instructed to apply a production-rule rubric and return a risk level:

- Low
- Medium
- High
- Critical

The bot must not claim that a link, phone number, company, or person is definitely safe. The output is a preliminary AI-assisted rule-based assessment.

## Flowcharts

The repository includes multiple draw.io diagrams:

- `simple_chatbot_flow.drawio`
- `chatbot_menu_flowchart.drawio`
- `ai_chatbot_flow_v2.drawio`
- `ai_chatbot_flow_v3.drawio`
- `ai_chatbot_flow_v3_technical.drawio`
- `ai_chatbot_flow_v4.drawio`
- `scamshield_chatbot_flow.drawio`

These files document the chatbot flow from simple menu routing to more technical assessment logic.

## How To Use This Repository

1. Open `BOTPRESS_BUILD_GUIDE.md`.
2. Recreate the Botpress nodes and cards in Botpress Studio.
3. Upload or reference the knowledge base files where appropriate.
4. Use the provided test cases to validate each scam branch.
5. Refer to `Methodology.md` and `scamshield_resources.md` for report writing and source justification.

## Safety Limitations

ScamShield Bot provides a preliminary risk assessment only. It does not:

- perform live website verification
- guarantee that a link is safe
- guarantee that a company, job, investment, or person is legitimate
- replace official reporting or professional advice
- ask users to provide OTPs, passwords, PINs, banking details, or sensitive personal credentials

Users should verify suspicious messages through official websites, official apps, or official hotlines, and should report urgent scam incidents through the relevant Malaysian channels.

## Development Status

This repository currently contains the documentation, knowledge base, flowcharts, and Botpress build guide for the ScamShield Bot project. The executable Botpress bot is expected to be built in Botpress Studio by following `BOTPRESS_BUILD_GUIDE.md`.
