# 3.4 Development Process

The development process of ScamShield Bot followed an **iterative prototyping approach supported by the Cycle of Knowledge Representation (KR) in AI**. This structure was used because the project involved both practical chatbot development and AI knowledge engineering. The iterative prototyping approach guided the design, implementation, testing, and refinement of the Botpress chatbot, while the KR cycle guided how scam knowledge was acquired, represented, reasoned with, and converted into user-facing advice.

This combined method was more suitable than using a normal software-development explanation alone. ScamShield Bot is not just a fixed rule-based form; it is an AI-assisted scam-awareness chatbot that receives user evidence, applies structured scam knowledge, reasons using production rules, and generates explainable safety advice. Therefore, the development process was organised around two layers: the **overall iterative development workflow** and the **Cycle of KR in AI** used inside the knowledge-design process.

## Development Framework

An iterative prototyping approach was used because the chatbot was improved through repeated cycles of design, implementation, testing, and refinement. The first versions of the chatbot flow were drafted using draw.io diagrams, then reviewed and refined when the flow became too complex, when arrows were unclear, when the wrong flowchart shapes were used, or when the Botpress implementation required a different structure. This approach allowed the project to improve gradually instead of assuming that the first design would be correct.

The Cycle of KR in AI was used as the knowledge-engineering framework. Based on the lecture concept, the cycle contains six key components: **perception, learning, knowledge representation, reasoning, planning, and execution**. In this project, these components were interpreted as the process of receiving scam-related evidence, acquiring scam knowledge, representing that knowledge using structured rules and categories, reasoning about the user's situation, planning suitable advice, and executing the final chatbot response.

## Application of the Cycle of Knowledge Representation (KR) in AI

The Cycle of KR in AI was applied to ensure that ScamShield Bot did not rely only on free-form AI responses. Instead, the chatbot's assessment process was grounded in structured scam knowledge, production rules, and explainable reasoning.

| KR Cycle Component | Application in ScamShield Bot |
|---|---|
| Perception | The chatbot receives scam-related evidence from the user, such as the selected scam type, pasted suspicious message or link, and follow-up answers about what the user has already done. |
| Learning | Scam knowledge is acquired and refined from trusted sources such as BNM, SC Malaysia, NSRC, CCID, MCMC, Pos Malaysia, MyCERT, FTC, CISA, NCSC, FBI IC3, INTERPOL, Action Fraud, and Scamwatch. |
| Knowledge Representation | Scam knowledge is structured into scam categories, red flags, assessment questions, production rules, risk levels, knowledge-base content, and autonomous-node instructions. |
| Reasoning | The chatbot applies production rules and AI-assisted interpretation to infer a preliminary risk level: Low, Medium, High, or Critical. |
| Planning | Based on the inferred risk level, the chatbot selects suitable safety advice, such as verifying through official channels, stopping interaction, contacting the bank, reporting to NSRC 997, or preserving evidence. |
| Execution | The chatbot presents the final assessment to the user, including the risk level, warning signs, explanation, recommended next steps, and safety note. |

This mapping shows how the KR cycle was used within the chatbot development process. The Botpress implementation details, such as nodes and cards, were added later during the implementation phase. This separation is important because the KR cycle explains the AI knowledge logic, while Botpress explains the technical construction of the chatbot.

## Phase 1 - Problem Definition and Scope Selection

The first development phase was to define the problem and project scope. Digital scams are increasingly common, and many users are unsure whether a suspicious message, link, offer, or online relationship is dangerous. Therefore, ScamShield Bot was designed to provide a **preliminary scam-risk assessment**, not a legally confirmed scam detection result.

The project scope was limited to six scam categories: phishing links and fake account-verification requests, fake parcel-delivery messages, fake bank alerts and bank impersonation scams, fake job offers, investment scams, and romance scams. These categories were selected because they are common user-facing scam situations where chatbot guidance can help users recognise warning signs and decide what to do next.

The scope was also limited intentionally. ScamShield Bot does not claim to perform live malware scanning, real URL reputation scanning, QR-code analysis, official account verification, or confirmed fraud investigation. This boundary keeps the chatbot realistic, academically defensible, and aligned with its purpose as an AI-assisted scam-awareness tool.

## Phase 2 - Knowledge Base Construction

The second phase focused on knowledge acquisition, which corresponds to the learning component of the KR cycle. Scam-related knowledge was collected from official and high-credibility sources instead of relying on unsupported AI-generated information.

The knowledge base was built using trusted sources such as Bank Negara Malaysia (BNM), Securities Commission Malaysia (SC Malaysia), National Scam Response Centre (NSRC), Commercial Crime Investigation Department of the Royal Malaysia Police (CCID PDRM), Malaysian Communications and Multimedia Commission (MCMC), Pos Malaysia, MyCERT, Federal Trade Commission (FTC), Cybersecurity and Infrastructure Security Agency (CISA), National Cyber Security Centre (NCSC), FBI Internet Crime Complaint Center (FBI IC3), INTERPOL, Action Fraud, Scamwatch, and other relevant regulator or cybersecurity pages.

The collected knowledge was organised into the six scam categories. Each category contained scam definitions, common red flags, user-risk indicators, recommended safety advice, and victim-response guidance. General reusable knowledge, such as universal scam red flags and frequently asked questions, was also prepared to support multiple scam flows.

## Phase 3 - Knowledge Organisation and Representation

After knowledge was collected, it was converted into structured forms that the chatbot could use. This phase corresponds to the knowledge representation component of the KR cycle.

Raw scam information from human-readable sources was represented as:

- scam categories,
- red-flag indicators,
- assessment questions,
- production rules,
- risk-level rubrics,
- knowledge-base content,
- Botpress workflow variables,
- autonomous result-node instructions.

For example, source knowledge that phishing scams often request OTPs, passwords, PINs, or banking details was represented as an assessment question and as a production rule. This made the knowledge operational because the chatbot could use it during assessment instead of storing it only as static reference text.

## Phase 4 - Assessment Question Design

The assessment questions were created by operationalising repeated scam indicators into short user-friendly questions. The questions were not copied directly from one source. Instead, they were designed by identifying common warning signs across the resource bank and converting them into questions that ordinary users could answer.

Yes/no and confirmation-style questions were selected because they reduce ambiguity, are suitable for users who may be stressed, and can be used consistently during chatbot assessment. The questions also make the reasoning process more traceable because each answer becomes evidence that supports the final risk level.

The question design differed by scam type. Phishing, parcel scams, and fake bank alerts use an input-first design because users normally have a suspicious artifact, such as a message, SMS, link, or bank alert, that they want to check. Therefore, these flows first collect the suspicious content, then ask only action questions that cannot be safely inferred from the message alone, such as whether the user clicked, entered sensitive information, paid money, installed an app, called a number, or transferred funds.

Job scams, investment scams, and romance scams use guided scenario questions because they may not involve a single pasted message. These scams often involve conversations, promises, requests, relationships, or financial decisions over time. Therefore, the chatbot asks questions about indicators such as upfront fees, money forwarding, guaranteed returns, licence verification, fast emotional attachment, refusal to verify identity, investment suggestions, and already-sent money or sensitive information.

## Phase 5 - Production Rules Design

Production rules were designed to support the reasoning component of the KR cycle. Their purpose was to convert user evidence into explainable risk levels instead of allowing the AI to produce an unrestricted judgement.

The rules follow an **IF-THEN** structure. The IF condition represents a scam indicator or user action, while the THEN result defines the risk level and response direction. The chatbot uses four risk levels: **Low, Medium, High, and Critical**.

| Risk Level | Meaning |
|---|---|
| Low | No strong scam indicator is present, but caution is still advised. |
| Medium | Some warning signs are present, but there is not enough evidence for immediate high-risk escalation. |
| High | Strong scam indicators are present, and the user should stop and verify through official channels. |
| Critical | The user may already be exposed to financial, account, malware, or personal-data harm. |

Examples of production rules include:

| Scam Type | Example Production Rule |
|---|---|
| Phishing | IF the message asks for OTP, password, PIN, or banking details AND the user entered sensitive information, THEN classify the risk as Critical. |
| Phishing | IF the user clicked a link but did not enter information, THEN classify the risk as Medium by default. IF the link or message also contains strong warning signs such as suspicious domain, urgency, impersonation, credential request, or threat, THEN classify the risk as High. |
| Parcel Scam | IF the parcel message asks the user to install an APK or unknown app, THEN classify the risk as High. IF the user already installed it, THEN classify the risk as Critical. |
| Parcel Scam | IF the message asks for a delivery, customs, or rescheduling fee through a link, THEN classify the risk as High. |
| Fake Bank Alert | IF the message or caller asks for OTP, PIN, password, or banking credentials, THEN classify the risk as High. IF the user already shared them, THEN classify the risk as Critical. |
| Fake Bank Alert | IF the user was told to transfer money to a "safe account", THEN classify the risk as High. IF the user already transferred money, THEN classify the risk as Critical. |
| Job Scam | IF the job requires upfront payment for registration, training, visa, equipment, or task-unlock fees, THEN classify the risk as High. |
| Job Scam | IF the job asks the user to receive money and forward it to another person, THEN classify the risk as High because it may involve money mule recruitment. IF the user already received or forwarded money, THEN classify the risk as Critical. |
| Investment Scam | IF the investment promises guaranteed or unusually high returns AND the company cannot be verified as licensed, THEN classify the risk as High. |
| Investment Scam | IF the user is asked to pay a fee, tax, or deposit to withdraw profits, THEN classify the risk as High. IF the user already paid the fee or cannot withdraw transferred funds, THEN classify the risk as Critical. |
| Romance Scam | IF an online-only contact quickly builds a relationship and asks for money, gift cards, crypto, or emergency help, THEN classify the risk as High. |
| Romance Scam | IF the user already sent money, intimate images, or sensitive personal information, THEN classify the risk as Critical. |

These production rules are important because they make the chatbot's reasoning explainable. The final risk level can be traced back to specific warning signs and user answers instead of being treated as a black-box AI decision.

## Phase 6 - Flowchart and Conversation Architecture Design

Before implementation in Botpress, draw.io flowcharts were created to plan the chatbot's conversation architecture. The flowcharts helped visualise the route from the start of the conversation to scam selection, scam-specific assessment, result generation, and follow-up action.

The main flow begins with a greeting, followed by the scam type selection menu. The user selects one scam category, and the chatbot routes the user to the correct scam branch. Each branch collects either pasted input, guided answers, or both. After assessment, the chatbot provides a result and asks whether the user wants to check another scam type.

Several versions of the flowchart were refined through the iterative prototyping approach. Early diagrams were improved when they became too detailed, when components were disconnected, when arrows crossed too much, or when technical variables made the flow hard to understand. The final flowchart separates user-facing documentation logic from technical implementation details, making it easier to explain in the report while still matching the actual chatbot behaviour.

## Phase 7 - Botpress Implementation

The chatbot was implemented in Botpress Studio using a combination of deterministic standard nodes and AI-assisted autonomous result nodes. This phase is where the practical Botpress components were configured.

`Standard Node` components were used for predictable conversation steps such as greeting, scam selection, input collection, and confirmation questions. The main scam type menu was implemented using a `Single Choice` capture card. Phishing, parcel scam, and fake bank alert branches used `Raw Input` capture cards so users could paste suspicious messages, links, SMS content, or bank-alert text. `Confirmation` cards were used for binary action questions, such as whether the user clicked, paid, installed an app, shared credentials, or transferred money.

The implementation structure differs by scam type. Phishing, parcel scam, and fake bank alert follow an input-first flow: introduction, raw input collection, action questions, and autonomous result generation. Job scam, investment scam, and romance scam follow a guided-question flow because they are more scenario-based and may not involve one specific message artifact.

Each scam branch ends with an autonomous result node. The autonomous node receives the relevant user input, captured answers, knowledge-base context, production-rule rubric, output format instructions, and safety boundaries. The node is instructed to produce a structured assessment instead of a vague AI response.

## Phase 8 - Autonomous Reasoning and Result Generation

This phase corresponds to the reasoning, planning, and execution components of the KR cycle. The autonomous result node reasons over the user input and captured answers, applies the production-rule rubric, plans the appropriate safety advice, and executes the final chatbot response.

The autonomous node is not used as an unrestricted decision-maker. It is constrained by the knowledge base and production rules. It must classify the case as Low, Medium, High, or Critical and explain the warning signs that led to the assessment.

The required output format is:

- `Risk level: Low / Medium / High / Critical`
- `Warning signs found`
- `Why this matters`
- `Recommended next steps`
- `Safety note`

For Low risk, the chatbot provides general caution and encourages verification through official channels. For Medium risk, it warns the user to be careful and avoid acting quickly. For High risk, it advises the user to stop interaction, avoid clicking links or making payments, and verify directly through official websites, apps, or hotlines. For Critical risk, it recommends urgent response steps such as contacting the bank, changing passwords, reporting to NSRC 997, preserving evidence, or stopping communication with the scammer.

After giving the result, the autonomous node uses a transition instruction to continue to the `Check_Another_Scam` node. This allows the user to check another scam type or end the conversation instead of the chatbot stopping immediately after the AI-generated result.

## Phase 9 - Testing, Validation, and Refinement

Testing was performed using the Botpress emulator. Test cases were created for low-risk, medium-risk, high-risk, and critical-risk scenarios across the six scam categories. The purpose was to verify whether the chatbot routed users correctly, captured inputs properly, applied the intended production-rule logic, generated useful explanations, and transitioned correctly after each result.

The testing process checked:

- whether the scam menu routed users to the correct branch,
- whether pasted input was captured correctly,
- whether confirmation answers were stored in the correct workflow variables,
- whether the autonomous result node could read the required information,
- whether the risk level matched the production-rule rubric,
- whether the response included warning signs and recommended next steps,
- whether the chatbot moved to `Check_Another_Scam` after the result.

Refinement was done iteratively. When a flow was too messy, it was simplified. When a question was unclear, it was rewritten. When the autonomous result output was too vague, the instruction was strengthened. When the conversation ended too early after an autonomous node, a transition to `Check_Another_Scam` was added. This iterative process improved both the user experience and the academic defensibility of the chatbot.

## Phase 10 - Documentation and Handover

The final phase was documentation. The methodology, knowledge base, resource list, Botpress build guide, flowcharts, and test examples were documented so that the project could be explained, evaluated, and recreated by others.

This documentation is important because ScamShield Bot is not only a functional chatbot prototype but also an AI assignment artifact. The development process shows how the chatbot moved from scam knowledge acquisition to knowledge representation, reasoning, implementation, testing, and final response generation. By following the Cycle of KR in AI together with iterative prototyping, the project demonstrates a clear connection between AI theory and practical chatbot development.
