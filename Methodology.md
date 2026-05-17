# 3. Methodology

This section outlines the methodology adopted in the development of ScamShield Bot, a rule-based scam awareness and risk assessment chatbot. The methodology covers the design approach, knowledge representation technique, tools and technologies employed, and the step-by-step development process from initial planning to final implementation.

---

## 3.1 Design Approach

The development of ScamShield Bot followed an **iterative design approach**, beginning with the identification of a real-world problem and progressing through structured planning, development, testing, and documentation phases. This approach was selected because it allows for continuous refinement based on testing feedback, ensuring the chatbot meets user expectations at each stage before finalisation.

During the planning phase, a **conversation flowchart** was developed using draw.io to map the logical flow of the chatbot's interaction with users. The flowchart illustrates how a user initiates a scam assessment, how the chatbot collects input through guided follow-up questions, and how it produces a risk classification (Low / Medium / High / Critical) together with an explanation and recommended next steps. The flowchart is included in this report as a supporting diagram for the design phase.

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

Production rules, by contrast, are simple, transparent, and practical within Botpress because they can be represented through structured workflows, captured variables, and rule-constrained autonomous result nodes. This makes them the most academically appropriate choice for an explainable AI-assisted scam-awareness chatbot.

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

### Artifact-Level Input Design

For the "paste suspicious message or link" feature, the chatbot design needed to distinguish between two types of evidence: evidence visible inside the user's submitted message and evidence about what the user already did. General scam knowledge is sufficient for guided yes/no assessment, but pasted-message assessment requires the AI to review visible artifact-level indicators such as suspicious links, urgent wording, credential requests, payment requests, APK prompts, and fake bank or courier claims.

This input-first design was applied only to the three scam categories most suitable for direct text or link input:

- phishing
- fake parcel delivery scams
- fake bank alerts / bank impersonation

These three flows use the same curated project knowledge base as the guided assessment questions. The difference is in how the evidence is collected and processed. For phishing, parcel, and bank-alert cases, the user first provides the suspicious message, link, or bank-alert text. The autonomous result node then reviews the submitted content against the knowledge-base indicators and production-rule rubric. After that, the chatbot uses a few follow-up questions to capture actions that cannot be safely inferred from the message alone, such as whether the user clicked, entered sensitive information, paid money, installed an app, called a number, or transferred funds.

This distinction is important academically. The chatbot does not claim to determine whether a URL is technically safe or malicious in a definitive sense. Instead, it performs a **preliminary rule-based assessment** by combining visible indicators from pasted content with follow-up user answers. This makes the feature source-grounded, explainable, and appropriately limited.

### Question-to-Resource Mapping

The following table documents how each assessment question was anchored to the project resource bank. Each supporting resource is linked directly to its original source.

| Scam Type | Assessment Question | Supporting Resource(s) |
|---|---|---|
| Phishing | Did the message ask you to click a link to verify, unlock, or secure an account? | [MCMC - Identify Phishing E-Mail](https://www.mcmc.gov.my/en/faqs/phishing-attack/2-identify-phishing-e-mail); [FTC - How To Recognize and Avoid Phishing Scams](https://consumer.ftc.gov/articles/how-recognize-avoid-phishing-scams); [CISA - Recognize and Report Phishing](https://www.cisa.gov/secure-our-world/recognize-and-report-phishing) |
| Phishing | Did it ask for your OTP, password, PIN, or banking details? | [CyberSecurity Malaysia - Security Best Practices for Online Account Credentials](https://www.cybersecurity.my/portal-main/advisories-details/3800db58-522f-11f0-a5d3-0050568c1b65); [CCID (PDRM) - E-Banking Fraud](https://ccid.rmp.gov.my/Card/E-Banking-Fraud.cfm); [CISA - NSA FBI MS-ISAC Phishing Prevention Alert](https://www.cisa.gov/news-events/alerts/2023/10/18/cisa-nsa-fbi-and-ms-isac-release-phishing-prevention-guidance) |
| Phishing | Did the sender, phone number, email, or website look suspicious or not match the official one? | [MCMC - Identify Phishing E-Mail](https://www.mcmc.gov.my/en/faqs/phishing-attack/2-identify-phishing-e-mail); [MyCERT - Phishing Emails Targeting Bank Negara Malaysia](https://www.mycert.org.my/portal/advisory?id=MA-640.102016); [NCSC - Phishing: Spot and Report Scam Emails, Texts, Websites and Calls](https://www.ncsc.gov.uk/collection/phishing-scams) |
| Phishing | Did the message create urgency, such as account suspension, refund expiry, or legal action? | [FTC - How To Recognize and Avoid Phishing Scams](https://consumer.ftc.gov/articles/how-recognize-avoid-phishing-scams); [CISA - Recognize and Report Phishing](https://www.cisa.gov/secure-our-world/recognize-and-report-phishing); [ACSC - Phishing](https://www.cyber.gov.au/threats/types-threats/phishing) |
| Phishing | Did you already click the link or enter any information? | [MCMC - Protect Yourself from Phishing](https://mcmc.gov.my/en/faqs/phishing-attack/3-protect-yourself-how); [CCID (PDRM) - E-Banking Fraud](https://ccid.rmp.gov.my/Card/E-Banking-Fraud.cfm); [CISA - Phishing Guidance](https://www.cisa.gov/resources-tools/resources/phishing-guidance-stopping-attack-cycle-phase-one) |
| Parcel Scam | Did you receive a parcel message for a package you were not expecting? | [Pos Malaysia - Scam Update Page](https://www.pos.com.my/scam-update); [FTC - Got a Package You Didn't Order? It's Probably a Scam](https://consumer.ftc.gov/consumer-alerts/2025/01/got-package-you-didnt-order-its-probably-scam); [ACSC - Recognise and Report Scams](https://www.cyber.gov.au/learn-basics/explore-basics/recognise-and-report-scams) |
| Parcel Scam | Did the message ask you to click a tracking or rescheduling link? | [Pos Malaysia - Online Security, Fraud and Scams](https://www.pos.com.my/news-info/online-security-fraud-and-scams.html); [FTC - Fake Shipping Notification Emails and Text Messages](https://consumer.ftc.gov/consumer-alerts/2023/12/fake-shipping-notification-emails-text-messages-what-you-need-know-holiday-season); [NCSC - Scam 'Missed Parcel' SMS Messages](https://www.ncsc.gov.uk/guidance/scam-missed-parcel-sms-messages) |
| Parcel Scam | Did it ask you to pay a delivery fee, customs fee, or rescheduling fee? | [Pos Malaysia - Online Security, Fraud and Scams](https://www.pos.com.my/news-info/online-security-fraud-and-scams.html); [FTC - Think That Text from USPS Could Be a Scam](https://consumer.ftc.gov/consumer-alerts/2025/04/think-text-message-usps-it-could-be-scam); [FTC - Scammers Delivering Phishing Messages Holiday Season](https://consumer.ftc.gov/consumer-alerts/2024/12/scammers-are-delivering-phishing-messages-holiday-season) |
| Parcel Scam | Did it ask you to install an app or APK from the message? | [Pos Malaysia - Impersonator Scams Are on the Rise](https://www.pos.com.my/knowledge-hub/pos-malaysia-scam-are-on-the-rise); [CyberSecurity Malaysia - Malicious Attachment Targeting Banking Users](https://www.cybersecurity.my/portal-main/advisories-details/dc8402e8-b9f7-11f0-b161-0050568ccc16); [NCSC - Scam 'Missed Parcel' SMS Messages](https://www.ncsc.gov.uk/guidance/scam-missed-parcel-sms-messages) |
| Parcel Scam | Did anyone claim illegal items were found in your parcel or ask you to transfer money to a "safe account"? | [NSRC - Press Conference Page](https://nfcc.jpm.gov.my/index.php/en/component/content/article/press-conference-national-scam-resonse-centre-nsrc?catid=20&Itemid=114); [Action Fraud - Courier Fraud A-Z](https://www.actionfraud.police.uk/a-z-of-fraud/courier-fraud); [Action Fraud - Beat the Courier Fraudsters](https://www.actionfraud.police.uk/beatthecourierfraudsters) |
| Parcel Scam | Did you already pay or install anything? | [CyberSecurity Malaysia - Malicious Attachment Targeting Banking Users](https://www.cybersecurity.my/portal-main/advisories-details/dc8402e8-b9f7-11f0-b161-0050568ccc16); [FTC - Fake Shipping Notification Emails and Text Messages](https://consumer.ftc.gov/consumer-alerts/2023/12/fake-shipping-notification-emails-text-messages-what-you-need-know-holiday-season); [NCSC - Report a Scam Text Message](https://www.ncsc.gov.uk/collection/phishing-scams/report-scam-text-message) |
| Fake Bank Alert | Did the message or caller claim to be from your bank? | [Maybank - Latest Scam Alerts](https://www.maybank2u.com.my/maybank2u/malaysia/en/personal/security_alert/new-scam-alert.page); [FTC - How to Avoid Imposter Scams](https://consumer.ftc.gov/features/how-avoid-imposter-scams); [NCSC - How to Spot a Scam Email, Text or Call](https://www.ncsc.gov.uk/collection/phishing-scams/spot-scams) |
| Fake Bank Alert | Did they ask for your OTP, PIN, password, or other banking credentials? | [BNM - Beware of Fraudulent SMS and Telephone Calls](https://www.bnm.gov.my/-/beware-of-fraudulent-sms-and-telephone-calls-requesting-confirmation-on-credit-card-transactions); [CCID (PDRM) - E-Banking Fraud](https://ccid.rmp.gov.my/Card/E-Banking-Fraud.cfm); [FBI IC3 - Account Takeover via Impersonation of Financial Institution Support](https://www.ic3.gov/PSA/2025/PSA251125) |
| Fake Bank Alert | Did they ask you to click a login link or call a number shown in the message? | [BNM - Alert: SMS Scam and Fake Website Using BNM Name](https://www.bnm.gov.my/-/alert-on-sms-scam-and-fake-website-involving-bank-negara-malaysia-name); [NCSC - Phishing: Spot and Report Scam Emails, Texts, Websites and Calls](https://www.ncsc.gov.uk/collection/phishing-scams); [NCSC - How to Spot a Scam Email, Text or Call](https://www.ncsc.gov.uk/collection/phishing-scams/spot-scams) |
| Fake Bank Alert | Did they tell you to transfer money to a "safe account" or "secure account"? | [BNM - Beware of Bogus Calls Impersonating BNM Officers](https://www.bnm.gov.my/-/beware-of-bogus-calls-from-persons-impersonating-as-bank-negara-malaysia-officers); [FTC - Did Someone Tell You to Move Your Money? It Could Be a Scam](https://consumer.ftc.gov/consumer-alerts/2024/01/did-someone-tell-you-move-or-transfer-your-money-it-could-be-scam); [FBI IC3 - Financial Institution Customer Fraud Scheme](https://www.ic3.gov/PSA/2024/PSA240802) |
| Fake Bank Alert | Did you already share your OTP or banking details? | [CCID (PDRM) - E-Banking Fraud](https://ccid.rmp.gov.my/Card/E-Banking-Fraud.cfm); [Action Fraud - Bank Account Fraud](https://www.actionfraud.police.uk/a-z-of-fraud/bank-account-fraud) |
| Job Scam | Did the job offer come through WhatsApp, Telegram, or an unsolicited message? | [KLN - Syndicate Offering Fake Job Opportunity in Malaysia Through Internet](https://www.kln.gov.my/web/pak_islamabad/news-from-mission/-/blogs/syndicate-offering-fake-fraud-job-opportunity-in-malaysia-through-internet); [MCMC - 744 Online Scam Reports Involving Facebook](https://www.mcmc.gov.my/en/media/press-clippings/744-online-scam-reports-involving-facebook-lodged); [FTC - How to Avoid Work-From-Home Job Scams](https://consumer.ftc.gov/consumer-alerts/2025/06/how-avoid-work-home-job-scams) |
| Job Scam | Were you asked to pay any fee before starting, such as registration, training, visa, or equipment fees? | [FTC - Job Scams](https://consumer.ftc.gov/articles/job-scams); [Action Fraud - Recruitment Scams](https://www.actionfraud.police.uk/a-z-of-fraud/recruitment-scams) |
| Job Scam | Did the job promise unusually high income for simple online tasks? | [RMP - Scam Alert: Identifying Job Scams](https://www.rmp.gov.my/news-detail/2023/11/29/besmartstayalert-letsfightscammertogether-keratan-akhbar-pilihan-scam-alert-identifying-job-scams); [FTC - How to Avoid Work-From-Home Job Scams](https://consumer.ftc.gov/consumer-alerts/2025/06/how-avoid-work-home-job-scams) |
| Job Scam | Were you asked to receive money into your account and forward it to someone else? | [Action Fraud - Employment Fraud](https://www.actionfraud.police.uk/a-z-of-fraud/employment-fraud); [Action Fraud - Money Mules: Fake Jobs Lead to Crime](https://www.actionfraud.police.uk/news/money-mules-fake-jobs-lead-to-crime) |
| Job Scam | Was the job overseas, especially without a proper contract or verified employer? | [KLN - Malaysians Rescued from Job Scam Syndicate in Cambodia](https://www.kln.gov.my/web/guest/-/malaysians-rescued-from-job-scam-syndicate-in-cambodia); [KLN - 20 Malaysian Victims of Job Scam in Myanmar Safely Repatriated](https://www.kln.gov.my/web/guest/-/20-malaysian-victims-of-job-scam-syndicates-in-myanmar-safely-repatriated); [FBI IC3 - False Job Advertisements Linked to Labor Trafficking at Scam Compounds](https://www.ic3.gov/PSA/2023/psa230522) |
| Job Scam | Were you asked to deposit money to unlock higher-paying tasks? | [FTC - How to Avoid Work-From-Home Job Scams](https://consumer.ftc.gov/consumer-alerts/2025/06/how-avoid-work-home-job-scams); [FBI IC3 - Scammers Defraud Individuals via Work-From-Home Scams](https://www.ic3.gov/PSA/2024/PSA240604) |
| Investment Scam | Does the investment promise guaranteed or unusually high returns? | [SC Malaysia - Beware of Scams](https://www.sc.com.my/investor-empowerment/scam); [SC Malaysia - Scam Meter](https://www.sc.com.my/investor-empowerment/scam-meter); [BNM - Beware of Financial Scams Promising Unrealistic High Returns](https://www.bnm.gov.my/-/beware-of-financial-scams-promising-unrealistic-high-returns-1); [Investor.gov - Red Flags of Investment Fraud Checklist](https://www.investor.gov/protect-your-investments/fraud/how-avoid-fraud/red-flags-investment-fraud-checklist) |
| Investment Scam | Can the company or person be verified as licensed by SC Malaysia or BNM? | [SC Malaysia - Investor Alert List](https://www.sc.com.my/investor-alert-list); [SC Malaysia - Investment Checker](https://www.sc.com.my/investment-checker); [BNM - Financial Consumer Alert List](https://www.bnm.gov.my/financial-consumer-alert-list); [SEC - Fraudsters Posing as Brokers or Investment Advisers](https://www.sec.gov/oiea/investor-alerts-and-bulletins/fraudsters-posing-brokers-or-investment-advisers-investor-alert) |
| Investment Scam | Are you being pressured to invest quickly or keep it secret? | [SC Malaysia - Beware of Scams](https://www.sc.com.my/investor-empowerment/scam); [SC Malaysia - Scam Meter](https://www.sc.com.my/investor-empowerment/scam-meter); [Investor.gov - What You Can Do to Avoid Investment Fraud](https://www.investor.gov/protect-your-investments/fraud/how-avoid-fraud/what-you-can-do-avoid-investment-fraud); [Investor.gov - Red Flags of Investment Fraud Checklist](https://www.investor.gov/protect-your-investments/fraud/how-avoid-fraud/red-flags-investment-fraud-checklist) |
| Investment Scam | Did someone you met online or recently became close to recommend this investment? | [Investor.gov - Relationship Investment Scams](https://www.investor.gov/protect-your-investments/fraud/types-fraud/relationship-investment-scam); [INTERPOL - Investment Fraud via Dating Apps](https://www.interpol.int/en/News-and-Events/News/2021/Investment-fraud-via-dating-apps); [IOSCO - Relationship Investment Scams](https://www.iosco.org/v2/investor_protection/?subsection=relationship_investment_scams) |
| Investment Scam | Have you been asked to pay a fee, tax, or deposit to withdraw your profits? | [SC Malaysia - Beware of Scams](https://www.sc.com.my/investor-empowerment/scam); [SC Malaysia - Scam Meter](https://www.sc.com.my/investor-empowerment/scam-meter); [FTC - Investment Scams](https://consumer.ftc.gov/articles/investment-scams); [ASIC - Investment Scams](https://www.asic.gov.au/about-asic/contact-us/reporting-misconduct-to-asic/investment-scams/) |
| Investment Scam | Is the investment using an app, website, or platform that looks unverified or suspicious? | [SC Malaysia - Investment Checker](https://www.sc.com.my/investment-checker); [SC Malaysia - Warns Public on Deepfake Investment Scams](https://www.sc.com.my/resources/media/media-release/sc-warns-public-on-deepfake-investment-scams-impersonating-prominent-personalities-and-using-names-of-reputable-companies); [FTC - Spotting Cryptocurrency Investment Scams](https://consumer.ftc.gov/consumer-alerts/2021/05/spotting-cryptocurrency-investment-scams); [ASIC - Shuts Down 130 Investment Scam Websites Per Week](https://www.asic.gov.au/about-asic/news-centre/find-a-media-release/2025-releases/25-026mr-asic-shuts-down-130-investment-scam-websites-per-week/) |
| Romance Scam | Did you meet this person online and not in real life? | [FTC - What To Know About Romance Scams](https://consumer.ftc.gov/articles/what-know-about-romance-scams); [FBI - Romance Scams](https://www.fbi.gov/how-we-can-help-you/scams-and-safety/common-frauds-and-scams/romance-scams); [Action Fraud - Romance Scams](https://www.actionfraud.police.uk/a-z-of-fraud/romance-scams); [Scamwatch - Online Dating and Romance Scams](https://www.scamwatch.gov.au/types-of-scams/online-dating-and-romance-scams) |
| Romance Scam | Has the relationship become serious very quickly? | [FTC - What To Know About Romance Scams](https://consumer.ftc.gov/articles/what-know-about-romance-scams); [FTC - Romance Scammers Love to Take Your Money](https://consumer.ftc.gov/consumer-alerts/2024/02/romance-scammers-love-take-your-money); [INTERPOL - Social Engineering Scams](https://www.interpol.int/en/Crimes/Financial-crime/Social-engineering-scams); [Scamwatch - Relationship Scams](https://www.scamwatch.gov.au/types-of-scams/relationship-scams) |
| Romance Scam | Has the person refused a live video call or avoided verifying their identity? | [FTC - Why Can't That New Love Interest Meet in Person?](https://consumer.ftc.gov/consumer-alerts/2026/02/why-cant-new-love-interest-meet-person); [FBI - Romance Scams](https://www.fbi.gov/how-we-can-help-you/scams-and-safety/common-frauds-and-scams/romance-scams); [Action Fraud - Fall for the Person, Not the Profile](https://www.actionfraud.police.uk/romancefraud); [Competition Bureau Canada - Romance Scams](https://competition-bureau.canada.ca/en/fraud-and-scams/tips-and-advice/romance-scams) |
| Romance Scam | Has the person asked for money, crypto, gift cards, or help with an emergency? | [FTC - What To Know About Romance Scams](https://consumer.ftc.gov/articles/what-know-about-romance-scams); [FTC - Romance Scammers Love to Take Your Money](https://consumer.ftc.gov/consumer-alerts/2024/02/romance-scammers-love-take-your-money); [FBI - Romance Scams](https://www.fbi.gov/how-we-can-help-you/scams-and-safety/common-frauds-and-scams/romance-scams); [Scamwatch - Online Dating and Romance Scams](https://www.scamwatch.gov.au/types-of-scams/online-dating-and-romance-scams) |
| Romance Scam | Has the person encouraged you to invest in a platform they recommended? | [FBI - Romance Scammers Targeting Victims with Fake Crypto Investments](https://www.fbi.gov/contact-us/field-offices/jacksonville/news/romance-scammers-targeting-victims-with-fake-crypto-investments); [FBI IC3 - New Trend in Romance Scams: Crypto Investment Fraud](https://www.ic3.gov/Media/Y2021/PSA210916); [INTERPOL - Investment Fraud via Dating Apps](https://www.interpol.int/en/News-and-Events/News/2021/Investment-fraud-via-dating-apps); [ACCC - Never Take Investment Advice from an Online Love Interest](https://www.accc.gov.au/media-release/avoid-heartache-this-valentines-day-never-take-investment-advice-from-an-online-love-interest) |
| Romance Scam | Have you already sent money, intimate images, or sensitive personal information? | [NFCC/NSRC - About National Scam Response Centre](https://nfcc.jpm.gov.my/index.php/en/about-nsrc); [FTC - What To Know About Romance Scams](https://consumer.ftc.gov/articles/what-know-about-romance-scams); [FBI - Romance Scams](https://www.fbi.gov/how-we-can-help-you/scams-and-safety/common-frauds-and-scams/romance-scams); [INTERPOL - Social Engineering Scams](https://www.interpol.int/en/Crimes/Financial-crime/Social-engineering-scams); [Scamwatch - Online Dating and Romance Scams](https://www.scamwatch.gov.au/types-of-scams/online-dating-and-romance-scams) |

---

## 3.3 Tools and Technologies Used

The tools and technologies selected for this project were chosen based on their suitability for low-code chatbot development, accessibility, and alignment with the project objectives.

| Tool / Technology | Purpose | Reason for Selection |
|---|---|---|
| **Botpress** | Main chatbot development platform | Low-code visual flow builder, free tier available, supports structured workflows, capture cards, knowledge bases, and autonomous nodes |
| **Botpress Emulator** | Testing and debugging chatbot conversations | Built into Botpress; allows full simulation of user interactions without needing external deployment |
| **draw.io** | Conversation flowchart design | Free, browser-based, supports standard flowchart symbols (parallelogram for I/O, diamond for decisions) |
| **Google Docs / Microsoft Word** | Report writing and group documentation | Enables collaborative editing, supports APA formatting, accessible across all group members |
| **Bank Negara Malaysia (BNM) Resources** | Knowledge base for chatbot scam guidance content | Official, credible Malaysian government source; directly relevant to local target users |
| **Securities Commission Malaysia (SC Malaysia)** | Investment scam reference and Investor Alert List | Authoritative Malaysian financial regulator; provides the SC Scam Meter and Investment Checker tools referenced in chatbot advice |
| **NSRC 997** | Reporting hotline reference included in chatbot safety advice | Official Malaysian National Scam Response Centre; most relevant emergency contact for Malaysian users |

Each tool was specifically selected to keep the project within a **low-code, zero-cost** development environment while maintaining professional output quality suitable for academic submission.

---

## 3.4 Development Process

The development of ScamShield Bot followed a structured iterative process based on the **Software Development Life Cycle (SDLC)**, adapted for a low-code conversational AI environment. Instead of building the chatbot directly from assumptions, the process moved from problem definition and literature review to knowledge-base construction, question design, flowchart planning, Botpress implementation, testing, and final documentation. This ensured that each node, question, and output in the chatbot had a clear purpose and could be justified academically.

### Phase 1 - Problem Definition and Scope Selection

The development process began by defining the problem: many users receive suspicious messages, links, calls, job offers, investment proposals, or online relationship requests, but may not know which warning signs are serious or what action to take next. The project was therefore scoped as an **AI-assisted, explainable, Malaysia-focused scam-awareness chatbot** that provides a **preliminary risk assessment**, rather than a commercial cybersecurity scanner.

The chatbot scope was limited to six scam categories: phishing links and fake account-verification requests, fake parcel-delivery messages, fake bank alerts and bank-impersonation scams, fake job offers, investment scams, and romance scams. These categories were selected because they are common digital scam scenarios and because each category has different indicators, user actions, and recommended safety responses.

### Phase 2 - Literature Review and Gap Identification

Existing AI scam-detection solutions and related chatbot technologies were reviewed to understand what current tools can do and where a project-level chatbot could still contribute. The review showed that commercial solutions such as Scamio, Sago, and UncovAI provide strong AI-powered scam analysis, but they are generally broader tools and do not clearly expose a Malaysia-specific, step-by-step assessment process to the user.

From this review, ScamShield's development focus was defined as local relevance, explainability, guided assessment, and user education. The chatbot was not designed to outperform commercial threat-intelligence systems. Instead, it was designed to help users understand warning signs, answer structured follow-up questions, and receive practical next steps grounded in curated scam-awareness resources.

### Phase 3 - Knowledge Base Construction

The knowledge base was built from official or high-credibility sources, including BNM, SC Malaysia, NSRC, CCID, MCMC, Pos Malaysia, MyCERT, FTC, CISA, NCSC, FBI IC3, INTERPOL, Action Fraud, Scamwatch, and other regulator or public-agency resources. These sources were collected in `scamshield_resources.md` and reviewed to identify repeated scam indicators, victim actions, reporting contacts, and prevention advice.

The collected information was organised into the six scam categories used by the chatbot. For each scam type, the knowledge base files contain scam definitions, common tactics, red flags, production rules, user advice, and victim-response guidance. This structure allows the chatbot to produce consistent and explainable advice instead of relying only on open-ended AI generation.

Reusable knowledge artifacts were also created. The universal red-flag table summarises cross-scam warning signs and risk levels, while the general FAQ provides common guidance such as what to do after clicking a link, sharing credentials, transferring money, or installing a suspicious app. These artifacts support consistent advice across multiple scam flows.

### Phase 4 - Assessment Question Design

The assessment questions were created by **operationalising scam indicators** found repeatedly across the resource bank. The questions were not copied directly from a single source. Instead, broad warning signs such as credential requests, suspicious links, upfront fees, guaranteed returns, refusal to verify identity, and post-incident actions were converted into short user-friendly questions suitable for Botpress.

Yes/no and confirmation-style questions were selected because they reduce ambiguity, are easy for stressed users to answer, and fit Botpress `Capture Information` cards. They also make the assessment traceable because each answer can be stored as a workflow variable and passed into the final autonomous result node.

| Scam Type | Question Focus | Why This Question Was Asked |
|---|---|---|
| Phishing | Clicked link; entered OTP, password, PIN, banking details, or personal information | Phishing risk depends heavily on what the user already did after receiving the message. Entering sensitive information requires stronger escalation than merely receiving a suspicious link. |
| Parcel Scam | Expected delivery; paid delivery/customs/rescheduling fee; installed app or APK | These answers distinguish a normal delivery notification from high-risk parcel smishing or critical APK/payment compromise. |
| Fake Bank Alert | Clicked or called a number; shared credentials; transferred money | These actions indicate possible account compromise, social engineering success, or direct financial loss. |
| Job Scam | Upfront fee; money forwarding; overseas job without clear contract; deposit to unlock tasks | These indicators map to fake recruitment, labour-trafficking risk, task scams, and money mule recruitment. |
| Investment Scam | Guaranteed returns; licence verification; pressure to invest; online contact; withdrawal fee; suspicious platform | These questions map to unlicensed investment schemes, relationship-investment scams, and fake profit-withdrawal tactics. |
| Romance Scam | Online-only contact; fast emotional commitment; refusal to verify identity; money request; investment suggestion; already sent money, images, or sensitive information | These indicators map to romance scams, relationship-investment scams, and sextortion or personal-data misuse risk. |

### Phase 5 - Production Rules Design

After the assessment questions were defined, production rules were designed to convert the user's answers and pasted-message indicators into risk levels. This step was important because the chatbot needed to provide an explainable preliminary risk assessment, not only a general AI-generated opinion. The production rules act as the reasoning layer between the collected evidence and the final chatbot response.

The rules were written using an IF-THEN structure. Each IF condition represents a scam indicator or user action, while the THEN result defines the risk level and recommended response direction. For example, if a phishing user entered an OTP or password, the risk should be Critical because account compromise may already have occurred. If a parcel message asks the user to install an APK, the risk should also be escalated because malicious app installation is a serious warning sign.

The four risk levels were defined as Low, Medium, High, and Critical. Low is used when no strong warning signs are present, Medium is used when early warning signs exist but no direct loss or sensitive action has occurred, High is used when strong scam indicators are present, and Critical is used when the user may already be exposed to harm, such as sharing credentials, transferring money, installing a suspicious app, or sending sensitive content.

| Risk Level | Rule Meaning | Example Trigger |
|---|---|---|
| Low | No strong scam indicator is present, but caution is still advised. | A message looks generic and the user has not clicked, paid, or shared information. |
| Medium | Some warning signs are present, but there is not enough evidence for immediate high-risk escalation. | A message uses urgency but does not request payment, credentials, or a suspicious link. |
| High | Strong scam indicators are present and the user should stop and verify through official channels. | A message requests OTP, payment, login through a link, guaranteed returns, or upfront job fees. |
| Critical | The user may already be exposed to financial, account, malware, or personal-data harm. | User shared OTP, transferred money, installed APK, paid a withdrawal fee, or sent sensitive images. |

The following table shows examples of the production rules used in the chatbot design:

| Scam Type | Example Production Rule |
|---|---|
| Phishing | IF the pasted message asks for OTP, password, PIN, or banking details AND the user entered sensitive information, THEN classify the risk as Critical and advise immediate account protection steps. |
| Phishing | IF the user clicked a link but did not enter information, THEN classify the risk as Medium by default. IF the link or message also contains strong warning signs such as suspicious domain, urgency, impersonation, credential request, or threat, THEN classify the risk as High. |
| Parcel Scam | IF the parcel message asks the user to install an APK or unknown app, THEN classify the risk as High. IF the user already installed it, THEN classify the risk as Critical. |
| Parcel Scam | IF the parcel message asks for a delivery, customs, or rescheduling fee through a suspicious or unofficial-looking link, THEN classify the risk as High and advise verification through the official courier website or hotline. |
| Fake Bank Alert | IF the message or caller asks for OTP, PIN, password, or banking credentials, THEN classify the risk as High. IF the user already shared these details, THEN classify the risk as Critical. |
| Fake Bank Alert | IF the user was told to transfer money to a "safe account", THEN classify the risk as High. IF the user already transferred money, THEN classify the risk as Critical and advise contacting the bank and NSRC 997 immediately. |
| Job Scam | IF the job requires the user to pay registration, training, visa, equipment, or task-unlock fees before earning income, THEN classify the risk as High. |
| Job Scam | IF the job asks the user to receive money into their account and forward it to another person, THEN classify the risk as High because it may involve money mule recruitment. IF the user already received or forwarded money, THEN classify the risk as Critical. |
| Investment Scam | IF the investment promises guaranteed or unusually high returns AND the company cannot be verified as licensed, THEN classify the risk as High. |
| Investment Scam | IF the user is asked to pay a fee, tax, or deposit to withdraw investment profits, THEN classify the risk as High. IF the user already paid the fee or cannot withdraw transferred funds, THEN classify the risk as Critical. |
| Romance Scam | IF an online-only contact quickly builds a relationship and asks for money, gift cards, crypto, or emergency help, THEN classify the risk as High. |
| Romance Scam | IF the user already sent money, intimate images, or sensitive personal information, THEN classify the risk as Critical and advise immediate reporting and account-safety actions. |

The production rules were designed separately for each scam category because different scams require different escalation logic. For phishing, credential entry is the strongest indicator. For parcel scams, APK installation or payment is highly serious. For bank alerts, sharing banking details or transferring money is critical. For job scams, money forwarding and task deposits are severe indicators. For investment scams, withdrawal fees and unlicensed guaranteed returns are major red flags. For romance scams, money requests, investment pressure, and already-sent sensitive content strongly increase the risk.

These rules were later embedded into the autonomous result-node instructions. This allowed the AI to generate a natural-language explanation while still following a controlled rubric. As a result, the chatbot's output remains explainable because the final risk level can be traced back to specific warning signs and user answers.

### Phase 6 - Artifact-Level Input Design

The chatbot design also considered whether each scam type should rely mainly on guided questions or require the user to paste a suspicious artifact. Phishing, parcel-delivery scams, and fake bank alerts commonly begin with a specific message, link, phone number, or email that the user wants to check. For these three scam types, asking only generic questions would be weaker because the most useful evidence is often inside the message itself, such as the link wording, urgency, sender claim, payment request, credential request, APK instruction, or safe-account wording.

Therefore, these three flows were designed as input-first flows. The user first pastes the suspicious message, link, or bank alert. After that, the chatbot asks only a small number of action questions that the AI cannot reliably infer from the message alone, such as whether the user clicked the link, entered sensitive information, paid money, installed an app, called a number, or transferred funds. This separates message analysis from exposure assessment: the pasted content helps identify visible scam indicators, while the follow-up questions determine how urgent the user's situation is.

The same curated project knowledge base supports both the guided questions and the input-based assessment. In the autonomous result node, the AI reviews the pasted content against the scam indicators stored in the knowledge base and the production-rule rubric. It looks for visible patterns such as suspicious links, urgent wording, OTP or password requests, fake delivery fees, APK prompts, fake bank login links, suspicious phone numbers, and safe-account claims. It then combines those visible indicators with the user's action answers to produce the final Low, Medium, High, or Critical risk level.

The limitation was also defined clearly. ScamShield does not perform live malware scanning, real URL reputation scanning, QR-code analysis, or official account verification. It performs a preliminary risk assessment by interpreting visible message patterns and combining them with the user's follow-up answers.

### Phase 7 - Flowchart and Conversation Architecture Design

Before implementation in Botpress, draw.io flowcharts were created to plan the chatbot route, decision points, input/output steps, and final risk assessment flow. Standard flowchart conventions were used, including diamonds for decisions, rectangles for processing steps, and parallelograms for user input and chatbot output.

The main conversation architecture follows this sequence: Start, Greeting, `Scam_Type_Selection`, scam-specific branch, scam-specific questions or input collection, autonomous result generation, `Check_Another_Scam`, then either return to the scam menu or end the conversation. The `Scam_Type_Selection` node uses a `Single Choice` capture so the user can select one of the six scam categories.

The flow was split by scam type because each scam category has different evidence and escalation rules. For example, phishing requires link and credential-action questions, parcel scams require delivery, fee, and APK questions, while investment scams require licence, return, pressure, and withdrawal-fee checks.

### Phase 8 - Botpress Implementation

The chatbot was implemented in Botpress Studio using a combination of deterministic standard nodes and AI-assisted autonomous result nodes. `Standard Node` components were used for predictable conversation steps such as greeting, menu selection, raw input capture, and confirmation questions. `Capture Information` cards were used to store user answers into workflow variables, making the final assessment traceable.

The main menu was implemented using a `Single Choice` capture card inside the `Scam_Type_Selection` node. Phishing, parcel scam, and fake bank alert use `Raw Input` capture cards because users are expected to paste a suspicious message, link, or bank-alert text. Follow-up action questions use `Confirmation` cards because the required answers are binary and directly affect the final risk level.

The three input-based flows follow this structure: intro node, raw input node, action question node, and autonomous result node. This design ensures that the chatbot first receives the suspicious artifact, then asks only the action questions that AI cannot safely infer from the pasted content, such as whether the user already clicked, paid, installed an APK, shared credentials, or transferred money.

The job, investment, and romance scam flows use guided-question nodes instead of raw-input-first nodes. These scams are often scenario-based and may not involve a single pasted message. For these branches, the chatbot asks a sequence of confirmation questions that represent the key scam indicators before moving to the autonomous result node.

### Phase 9 - Autonomous Result-Node Instruction Design

Each scam branch ends with an `Autonomous Node` that generates the final AI-assisted assessment. The autonomous node receives the user's pasted input where applicable, captured workflow variables, a strict production-rule rubric, output format instructions, safety boundaries, and a transition instruction to continue to `Check_Another_Scam` after giving the result.

The autonomous node is used as an explanation layer, not as an unrestricted decision-maker. Its instructions require it to follow the defined production-rule rubric, avoid overclaiming technical certainty, and use only the supported risk levels: Low, Medium, High, and Critical. This keeps the output more consistent while still allowing the AI to explain the warning signs in natural language.

The required result format is standardised across all scam categories:

- `Risk level: Low / Medium / High / Critical`
- `Warning signs found`
- `Why this matters`
- `Recommended next steps`
- `Safety note`

The `workflow.transition` instruction was added to each autonomous result node so the conversation does not end immediately after the AI-generated assessment. After presenting the result, the chatbot transitions to `Check_Another_Scam`, where the user can choose whether to assess another scam type or finish the session.

### Phase 10 - Testing and Refinement

Testing was performed using the Botpress emulator with high-risk, critical-risk, and low/medium-risk sample cases for each scam type. The test cases were designed to check whether the chatbot could route users correctly, capture answers into the expected workflow variables, pass those variables into the autonomous result node, and produce a risk level consistent with the production-rule rubric.

Testing also checked whether the output included the required warning signs, explanation, recommended next steps, and safety note. The transition from each autonomous result node to `Check_Another_Scam` was also verified so that the conversation could continue after one assessment. The detailed test samples are documented in `BOTPRESS_BUILD_GUIDE.md`.

Refinement was performed iteratively. Early flow designs were simplified where they became too detailed for user understanding, while a separate technical flowchart and Botpress build guide were retained for implementation reference. The final design balances user-facing simplicity with enough internal structure to support explainable assessment.

### Phase 11 - Documentation and Handover Artifacts

The final phase focused on documentation so that the chatbot could be explained, evaluated, and recreated. The methodology documents the design approach, knowledge representation method, source-grounded question design, and development process. The draw.io flowcharts document the conversation logic visually, while `BOTPRESS_BUILD_GUIDE.md` records the Botpress nodes, cards, variables, autonomous prompts, transitions, and test cases needed to rebuild the chatbot.

This documentation is important because ScamShield is not only a chatbot prototype but also an academic AI project. The development artifacts show how the system moved from literature review and knowledge-base construction to implementation and testing, making the final chatbot transparent, reproducible, and academically defensible.

---

### Summary of Development Phases

| Phase | Stage | Key Deliverable |
|---|---|---|
| 1 | Problem Definition and Scope Selection | Problem statement, objectives, six scam categories, project boundaries |
| 2 | Literature Review and Gap Identification | Existing-solution review, technology review, project gap positioning |
| 3 | Knowledge Base Construction | Curated project knowledge base, scam profiles, red-flag library, action guides |
| 4 | Assessment Question Design | Source-informed yes/no questions mapped to scam indicators |
| 5 | Production Rules Design | IF-THEN rules, risk-level rubric, scam-specific escalation logic |
| 6 | Artifact-Level Input Design | Input-first assessment design for phishing, parcel scams, and fake bank alerts |
| 7 | Flowchart and Conversation Architecture Design | draw.io flowcharts, scam menu route, branch logic, result flow |
| 8 | Botpress Implementation | Standard nodes, capture cards, raw input cards, confirmation questions, autonomous result nodes |
| 9 | Autonomous Result-Node Instruction Design | Rule-constrained AI prompts, output format, safety boundaries, `workflow.transition` setup |
| 10 | Testing and Refinement | Botpress emulator tests, risk-level validation, transition checks, refined flows |
| 11 | Documentation and Handover Artifacts | Methodology, flowcharts, Botpress build guide, test samples |

Throughout all phases, the team followed an iterative workflow. Issues discovered during flowchart review or Botpress testing were refined before final documentation, resulting in a chatbot that is realistic in scope, locally relevant, explainable, and suitable for academic evaluation.
