# 3. Methodology

This section outlines the methodology adopted in the development of ScamShield Bot, a rule-based scam awareness and risk assessment chatbot. The methodology covers the design approach, knowledge representation technique, tools and technologies employed, and the step-by-step development process from initial planning to final implementation.

---

## 3.1 Design Approach

The development of ScamShield Bot followed an **iterative design approach**, beginning with the identification of a real-world problem and progressing through structured planning, development, testing, and documentation phases. This approach was selected because it allows for continuous refinement based on testing feedback, ensuring the chatbot meets user expectations at each stage before finalisation.

During the planning phase, a **conversation flowchart** was developed using draw.io to map the logical flow of the chatbot's interaction with users. The flowchart illustrates how a user initiates a scam assessment, how the chatbot collects input through guided follow-up questions, and how it produces a risk classification (Low / Medium / High) together with an explanation and recommended next steps. The flowchart is included in this report as a supporting diagram for the design phase.

**User stories** were created to define the expected experience from the user's perspective:

- As a student, I want to paste a suspicious message into the chatbot so that I can quickly find out whether it is a scam.
- As a general user, I want to know the risk level of a suspicious investment offer so that I can decide whether to proceed safely.
- As a user who received a bank alert asking for my OTP, I want the chatbot to tell me whether it is a phishing attempt so that I do not share my credentials.
- As a user who has already clicked a suspicious link, I want clear advice on what to do immediately so that I can minimise potential damage.

These user stories guided the design of conversation flows and ensured that the chatbot addressed realistic, high-priority scam scenarios faced by the target users.

---

## 3.2 Knowledge Representation

ScamShield Bot employs **production rules** as its primary knowledge representation technique. Production rules operate on an **IF-THEN** logical structure, where each rule defines a specific condition and a corresponding action or conclusion. This technique was selected because scam detection is fundamentally pattern-based: scammers repeatedly exploit the same red flags — urgency, impersonation of trusted organisations, suspicious links, requests for credentials, and promises of guaranteed financial returns. Production rules translate these patterns directly into computable decision logic, making them well-suited for this project.

**Why not other techniques?**

Alternative knowledge representation methods were considered but not adopted:

- **Semantic networks** are more appropriate for representing conceptual relationships between entities (e.g., "phishing is a type of scam") rather than actionable decision logic. They do not directly produce outputs such as risk levels or safety advice.
- **Frames** are better suited for object-oriented knowledge structures requiring rich attribute descriptions. They involve a higher level of implementation complexity that is not practical within a low-code chatbot environment such as Botpress.

Production rules, by contrast, are simple, transparent, and directly supported by Botpress through its **condition-based node logic**, making them the most practical and academically appropriate choice for this project.

### Sample Production Rules

| IF Condition | THEN Action |
|---|---|
| Sender claims to be a bank AND requests OTP or password | Classify as **HIGH risk** — bank-impersonation scam. Advise user not to share credentials under any circumstances. |
| Parcel delivery message includes a suspicious link AND requests payment | Classify as **HIGH risk** — parcel-delivery scam. Advise user to verify via the official courier app or website only. |
| Investment offer promises guaranteed returns AND involves an unverified company | Classify as **HIGH risk** — investment scam. Direct user to SC Malaysia Investment Checker. |
| Online contact met recently AND requests money or cryptocurrency transfer | Classify as **HIGH risk** — romance scam. Advise user to stop all transfers immediately. |
| Job offer requires an upfront processing fee or training deposit | Classify as **HIGH risk** — job scam. Advise user not to pay and to verify the company registration. |
| Message contains urgency but no link, credential request, or payment demand | Classify as **MEDIUM risk** — monitor situation and verify through official channels. |
| Message is generic, no red flags triggered | Classify as **LOW risk** — advise user to remain cautious. |

The use of production rules enables ScamShield Bot to produce **explainable outputs**. Rather than returning a generic reply, the chatbot identifies which specific red flags triggered the risk classification and communicates this clearly to the user. This transparency is a key strength of the knowledge representation approach and differentiates ScamShield Bot from static FAQ-based systems.

### Knowledge Base Construction

The knowledge base was developed through an **AI-assisted but human-curated source synthesis process**. Rather than copying content from a single website, the development process began with collecting official and authoritative references for each scam category from Malaysian government agencies, regulators, law-enforcement bodies, and internationally recognised public-agency sources. These references were compiled in the project resource bank (`scamshield_resources.md`) and then reviewed to identify repeated scam patterns, warning signs, victim actions, and reporting procedures.

After source collection, the information was organised into six scam categories: phishing, fake parcel delivery, fake bank alerts, fake job offers, investment scams, and romance scams. For each category, the recurring indicators were structured into four practical knowledge components:

- scam description and common tactics
- red flags and warning signs
- recommended user actions
- reporting and escalation contacts

The final knowledge-base files were therefore not treated as raw AI output. Instead, they functioned as **structured, human-reviewed syntheses** of the collected references, translated into a format suitable for rule-based chatbot decision logic.

### Assessment Question Design

The exact assessment questions used in the chatbot were also not copied verbatim from a single source. They were created by **operationalising scam indicators** found repeatedly across the official references. In practice, this means that broad warning signs such as suspicious links, OTP requests, impersonation, urgent pressure, fee requests, requests to move money, refusal to verify identity, unlicensed investment claims, and post-incident actions were converted into short guided yes/no questions that users could answer easily inside Botpress.

This design process followed five steps:

1. Review the official sources collected in the resource bank for each scam type.
2. Identify scam indicators that appeared repeatedly across multiple authoritative references.
3. Select the indicators that were both highly diagnostic and easy for users to answer.
4. Convert those indicators into short, guided assessment questions.
5. Map each question to one or more relevant resource entries to ensure that the final interview flow remained source-informed and academically defensible.

As a result, the chatbot questions should be understood as **researcher-designed prompts anchored to authoritative scam indicators**, not as arbitrary AI-generated questions.

### Artifact-Level Input Pattern Research

For the "paste suspicious message or link" feature, a second research layer was required beyond the general scam knowledge base. General scam knowledge is sufficient for guided yes/no assessment, but pasted-message assessment requires **artifact-level pattern research**. This means identifying what can be reasonably inferred from the text of a suspicious SMS, email, chat message, or visible link string without pretending to perform live URL scanning or forensic website analysis.

This additional research was conducted only for the three scam categories most suitable for direct text or link input:

- phishing
- fake parcel delivery scams
- fake bank alerts / bank impersonation

For each category, a separate pattern-research file was created inside the project knowledge base:

- `knowledge_base/kb_input_patterns_01_phishing.txt`
- `knowledge_base/kb_input_patterns_02_parcel_scam.txt`
- `knowledge_base/kb_input_patterns_03_fake_bank_alert.txt`

Each file was compiled from official and public-authority sources only and documented six implementation-focused evidence types:

1. common scam wording patterns
2. suspicious visible link or app-install patterns
3. impersonation patterns
4. extractable indicators for the chatbot parser
5. post-incident indicators for immediate escalation
6. limitations of what cannot be safely inferred from pasted text alone

This distinction is important academically. The chatbot does not claim to determine whether a URL is technically safe or malicious in a definitive sense. Instead, it performs a **preliminary rule-based assessment** by combining artifact-level indicators from pasted content with follow-up user answers. This makes the feature source-grounded, explainable, and appropriately limited.

### Question-to-Resource Mapping

The following table documents how each assessment question was anchored to the project resource bank. Resource numbers refer to the numbering used inside `scamshield_resources.md`.

| Scam Type | Assessment Question | Supporting Resource(s) |
|---|---|---|
| Phishing | Did the message ask you to click a link to verify, unlock, or secure an account? | Scam Type 1: #4 MCMC - Identify Phishing E-Mail; #17 FTC - How To Recognize and Avoid Phishing Scams; #21 CISA - Recognize and Report Phishing |
| Phishing | Did it ask for your OTP, password, PIN, or banking details? | Scam Type 1: #13 CyberSecurity Malaysia - Security Best Practices for Online Account Credentials; #16 CCID (PDRM) - E-Banking Fraud; #22 CISA - NSA FBI MS-ISAC Phishing Prevention Alert |
| Phishing | Did the sender, phone number, email, or website look suspicious or not match the official one? | Scam Type 1: #4 MCMC - Identify Phishing E-Mail; #9 MyCERT - Phishing Emails Targeting Bank Negara Malaysia; #25 NCSC - Phishing: Spot and Report Scam Emails, Texts, Websites and Calls |
| Phishing | Did the message create urgency, such as account suspension, refund expiry, or legal action? | Scam Type 1: #17 FTC - How To Recognize and Avoid Phishing Scams; #21 CISA - Recognize and Report Phishing; #30 ACSC - Phishing |
| Phishing | Did you already click the link or enter any information? | Scam Type 1: #5 MCMC - Protect Yourself from Phishing; #16 CCID (PDRM) - E-Banking Fraud; #20 CISA - Phishing Guidance |
| Parcel Scam | Did you receive a parcel message for a package you were not expecting? | Scam Type 2: #1 Pos Malaysia - Scam Update Page; #18 FTC - Got a Package You Didn't Order? It's Probably a Scam; #26 ACSC - Recognise and Report Scams |
| Parcel Scam | Did the message ask you to click a tracking or rescheduling link? | Scam Type 2: #3 Pos Malaysia - Online Security, Fraud and Scams; #14 FTC - Fake Shipping Notification Emails and Text Messages; #23 NCSC - Scam 'Missed Parcel' SMS Messages |
| Parcel Scam | Did it ask you to pay a delivery fee, customs fee, or rescheduling fee? | Scam Type 2: #3 Pos Malaysia - Online Security, Fraud and Scams; #15 FTC - Think That Text from USPS Could Be a Scam; #16 FTC - Scammers Delivering Phishing Messages Holiday Season |
| Parcel Scam | Did it ask you to install an app or APK from the message? | Scam Type 2: #2 Pos Malaysia - Impersonator Scams Are on the Rise; #13 CyberSecurity Malaysia - Malicious Attachment Targeting Banking Users; #23 NCSC - Scam 'Missed Parcel' SMS Messages |
| Parcel Scam | Did anyone claim illegal items were found in your parcel or ask you to transfer money to a "safe account"? | Scam Type 2: #11 NSRC - Press Conference Page; #19 Action Fraud - Courier Fraud A-Z; #20 Action Fraud - Beat the Courier Fraudsters |
| Parcel Scam | Did you already pay or install anything? | Scam Type 2: #13 CyberSecurity Malaysia - Malicious Attachment Targeting Banking Users; #14 FTC - Fake Shipping Notification Emails and Text Messages; #24 NCSC - Report a Scam Text Message |
| Fake Bank Alert | Did the message or caller claim to be from your bank? | Scam Type 3: #13 Maybank - Latest Scam Alerts; #15 FTC - How to Avoid Imposter Scams; #25 NCSC - How to Spot a Scam Email, Text or Call |
| Fake Bank Alert | Did they ask for your OTP, PIN, password, or other banking credentials? | Scam Type 3: #7 BNM - Beware of Fraudulent SMS and Telephone Calls; #10 CCID (PDRM) - E-Banking Fraud; #18 FBI IC3 - Account Takeover via Impersonation of Financial Institution Support |
| Fake Bank Alert | Did they ask you to click a login link or call a number shown in the message? | Scam Type 3: #5 BNM - Alert: SMS Scam and Fake Website Using BNM Name; #24 NCSC - Phishing: Spot and Report Scam Emails, Texts, Websites and Calls; #25 NCSC - How to Spot a Scam Email, Text or Call |
| Fake Bank Alert | Did they tell you to transfer money to a "safe account" or "secure account"? | Scam Type 3: #6 BNM - Beware of Bogus Calls Impersonating BNM Officers; #16 FTC - Did Someone Tell You to Move Your Money? It Could Be a Scam; #19 FBI IC3 - Financial Institution Customer Fraud Scheme |
| Fake Bank Alert | Did you already share your OTP or banking details? | Scam Type 3: #10 CCID (PDRM) - E-Banking Fraud; #21 Action Fraud - Bank Account Fraud |
| Job Scam | Did the job offer come through WhatsApp, Telegram, or an unsolicited message? | Scam Type 4: #3 KLN - Syndicate Offering Fake Job Opportunity in Malaysia Through Internet; #9 MCMC - 744 Online Scam Reports Involving Facebook; #13 FTC - How to Avoid Work-From-Home Job Scams |
| Job Scam | Were you asked to pay any fee before starting, such as registration, training, visa, or equipment fees? | Scam Type 4: #11 FTC - Job Scams; #25 Action Fraud - Recruitment Scams |
| Job Scam | Did the job promise unusually high income for simple online tasks? | Scam Type 4: #7 RMP - Scam Alert: Identifying Job Scams; #13 FTC - How to Avoid Work-From-Home Job Scams |
| Job Scam | Were you asked to receive money into your account and forward it to someone else? | Scam Type 4: #26 Action Fraud - Employment Fraud; #27 Action Fraud - Money Mules: Fake Jobs Lead to Crime |
| Job Scam | Was the job overseas, especially without a proper contract or verified employer? | Scam Type 4: #1 KLN - Malaysians Rescued from Job Scam Syndicate in Cambodia; #2 KLN - 20 Malaysian Victims of Job Scam in Myanmar Safely Repatriated; #14 FBI IC3 - False Job Advertisements Linked to Labor Trafficking at Scam Compounds |
| Job Scam | Were you asked to deposit money to unlock higher-paying tasks? | Scam Type 4: #13 FTC - How to Avoid Work-From-Home Job Scams; #16 FBI IC3 - Scammers Defraud Individuals via Work-From-Home Scams |
| Investment Scam | Does the investment promise guaranteed or unusually high returns? | Scam Type 5: #1 SC Malaysia - Beware of Scams; #4 SC Malaysia - Scam Meter; #15 BNM - Beware of Financial Scams Promising Unrealistic High Returns; #25 Investor.gov - Red Flags of Investment Fraud Checklist |
| Investment Scam | Can the company or person be verified as licensed by SC Malaysia or BNM? | Scam Type 5: #2 SC Malaysia - Investor Alert List; #5 SC Malaysia - Investment Checker; #12 BNM - Financial Consumer Alert List; #23 SEC - Fraudsters Posing as Brokers or Investment Advisers |
| Investment Scam | Are you being pressured to invest quickly or keep it secret? | Scam Type 5: #1 SC Malaysia - Beware of Scams; #4 SC Malaysia - Scam Meter; #24 Investor.gov - What You Can Do to Avoid Investment Fraud; #25 Investor.gov - Red Flags of Investment Fraud Checklist |
| Investment Scam | Did someone you met online or recently became close to recommend this investment? | Scam Type 5: #26 Investor.gov - Relationship Investment Scams; #39 INTERPOL - Investment Fraud via Dating Apps; #42 IOSCO - Relationship Investment Scams |
| Investment Scam | Have you been asked to pay a fee, tax, or deposit to withdraw your profits? | Scam Type 5: #1 SC Malaysia - Beware of Scams; #4 SC Malaysia - Scam Meter; #28 FTC - Investment Scams; #34 ASIC - Investment Scams |
| Investment Scam | Is the investment using an app, website, or platform that looks unverified or suspicious? | Scam Type 5: #5 SC Malaysia - Investment Checker; #6 SC Malaysia - Warns Public on Deepfake Investment Scams; #29 FTC - Spotting Cryptocurrency Investment Scams; #35 ASIC - Shuts Down 130 Investment Scam Websites Per Week |
| Romance Scam | Did you meet this person online and not in real life? | Scam Type 6: #7 FTC - What To Know About Romance Scams; #12 FBI - Romance Scams; #21 Action Fraud - Romance Scams; #25 Scamwatch - Online Dating and Romance Scams |
| Romance Scam | Has the relationship become serious very quickly? | Scam Type 6: #7 FTC - What To Know About Romance Scams; #11 FTC - Romance Scammers Love to Take Your Money; #19 INTERPOL - Social Engineering Scams; #28 Scamwatch - Relationship Scams |
| Romance Scam | Has the person refused a live video call or avoided verifying their identity? | Scam Type 6: #10 FTC - Why Can't That New Love Interest Meet in Person?; #12 FBI - Romance Scams; #22 Action Fraud - Fall for the Person, Not the Profile; #33 Competition Bureau Canada - Romance Scams |
| Romance Scam | Has the person asked for money, crypto, gift cards, or help with an emergency? | Scam Type 6: #7 FTC - What To Know About Romance Scams; #11 FTC - Romance Scammers Love to Take Your Money; #12 FBI - Romance Scams; #25 Scamwatch - Online Dating and Romance Scams |
| Romance Scam | Has the person encouraged you to invest in a platform they recommended? | Scam Type 6: #14 FBI - Romance Scammers Targeting Victims with Fake Crypto Investments; #15 FBI IC3 - New Trend in Romance Scams: Crypto Investment Fraud; #17 INTERPOL - Investment Fraud via Dating Apps; #31 ACCC - Never Take Investment Advice from an Online Love Interest |
| Romance Scam | Have you already sent money, intimate images, or sensitive personal information? | Scam Type 6: #1 NFCC/NSRC - About National Scam Response Centre; #7 FTC - What To Know About Romance Scams; #12 FBI - Romance Scams; #19 INTERPOL - Social Engineering Scams; #25 Scamwatch - Online Dating and Romance Scams |

---

## 3.3 Tools and Technologies Used

The tools and technologies selected for this project were chosen based on their suitability for low-code chatbot development, accessibility, and alignment with the project objectives.

| Tool / Technology | Purpose | Reason for Selection |
|---|---|---|
| **Botpress** | Main chatbot development platform | Low-code visual flow builder, free tier available, supports condition-based rule logic natively |
| **Botpress Emulator** | Testing and debugging chatbot conversations | Built into Botpress; allows full simulation of user interactions without needing external deployment |
| **draw.io** | Conversation flowchart design | Free, browser-based, supports standard flowchart symbols (parallelogram for I/O, diamond for decisions) |
| **Google Docs / Microsoft Word** | Report writing and group documentation | Enables collaborative editing, supports APA formatting, accessible across all group members |
| **Bank Negara Malaysia (BNM) Resources** | Knowledge base for chatbot scam guidance content | Official, credible Malaysian government source; directly relevant to local target users |
| **Securities Commission Malaysia (SC Malaysia)** | Investment scam reference and Investor Alert List | Authoritative Malaysian financial regulator; provides the SC Scam Meter and Investment Checker tools referenced in chatbot advice |
| **NSRC 997** | Reporting hotline reference included in chatbot safety advice | Official Malaysian National Scam Response Centre; most relevant emergency contact for Malaysian users |

Each tool was specifically selected to keep the project within a **low-code, zero-cost** development environment while maintaining professional output quality suitable for academic submission.

---

## 3.4 Development Process

The development of ScamShield Bot followed a structured iterative process drawing on principles from the **Software Development Life Cycle (SDLC)**, adapted for a low-code chatbot environment. The process consisted of seven phases, each with clearly defined objectives and deliverables.

### Phase 1 — Problem Definition
The team identified the growing prevalence of digital scams targeting students and general users in Malaysia as a critical and underserved problem. The primary problem statement, secondary information gap, and practical need were formally defined. The project scope was established, clearly outlining the six scam categories covered (phishing, parcel scams, fake bank alerts, fake job offers, investment scams, and romance scams) and the boundaries of what the chatbot would and would not handle.

### Phase 2 — Literature Review
Existing scam awareness systems, rule-based chatbot implementations, and cybersecurity advisory tools were reviewed. Official resources from Bank Negara Malaysia, Securities Commission Malaysia, and the Federal Trade Commission (FTC) were studied to build an accurate and authoritative knowledge base. Knowledge representation techniques — production rules, semantic networks, and frames — were also reviewed to justify the final selection.

### Phase 3 — Design and Planning
A detailed conversation flowchart was produced in draw.io, mapping the chatbot's full decision logic from user input through guided follow-up questions to final risk classification and safety advice output. User stories were written for each major scam scenario. The complete production rule set was defined, covering all key red flags: urgency, impersonation, suspicious links, credential requests, money requests, and guaranteed return promises.

### Phase 4 — Knowledge Base Setup
The Botpress knowledge base was populated with scam-type profiles for all six categories in scope, a universal red-flag indicator library, and structured action guides covering four critical situations: before clicking a link, after clicking a link, after sharing credentials, and after transferring money. Official Malaysian reporting contacts — including NSRC 997, BNM's Financial Consumer Alert, and the SC Investor Alert List — were incorporated into all relevant response nodes.

### Phase 5 — Chatbot Development
The chatbot was built on Botpress using the visual flow builder. Six independent conversation flows were implemented, one for each scam category. Condition nodes were configured to apply the production rules based on user responses to guided questions. Response nodes were set up to output the risk classification level (Low / Medium / High), the specific red flags that triggered the result, and the recommended next steps tailored to each scenario.

### Phase 6 — Testing and Refinement
The chatbot was tested using the Botpress emulator with a structured set of simulated user scenarios drawn from realistic scam cases. Each of the six conversation flows was tested with both positive cases (genuine scam indicators) and negative cases (benign messages with no red flags). Incorrect classifications and flow logic errors were identified, the production rules were refined, and all outputs were verified to be accurate, clear, and appropriately advising before proceeding to documentation.

### Phase 7 — Documentation
The full project report was compiled in APA format, covering all required sections: Introduction, Literature Review, Methodology, Implementation, and Conclusion. The flowchart, production rule tables, and test case results were incorporated as supporting evidence. The presentation and a video demonstration of the chatbot in operation were prepared for submission.

---

### Summary of Development Phases

| Phase | Stage | Key Deliverable |
|---|---|---|
| 1 | Problem Definition | Problem statement, objectives, scope |
| 2 | Literature Review | Knowledge base research, technique comparison |
| 3 | Design & Planning | Flowchart, user stories, production rules |
| 4 | Knowledge Base Setup | Scam profiles, red-flag library, action guides |
| 5 | Chatbot Development | Fully functional Botpress chatbot |
| 6 | Testing & Refinement | Verified test cases, refined rules |
| 7 | Documentation | Full report, presentation, video demo |

Throughout all phases, the team maintained consistent collaboration and divided responsibilities according to each member's strengths. The iterative nature of the process ensured that issues identified during testing were resolved before progressing to final documentation, resulting in a chatbot that is both technically sound and academically defensible.
