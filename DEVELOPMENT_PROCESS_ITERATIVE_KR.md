# 3.4 Development Process

The development process of ScamShield Bot followed an **iterative prototyping approach**, with the **Cycle of Knowledge Representation (KR) in AI** applied inside the development process to guide the chatbot's knowledge-design and reasoning components. This combined approach was selected because the project required both a working Botpress chatbot and an explainable AI knowledge-based assessment process.

The iterative prototyping approach guided how the chatbot was designed, built, tested, and refined over multiple versions. The Cycle of KR in AI guided how scam-related input was perceived, how scam knowledge was learned and represented, how the chatbot reasoned about risk, and how it planned and executed safety advice. Therefore, the project was not developed as a one-time fixed workflow. Instead, each prototype improved the chatbot's conversation flow, knowledge structure, production rules, and AI-generated responses.

## Overall Iterative Prototyping Approach

An iterative prototyping approach means that the system is developed through repeated cycles of design, implementation, testing, and refinement. This approach was suitable because the chatbot's final structure could not be perfectly designed in one attempt. The project required continuous adjustment of the flowchart, Botpress nodes, assessment questions, production rules, and autonomous-node instructions.

The main iterative cycle used in this project was:

1. **Design**: Draft the chatbot flow, scam categories, assessment questions, and expected result structure.
2. **Prototype**: Build the initial version using draw.io flowcharts and Botpress nodes.
3. **Test**: Run sample scam scenarios in the Botpress emulator.
4. **Evaluate**: Check whether the flow, captured variables, risk level, explanation, and transition behaviour were correct.
5. **Refine**: Improve the flowchart, questions, production rules, prompts, and Botpress transitions based on the issues found.

This iterative approach was used throughout the project. Early flowcharts were refined when they became too messy, when arrows were disconnected, when incorrect flowchart shapes were used, or when the workflow did not match the actual Botpress implementation. The Botpress chatbot was also improved when testing showed that some scam branches required pasted message input, some questions were unclear, and autonomous result nodes needed explicit transitions to continue the conversation.

## Cycle of Knowledge Representation (KR) in AI Within the Iterative Approach

Within each iteration, the Cycle of KR in AI was used to structure the AI knowledge and reasoning process. Based on the lecture concept, the cycle consists of **perception, learning, knowledge representation, reasoning, planning, and execution**. These components were mapped directly to ScamShield Bot so that the chatbot's behaviour was grounded in structured knowledge rather than unrestricted AI generation.

| KR Cycle Component | Application in ScamShield Bot |
|---|---|
| Perception | The chatbot collects user evidence through Botpress capture cards. `Single Choice` captures the selected scam type, `Raw Input` captures suspicious messages or links, and `Confirmation` captures user actions such as clicking a link, entering sensitive information, paying money, installing an app, or transferring funds. |
| Learning | Scam knowledge is acquired and refined from trusted sources such as BNM, SC Malaysia, NSRC, CCID, MCMC, Pos Malaysia, MyCERT, FTC, CISA, NCSC, FBI IC3, INTERPOL, Action Fraud, and Scamwatch. |
| Knowledge Representation | The collected scam knowledge is represented as scam categories, red flags, assessment questions, workflow variables, production rules, risk levels, knowledge-base content, and autonomous-node instructions. |
| Reasoning | The chatbot combines user input, captured answers, scam indicators, and production rules to infer a preliminary risk level: Low, Medium, High, or Critical. |
| Planning | Based on the inferred risk level, the chatbot decides the most suitable advice, such as verifying through official channels, stopping communication, contacting the bank, reporting to NSRC 997, or preserving evidence. |
| Execution | The chatbot presents the final assessment, including the risk level, warning signs, explanation, recommended next steps, and safety note. It then continues to the `Check_Another_Scam` node. |

This mapping shows how the KR cycle was not treated as a separate theory only. It was applied directly to the chatbot's development. For example, the perception component was implemented through Botpress input cards, the knowledge representation component was implemented through rules and structured knowledge-base content, and the reasoning component was implemented through production-rule-guided autonomous result nodes.

## Phase 1 - Problem Definition and Scope Selection

The first phase was to define the problem, target users, and chatbot scope. Digital scams are common, and many users are unsure whether a suspicious message, link, job offer, investment opportunity, or online relationship is dangerous. Therefore, ScamShield Bot was designed to provide a **preliminary scam-risk assessment** and safety guidance.

The chatbot was scoped around six scam categories:

- phishing links and fake account-verification requests,
- fake parcel-delivery messages,
- fake bank alerts and bank impersonation scams,
- fake job offers,
- investment scams,
- romance scams.

The scope was intentionally limited. ScamShield Bot does not claim to provide confirmed scam detection, live malware scanning, real-time URL reputation scanning, QR-code analysis, official account verification, or formal investigation. It provides an AI-assisted assessment based on visible user input, structured scam knowledge, and production rules.

## Phase 2 - Knowledge Base Construction

The second phase was to construct the knowledge base. This phase supports the learning component of the KR cycle because the chatbot required reliable scam knowledge before it could provide useful advice.

Scam knowledge was collected from official and high-credibility sources, including Bank Negara Malaysia (BNM), Securities Commission Malaysia (SC Malaysia), National Scam Response Centre (NSRC), Commercial Crime Investigation Department of the Royal Malaysia Police (CCID PDRM), Malaysian Communications and Multimedia Commission (MCMC), Pos Malaysia, MyCERT, Federal Trade Commission (FTC), Cybersecurity and Infrastructure Security Agency (CISA), National Cyber Security Centre (NCSC), FBI Internet Crime Complaint Center (FBI IC3), INTERPOL, Action Fraud, Scamwatch, and other regulator or cybersecurity pages.

The collected information was organised into scam definitions, common red flags, user-risk indicators, recommended safety advice, and victim-response guidance. This prevented the chatbot from relying only on AI-generated assumptions and made the knowledge base more traceable.

## Phase 3 - Knowledge Representation and Rule Structuring

After the knowledge was collected, it was converted into structured forms that could be used by the chatbot. This phase corresponds to the knowledge representation component of the KR cycle.

The knowledge was represented through:

- six scam categories,
- scam-specific red flags,
- assessment questions,
- Botpress workflow variables,
- IF-THEN production rules,
- risk-level rubrics,
- structured knowledge-base content,
- autonomous result-node instructions.

For example, the general warning that phishing scams often request OTPs, passwords, PINs, or banking details was converted into both an assessment question and a production rule. This allowed the chatbot to use the knowledge during reasoning instead of storing it only as background information.

## Phase 4 - Assessment Question and Input Design

The assessment questions were created by converting repeated scam indicators into short user-friendly questions. The questions were not copied directly from one source. They were designed by reviewing the knowledge base and identifying warning signs that could be asked clearly in a chatbot conversation.

The design separated scam types into two groups. Phishing, parcel scam, and fake bank alert use an input-first flow because users usually have a suspicious message, link, SMS, or alert that they want to check. For these scam types, the chatbot first asks the user to paste the suspicious content. After that, it asks only action questions that cannot be safely inferred from the message alone, such as whether the user clicked a link, entered sensitive information, paid money, installed an app, called a number, or transferred funds.

Job scam, investment scam, and romance scam use guided scenario questions because these scams may not involve one single pasted message. They often involve ongoing conversations, offers, promises, pressure, financial requests, or relationships. Therefore, the chatbot asks questions about scenario indicators such as upfront fees, money forwarding, guaranteed returns, licence verification, fast emotional attachment, refusal to verify identity, investment suggestions, and already-sent money or sensitive information.

## Phase 5 - Production Rules Design

Production rules were designed to support explainable reasoning. The rules follow an **IF-THEN** structure, where the IF condition represents a scam indicator or user action, and the THEN result defines the risk level and response direction.

The chatbot uses four risk levels:

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
| Fake Bank Alert | IF the message or caller asks for OTP, PIN, password, or banking credentials, THEN classify the risk as High. IF the user already shared them, THEN classify the risk as Critical. |
| Job Scam | IF the job requires upfront payment for registration, training, visa, equipment, or task-unlock fees, THEN classify the risk as High. |
| Investment Scam | IF the investment promises guaranteed or unusually high returns AND the company cannot be verified as licensed, THEN classify the risk as High. |
| Romance Scam | IF an online-only contact quickly builds a relationship and asks for money, gift cards, crypto, or emergency help, THEN classify the risk as High. |

These rules make the chatbot explainable because the final risk level can be traced back to specific warning signs and user answers.

## Phase 6 - Flowchart and Conversation Architecture Design

The chatbot flow was planned using draw.io before and during Botpress implementation. The flowchart showed the full conversation structure, including the greeting, scam type selection, scam-specific branches, input or question collection, result generation, and check-another-scam step.

The flowchart was refined through several iterations. Earlier versions were too detailed or visually messy, so the design was simplified. Correct flowchart shapes were applied: ovals for start and end, diamonds for decisions, rectangles for processing, and parallelograms for input and output. Repeated result steps were also merged where possible to make the final flowchart easier to understand.

This phase helped connect the AI knowledge structure to the user conversation flow. It also allowed implementation problems to be identified before building every branch in Botpress.

## Phase 7 - Botpress Implementation

The chatbot was implemented in Botpress Studio. `Standard Node` components were used for predictable conversation steps such as greeting, scam type selection, input collection, and confirmation questions. The main menu used a `Single Choice` card. Phishing, parcel scam, and fake bank alert used `Raw Input` cards for pasted suspicious content. Action questions used `Confirmation` cards because the answers were binary and directly affected the risk level.

Each scam branch ended with an autonomous result node. The autonomous node received the user's input, captured answers, relevant knowledge-base content, production-rule rubric, output format instructions, and safety boundaries. The result node was instructed to produce a structured assessment instead of a vague AI response.

The required output format was:

- `Risk level: Low / Medium / High / Critical`
- `Warning signs found`
- `Why this matters`
- `Recommended next steps`
- `Safety note`

The autonomous result nodes were also configured with transition instructions so the chatbot continued to the `Check_Another_Scam` node after giving the assessment. This allowed the user to check another scam type or end the conversation.

## Phase 8 - Testing and Iterative Refinement

Testing was performed using the Botpress emulator. Test cases were created for low-risk, medium-risk, high-risk, and critical-risk scenarios across the six scam categories.

The testing process checked:

- whether the scam menu routed users to the correct branch,
- whether raw input and confirmation answers were captured correctly,
- whether the autonomous result node could access the required information,
- whether the risk level matched the production-rule rubric,
- whether the result included warning signs and recommended next steps,
- whether the chatbot transitioned to `Check_Another_Scam` after the result.

The chatbot was refined based on testing. Questions were rewritten when they were unclear, flowcharts were reorganised when they became messy, autonomous prompts were strengthened when the output was too vague, and transitions were added when the conversation ended too early. This repeated testing and refinement reflects the iterative prototyping approach.

## Phase 9 - Documentation and Handover

The final phase was documentation. The methodology, knowledge base, resource list, Botpress build guide, flowcharts, and test examples were documented so that the chatbot could be explained, evaluated, and recreated by others.

This documentation also supports academic transparency. It shows how the chatbot moved from problem definition to knowledge acquisition, knowledge representation, production-rule reasoning, Botpress implementation, testing, and final response generation. By combining iterative prototyping with the Cycle of KR in AI, the development process demonstrates both practical chatbot construction and AI knowledge-based reasoning.
