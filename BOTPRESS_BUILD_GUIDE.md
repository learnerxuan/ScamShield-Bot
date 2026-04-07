# ScamShield Botpress Build Guide

This guide records the Botpress build steps completed so far. It is intended to help another person recreate the chatbot flow in Botpress Studio.

## Current Build Scope

The bot is being built as a rule-based scam assessment chatbot. The current implementation focuses on a menu-first flow where the user selects a scam type before answering scam-specific questions.

For now, the detailed build has been started for all six scam branches: phishing, parcel scam, fake bank alert, fake job offer, investment scam, and romance scam.

## Botpress Components Used So Far

Use these Botpress components:

- `Standard Node` for the rule-based conversation flow.
- `Capture Information` card for user answers.
- `Single Choice` capture for the main scam type menu.
- `Confirmation` capture for yes/no questions.
- `Raw Input` capture when the user pastes a suspicious message or link.
- `Autonomous Node` for AI-assisted assessment and result explanation using a strict production-rule rubric.

For the input-based scam types, the `Autonomous Node` is allowed to apply the production-rule rubric because the user provides a pasted message or link and the assignment requires an AI-chatbot design. The node must still be given strict instructions not to overclaim link safety and to follow the defined risk rubric.

## Autonomous Result Node Transition Setup

After testing, the `Autonomous Node` can continue to another node when a transition is configured properly. Each scam result node transitions to a common `Check_Another_Scam` node after the assessment result is given.

Build this result flow:

```text
Scam questions
-> Result generation
-> Check_Another_Scam
-> if yes: Scam Type Menu
-> if no: End
```

In each result `Autonomous Node`, add this exact sentence to the node instructions:

```text
After giving the assessment result, use workflow.transition to continue to the connected Check_Another_Scam node.
```

Also add a transition from the result node to `Check_Another_Scam`. Use this exact transition condition:

```text
After giving the assessment result, use workflow.transition to continue to the connected Check_Another_Scam node.
```

Use this transition setup for every scam result node.

Create a common `Standard Node` named:

```text
Check_Another_Scam
```

Add a `Capture Information` card.

Use:

```text
Confirmation
```

Question:

```text
Would you like to check another scam type?
```

Store result in:

```text
checkAnotherScam
```

Routing:

```text
Yes -> Scam Type Menu
No -> End
```

## Main Flow Created So Far

The current main flow is:

```text
Start
-> Greeting
-> Scam Type Menu
-> Phishing branch
```

The greeting node should contain this text:

```text
Welcome to ScamShield Bot. I can help you assess suspicious messages, links, and scam situations. Please choose the scam type you would like to check.
```

The scam type menu question should be:

```text
What would you like to check?
```

The menu choices should be:

```text
Phishing links and fake account-verification requests
Fake parcel-delivery messages
Fake bank alerts and bank-impersonation scams
Fake job offers
Investment scams
Romance scams
```

Store the menu result in:

```text
selectedScamType
```

## Phishing Flow Design

After the user selects the phishing option, the flow should follow this order:

```text
Phishing_Intro
-> Phishing_Input_Text
-> Phishing_Action_Questions
-> Phishing_Result
```

This follows the revised version 4 design: for phishing, the user is expected to provide the suspicious message or link because that is the item they want to check. The bot then asks only short action/context questions that cannot be reliably known from the pasted content.

The current phishing implementation uses a hybrid design:

```text
Pasted message/link + user action answers
-> Autonomous Node applies production-rule rubric
-> AI-generated risk result and advice
```

This keeps the system as an AI chatbot while still grounding the assessment in explicit production-rule instructions.

## Phishing Intro Node

Create a `Standard Node` named:

```text
Phishing_Intro
```

Add a text card:

```text
You selected phishing links and fake account-verification requests. I can review the situation using a pasted message or link and a few follow-up questions.
```

## Phishing Message or Link Input

Create a `Standard Node` named:

```text
Phishing_Input_Text
```

Add a `Capture Information` card.

Use:

```text
Raw Input
```

Question:

```text
Please paste the message or link here. Do not enter any OTP, password, PIN, or banking details.
```

Store result in:

```text
phishingInputText
```

Connect:

```text
Phishing_Intro -> Phishing_Input_Text
```

No separate `Phishing_AI_Extract` node is currently used. The pasted message or link is already stored in `phishingInputText`, so the final autonomous result node can review it directly.

## Phishing Action Questions

Do not ask the user to judge whether the message is suspicious. The AI can inspect the pasted content for visible warning signs such as suspicious wording, urgency, credential requests, and unusual links.

Only ask action/context questions that the AI cannot know from the pasted message alone.

Create a `Standard Node` named:

```text
Phishing_Action_Questions
```

Add these `Capture Information` cards using `Confirmation`.

Question 1:

```text
Did you already click the link?
```

Store result in:

```text
phishingClickedLink
```

Question 2:

```text
Did you enter any OTP, password, PIN, banking details, or personal information?
```

Store result in:

```text
phishingEnteredSenInfo
```

Connect:

```text
Phishing_Input_Text -> Phishing_Action_Questions
```

## Phishing Result Node

Create an `Autonomous Node` named:

```text
Phishing_Result
```

Connect:

```text
Phishing_Action_Questions -> Phishing_Result
```

Use this instruction text:

```text
You are ScamShield Bot, a Malaysian scam-awareness assistant.

Assess the user's pasted phishing message or link using the production-rule rubric below.

User pasted message or link:
{{workflow.phishingInputText}}

User action answers:
- Already clicked the link: {{workflow.phishingClickedLink}}
- Entered OTP, password, PIN, banking details, or personal information: {{workflow.phishingEnteredSenInfo}}

Production-rule rubric:
- If the user entered OTP, password, PIN, banking details, or personal information, classify the risk as Critical.
- If the user clicked the link but did not enter sensitive information, classify the risk as High.
- If the pasted message asks for OTP, password, PIN, banking details, account verification, account unlocking, or login through a link, classify the risk as High or Critical depending on severity.
- If the pasted message contains urgent, threatening, or fear-based wording such as account suspension, blocked account, refund expiry, legal action, or immediate verification, increase the risk.
- If the pasted message contains a shortened URL, random-looking domain, unusual spelling, or a domain that does not clearly match the claimed organization, increase the risk.
- If the pasted input is only a link and there is not enough context, do not say the link is safe. Explain that the assessment is limited and recommend verifying through the official app or website.
- If there are no strong warning signs and the user did not click or enter information, classify the risk as Low or Medium, but still advise caution.

Important rules:
- Do not claim that a link is definitely safe or definitely malicious.
- Do not ask for OTP, password, PIN, or banking details.
- Do not override the user's action answers.
- Use only Low, Medium, High, or Critical as the risk level.
- Keep the answer clear and practical.

Return the answer in this format:

Assessment result:
Risk level: [Low / Medium / High / Critical]

Warning signs found:
- [list visible warning signs from the pasted message or link]

Review of pasted message or link:
- [briefly explain any visible suspicious patterns]

Why this matters:
[short explanation]

Recommended next steps:
- [give practical next steps]

Safety note:
This is a preliminary AI-assisted rule-based assessment. It does not perform live website verification and cannot guarantee whether a link is safe.
```

## Current Test To Run

Test only the phishing branch for now.

Expected test flow:

```text
Start
-> Greeting appears
-> Scam type menu appears
-> Select phishing option
-> Phishing intro appears
-> Bot asks user to paste the suspicious message or link
-> Bot asks whether the user clicked the link
-> Bot asks whether the user entered sensitive information
-> Phishing_Result generates the AI-assisted rule-based assessment
```

At this stage, the phishing branch should produce an AI-generated final explanation based on the pasted input and the two user action answers.

## Next Step

The next step is to build the parcel scam branch using the same input-first pattern.

## Parcel Scam Flow Design

After the user selects the parcel scam option, the flow should follow this order:

```text
Parcel_Intro
-> Parcel_Input_Text
-> Parcel_Action_Questions
-> Parcel_Result
```

This branch is also input-first because the user is expected to have a suspicious parcel message or delivery link they want the bot to check.

## Parcel Intro Node

Create a `Standard Node` named:

```text
Parcel_Intro
```

Add a text card:

```text
You selected fake parcel-delivery messages. Please paste the suspicious delivery message or link so I can assess visible warning signs and ask a few safety questions.
```

## Parcel Message or Link Input

Create a `Standard Node` named:

```text
Parcel_Input_Text
```

Add a `Capture Information` card.

Use:

```text
Raw Input
```

Question:

```text
Paste the suspicious parcel message or delivery link here. Do not enter any banking details, OTP, password, or PIN.
```

Store result in:

```text
parcelInputText
```

Connect:

```text
Parcel_Intro -> Parcel_Input_Text
```

## Parcel Action Questions

Create a `Standard Node` named:

```text
Parcel_Action_Questions
```

Add these `Capture Information` cards using `Confirmation`.

Question 1:

```text
Were you expecting a parcel or delivery from this company?
```

Store result in:

```text
parcelExpectedDelivery
```

Question 2:

```text
Did you already pay any delivery, customs, rescheduling, or clearance fee?
```

Store result in:

```text
parcelAlreadyPaid
```

Question 3:

```text
Did you install any app or APK from the message?
```

Store result in:

```text
parcelInstalledApp
```

Connect:

```text
Parcel_Input_Text -> Parcel_Action_Questions
```

## Parcel Result Node

Create an `Autonomous Node` named:

```text
Parcel_Result
```

Connect:

```text
Parcel_Action_Questions -> Parcel_Result
```

Use this instruction text:

```text
You are ScamShield Bot, a Malaysian scam-awareness assistant.

Assess the user's pasted parcel-delivery message or link using the production-rule rubric below.

User pasted message or link:
{{workflow.parcelInputText}}

User action answers:
- User was expecting a delivery from this company: {{workflow.parcelExpectedDelivery}}
- User already paid a delivery, customs, rescheduling, or clearance fee: {{workflow.parcelAlreadyPaid}}
- User installed an app or APK from the message: {{workflow.parcelInstalledApp}}

Production-rule rubric:
- If the user installed an app or APK from the message, classify the risk as Critical.
- If the user already paid a delivery, customs, rescheduling, or clearance fee, classify the risk as High or Critical depending on the message.
- If the pasted message asks for a delivery fee, customs fee, clearance fee, redelivery fee, or rescheduling fee, increase the risk.
- If the pasted message asks the user to click a tracking, redelivery, or rescheduling link, increase the risk.
- If the pasted message asks the user to install an app or APK, classify the risk as Critical.
- If the message claims illegal parcel contents, customs investigation, police involvement, or asks the user to move money to a safe account, classify the risk as Critical.
- If the user was not expecting a parcel from this company and the message contains a link or payment request, classify the risk as High.
- If the pasted input is only a link and there is not enough context, do not say the link is safe. Explain that the assessment is limited and recommend verifying through the courier's official website or app.
- If there are no strong warning signs and the user was expecting a parcel, classify the risk as Low or Medium, but still advise caution.

Important rules:
- Do not claim that the link is definitely safe or definitely malicious.
- Do not ask for OTP, password, PIN, or banking details.
- Do not override the user's action answers.
- Use only Low, Medium, High, or Critical as the risk level.
- Keep the answer clear and practical.

Return the answer in this format:

Assessment result:
Risk level: [Low / Medium / High / Critical]

Warning signs found:
- [list visible warning signs from the pasted parcel message or link]

Review of pasted message or link:
- [briefly explain any visible suspicious patterns]

Why this matters:
[short explanation]

Recommended next steps:
- [give practical next steps]

Safety note:
This is a preliminary AI-assisted rule-based assessment. It does not perform live website verification and cannot guarantee whether a link is safe.
```

## Parcel Test Case

Test with this pasted message:

```text
Your parcel delivery failed. Pay RM2.00 redelivery fee now at http://pos-delivery-update.xyz or your parcel will be returned.
```

Answer:

```text
Expected delivery: No
Already paid: No
Installed app/APK: No
```

Expected result:

```text
High
```

Reason:

```text
The message contains a redelivery fee request, urgency, and a suspicious delivery link.
```

## Fake Bank Alert Flow Design

After the user selects the fake bank alert or bank impersonation option, the flow should follow this order:

```text
Bank_Intro
-> Bank_Input_Text
-> Bank_Action_Questions
-> Bank_Result
```

This branch is input-first because the user is expected to have a suspicious bank alert, phone number, or link they want the bot to check.

## Bank Intro Node

Create a `Standard Node` named:

```text
Bank_Intro
```

Add a text card:

```text
You selected fake bank alerts and bank-impersonation scams. Please paste the suspicious bank message, alert, phone number, or link so I can assess visible warning signs and ask a few safety questions.
```

## Bank Message, Phone Number, or Link Input

Create a `Standard Node` named:

```text
Bank_Input_Text
```

Add a `Capture Information` card.

Use:

```text
Raw Input
```

Question:

```text
Paste the suspicious bank message, alert, phone number, or link here. Do not enter any OTP, password, PIN, card number, or banking details.
```

Store result in:

```text
bankInputText
```

Connect:

```text
Bank_Intro -> Bank_Input_Text
```

## Bank Action Questions

Create a `Standard Node` named:

```text
Bank_Action_Questions
```

Add these `Capture Information` cards using `Confirmation`.

Question 1:

```text
Did you click the link or call the phone number shown in the message?
```

Store result in:

```text
bankClickedOrCalled
```

Question 2:

```text
Did you share any OTP, password, PIN, card number, or banking details?
```

Store result in:

```text
bankSharedSenInfo
```

Question 3:

```text
Did you transfer money to any account after receiving the message or call?
```

Store result in:

```text
bankTransferredMoney
```

Connect:

```text
Bank_Input_Text -> Bank_Action_Questions
```

## Bank Result Node

Create an `Autonomous Node` named:

```text
Bank_Result
```

Connect:

```text
Bank_Action_Questions -> Bank_Result
```

Use this instruction text:

```text
You are ScamShield Bot, a Malaysian scam-awareness assistant.

Assess the user's pasted bank alert, bank-impersonation message, phone number, or link using the production-rule rubric below.

User pasted message, phone number, or link:
{{workflow.bankInputText}}

User action answers:
- User clicked the link or called the number shown in the message: {{workflow.bankClickedOrCalled}}
- User shared OTP, password, PIN, card number, or banking details: {{workflow.bankSharedSenInfo}}
- User transferred money after receiving the message or call: {{workflow.bankTransferredMoney}}

Production-rule rubric:
- If the user shared OTP, password, PIN, card number, or banking details, classify the risk as Critical.
- If the user transferred money after receiving the message or call, classify the risk as Critical.
- If the message asks the user to transfer money to a "safe account", "secure account", "BNM account", "police account", or any third-party account, classify the risk as Critical.
- If the message asks the user to click a login/security link or call a number shown in the message, increase the risk.
- If the message asks for OTP, password, PIN, card number, TAC, Secure2u approval, or banking details, classify the risk as High or Critical depending on the context.
- If the message claims suspicious transactions, account freeze, blocked account, unauthorized login, legal action, or urgent verification, increase the risk.
- If the pasted input is only a phone number or link and there is not enough context, do not say it is safe. Explain that the assessment is limited and recommend contacting the bank using the official number from the bank's website or app.
- If there are no strong warning signs and the user did not click, share details, or transfer money, classify the risk as Low or Medium, but still advise caution.

Important rules:
- Do not claim that a link or phone number is definitely safe.
- Do not ask for OTP, password, PIN, card number, or banking details.
- Do not override the user's action answers.
- Use only Low, Medium, High, or Critical as the risk level.
- Keep the answer clear and practical.
- For urgent banking loss or recent money transfer in Malaysia, advise the user to immediately contact their bank's official hotline and NSRC 997.

Return the answer in this format:

Assessment result:
Risk level: [Low / Medium / High / Critical]

Warning signs found:
- [list visible warning signs from the pasted bank alert, phone number, or link]

Review of pasted message, phone number, or link:
- [briefly explain any visible suspicious patterns]

Why this matters:
[short explanation]

Recommended next steps:
- [give practical next steps]

Safety note:
This is a preliminary AI-assisted rule-based assessment. It does not perform live website or phone-number verification and cannot guarantee whether a link or number is safe.
```

## Bank Test Case

Test with this pasted message:

```text
RM0.00 Maybank: Suspicious login detected. Secure your account now at http://maybank-security-check.xyz or call 012-5558888 immediately.
```

Answer:

```text
Clicked or called: No
Shared sensitive info: No
Transferred money: No
```

Expected result:

```text
High
```

Reason:

```text
The message contains bank impersonation, a suspicious login claim, urgent wording, and a suspicious security link.
```

## Fake Job Offer Flow Design

After the user selects the fake job offer option, the flow should follow this order:

```text
Job_Intro
-> Job_Questions
-> Job_Result
```

This branch is guided-question first because job scams usually depend on context rather than one pasted message alone.

## Job Intro Node

Create a `Standard Node` named:

```text
Job_Intro
```

Add a text card:

```text
You selected fake job offers. I will ask a few questions about the job offer to assess whether it matches common job scam patterns.
```

## Job Questions Node

Create a `Standard Node` named:

```text
Job_Questions
```

Add these `Capture Information` cards using `Confirmation`.

Question 1:

```text
Did the job offer come through WhatsApp, Telegram, social media, or an unsolicited message?
```

Store result in:

```text
jobUnsolicitedMessage
```

Question 2:

```text
Were you asked to pay any fee before starting, such as registration, training, visa, deposit, or equipment fees?
```

Store result in:

```text
jobUpfrontFee
```

Question 3:

```text
Did the job promise unusually high income for simple online tasks?
```

Store result in:

```text
jobUnrealisticPay
```

Question 4:

```text
Were you asked to receive money into your bank account and transfer it to someone else?
```

Store result in:

```text
jobForwardMoney
```

Question 5:

```text
Is the job overseas without a proper contract, verified employer, or clear legal employment process?
```

Store result in:

```text
jobOverseasNoContract
```

Question 6:

```text
Were you asked to deposit money to unlock higher-paying tasks or commissions?
```

Store result in:

```text
jobDepositUnlockTasks
```

Connect:

```text
Job_Intro -> Job_Questions
```

## Job Result Node

Create an `Autonomous Node` named:

```text
Job_Result
```

Connect:

```text
Job_Questions -> Job_Result
```

Use this instruction text:

```text
You are ScamShield Bot, a Malaysian scam-awareness assistant.

Assess the user's job offer situation using the production-rule rubric below.

User answers:
- Job offer came through WhatsApp, Telegram, social media, or unsolicited message: {{workflow.jobUnsolicitedMessage}}
- Asked to pay upfront registration, training, visa, deposit, or equipment fee: {{workflow.jobUpfrontFee}}
- Promised unusually high income for simple online tasks: {{workflow.jobUnrealisticPay}}
- Asked to receive money into bank account and transfer it to someone else: {{workflow.jobForwardMoney}}
- Overseas job without proper contract, verified employer, or clear legal employment process: {{workflow.jobOverseasNoContract}}
- Asked to deposit money to unlock higher-paying tasks or commissions: {{workflow.jobDepositUnlockTasks}}

Production-rule rubric:
- If the user was asked to receive and forward money, classify the risk as Critical because this may indicate money mule recruitment.
- If the job is overseas without a proper contract, verified employer, or clear legal process, classify the risk as Critical or High depending on the context.
- If the user was asked to deposit money to unlock higher-paying tasks or commissions, classify the risk as Critical.
- If the user was asked to pay an upfront fee before starting, classify the risk as High.
- If the job promises unusually high income for simple online tasks and came through an unsolicited message, classify the risk as Medium or High.
- If only one weak indicator is present, classify the risk as Low or Medium but advise caution.
- If there are no strong warning signs, classify the risk as Low.

Important rules:
- Do not guarantee that a job is legitimate.
- Do not ask for passport numbers, bank details, NRIC, passwords, or OTP.
- Do not override the user's answers.
- Use only Low, Medium, High, or Critical as the risk level.
- Keep the answer clear and practical.

Return the answer in this format:

Assessment result:
Risk level: [Low / Medium / High / Critical]

Warning signs found:
- [list warning signs based on the user's answers]

Why this matters:
[short explanation]

Recommended next steps:
- [give practical next steps]

Safety note:
This is a preliminary AI-assisted rule-based assessment. It cannot verify whether an employer is legitimate.
```

## Job Test Case

Answer:

```text
Unsolicited message: Yes
Upfront fee: Yes
Unrealistic pay: Yes
Forward money: No
Overseas no contract: No
Deposit to unlock tasks: No
```

Expected result:

```text
High
```

Reason:

```text
The job offer contains an unsolicited approach, upfront payment request, and unrealistic income claim.
```

## Investment Scam Flow Design

After the user selects the investment scam option, the flow should follow this order:

```text
Investment_Intro
-> Investment_Questions
-> Investment_Result
```

This branch is guided-question first because investment scams usually depend on offer claims, licensing, pressure tactics, and user context.

## Investment Intro Node

Create a `Standard Node` named:

```text
Investment_Intro
```

Add a text card:

```text
You selected investment scams. I will ask a few questions about the investment offer to assess whether it matches common investment scam patterns.
```

## Investment Questions Node

Create a `Standard Node` named:

```text
Investment_Questions
```

Add these `Capture Information` cards using `Confirmation`.

Question 1:

```text
Does the investment promise guaranteed or unusually high returns?
```

Store result in:

```text
investmentGuaranteedReturns
```

Question 2:

```text
Can the company or person be verified as licensed or registered with the relevant regulator?
```

Store result in:

```text
investmentLicensedVerified
```

Question 3:

```text
Are you being pressured to invest quickly or keep the opportunity secret?
```

Store result in:

```text
investmentPressure
```

Question 4:

```text
Was this investment recommended by someone you met online or recently became close to?
```

Store result in:

```text
investmentOnlineContact
```

Question 5:

```text
Have you been asked to pay a fee, tax, or deposit before you can withdraw your profits?
```

Store result in:

```text
investmentWithdrawalFee
```

Question 6:

```text
Is the investment using an app, website, or platform that looks unverified or unfamiliar to you?
```

Store result in:

```text
investmentSuspiciousPlatform
```

Connect:

```text
Investment_Intro -> Investment_Questions
```

## Investment Result Node

Create an `Autonomous Node` named:

```text
Investment_Result
```

Connect:

```text
Investment_Questions -> Investment_Result
```

Use this instruction text:

```text
You are ScamShield Bot, a Malaysian scam-awareness assistant.

Assess the user's investment offer using the production-rule rubric below.

User answers:
- Investment promises guaranteed or unusually high returns: {{workflow.investmentGuaranteedReturns}}
- Company or person can be verified as licensed or registered with the relevant regulator: {{workflow.investmentLicensedVerified}}
- User is pressured to invest quickly or keep it secret: {{workflow.investmentPressure}}
- Investment was recommended by someone met online or recently became close to: {{workflow.investmentOnlineContact}}
- User was asked to pay a fee, tax, or deposit before withdrawing profits: {{workflow.investmentWithdrawalFee}}
- Investment uses an app, website, or platform that looks unverified or unfamiliar: {{workflow.investmentSuspiciousPlatform}}

Production-rule rubric:
- If the user was asked to pay a fee, tax, or deposit before withdrawing profits, classify the risk as Critical.
- If the investment promises guaranteed or unusually high returns and cannot be verified as licensed or registered, classify the risk as High or Critical.
- If the investment was recommended by someone met online and uses an unfamiliar platform, classify the risk as High or Critical because this may indicate a relationship-investment scam.
- If the user is pressured to invest quickly or keep the opportunity secret, increase the risk.
- If the investment cannot be verified as licensed or registered, increase the risk.
- If only one weak indicator is present, classify the risk as Low or Medium but advise caution.
- If there are no strong warning signs and the company is verified as licensed or registered, classify the risk as Low.

Important rules:
- Do not guarantee that an investment is legitimate or profitable.
- Do not provide financial advice.
- Do not ask for bank details, passwords, OTP, NRIC, or account numbers.
- Do not override the user's answers.
- Use only Low, Medium, High, or Critical as the risk level.
- Keep the answer clear and practical.
- Advise the user to verify investment entities through official regulator channels such as SC Malaysia Investment Checker or BNM resources.

Return the answer in this format:

Assessment result:
Risk level: [Low / Medium / High / Critical]

Warning signs found:
- [list warning signs based on the user's answers]

Why this matters:
[short explanation]

Recommended next steps:
- [give practical next steps]

Safety note:
This is a preliminary AI-assisted rule-based assessment. It cannot verify whether an investment is legitimate or suitable for you.
```

## Investment Test Case

Answer:

```text
Guaranteed returns: Yes
Licensed verified: No
Pressure: Yes
Online contact: No
Withdrawal fee: No
Suspicious platform: Yes
```

Expected result:

```text
High
```

Reason:

```text
The investment offer contains guaranteed returns, cannot be verified as licensed, pressure to invest, and a suspicious platform.
```

## Romance Scam Flow Design

After the user selects the romance scam option, the flow should follow this order:

```text
Romance_Intro
-> Romance_Questions
-> Romance_Result
```

This branch is guided-question first because romance scams usually depend on relationship context, emotional pressure, identity verification, and financial requests.

## Romance Intro Node

Create a `Standard Node` named:

```text
Romance_Intro
```

Add a text card:

```text
You selected romance scams. I will ask a few questions about the relationship or online contact to assess whether it matches common romance scam patterns.
```

## Romance Questions Node

Create a `Standard Node` named:

```text
Romance_Questions
```

Add these `Capture Information` cards using `Confirmation`.

Question 1:

```text
Did you meet this person online and not in real life?
```

Store result in:

```text
romanceMetOnlineOnly
```

Question 2:

```text
Did the relationship become serious very quickly?
```

Store result in:

```text
romanceSeriousQuickly
```

Question 3:

```text
Has the person refused a live video call or avoided verifying their identity?
```

Store result in:

```text
romanceRefusedVerification
```

Question 4:

```text
Has the person asked for money, cryptocurrency, gift cards, or help with an emergency?
```

Store result in:

```text
romanceAskedMoney
```

Question 5:

```text
Has the person encouraged you to invest in a platform they recommended?
```

Store result in:

```text
romanceEncouragedInvestment
```

Question 6:

```text
Have you already sent money, intimate images, or sensitive personal information?
```

Store result in:

```text
romanceAlreadySent
```

Connect:

```text
Romance_Intro -> Romance_Questions
```

## Romance Result Node

Create an `Autonomous Node` named:

```text
Romance_Result
```

Connect:

```text
Romance_Questions -> Romance_Result
```

Use this instruction text:

```text
You are ScamShield Bot, a Malaysian scam-awareness assistant.

Assess the user's romance scam situation using the production-rule rubric below.

User answers:
- Met this person online and not in real life: {{workflow.romanceMetOnlineOnly}}
- Relationship became serious very quickly: {{workflow.romanceSeriousQuickly}}
- Person refused live video call or avoided verifying identity: {{workflow.romanceRefusedVerification}}
- Person asked for money, cryptocurrency, gift cards, or emergency help: {{workflow.romanceAskedMoney}}
- Person encouraged user to invest in a platform they recommended: {{workflow.romanceEncouragedInvestment}}
- User already sent money, intimate images, or sensitive personal information: {{workflow.romanceAlreadySent}}

Production-rule rubric:
- If the user already sent money, intimate images, or sensitive personal information, classify the risk as Critical.
- If the person asked for money and refused verification, classify the risk as High or Critical.
- If the person encouraged investment in a platform they recommended, classify the risk as High or Critical because this may indicate a romance-investment scam.
- If the relationship became serious quickly and the person was met online only, increase the risk.
- If the person refused video call or identity verification, increase the risk.
- If only early weak indicators are present and there is no money request, classify the risk as Low or Medium but advise caution.
- If there are no strong warning signs, classify the risk as Low.

Important rules:
- Do not shame or blame the user.
- Do not ask for intimate images, financial account details, passwords, OTP, or personal identification numbers.
- Do not override the user's answers.
- Use only Low, Medium, High, or Critical as the risk level.
- Keep the answer clear, sensitive, and practical.
- If intimate images were shared and threats are involved, advise the user not to pay, preserve evidence, and seek help from relevant authorities/platform reporting channels.

Return the answer in this format:

Assessment result:
Risk level: [Low / Medium / High / Critical]

Warning signs found:
- [list warning signs based on the user's answers]

Why this matters:
[short explanation]

Recommended next steps:
- [give practical next steps]

Safety note:
This is a preliminary AI-assisted rule-based assessment. It cannot verify whether a person is genuine.
```

## Romance Test Case

Answer:

```text
Met online only: Yes
Serious quickly: Yes
Refused verification: Yes
Asked money: Yes
Encouraged investment: No
Already sent: No
```

Expected result:

```text
High
```

Reason:

```text
The situation contains an online-only relationship, fast emotional escalation, refusal to verify identity, and a money request.
```

## Full Test Samples

Use these samples to test whether each scam branch produces the expected risk level and advice. Exact wording may differ because result nodes are AI-assisted, but the risk level should remain consistent with the production-rule rubric.

### Phishing Test Samples

Test 1:

```text
Pasted input: Maybank Alert: Your account has been temporarily locked due to suspicious activity. Verify your account immediately at http://maybank-secure-login.xyz to avoid suspension.
Clicked link: No
Entered sensitive information: No
Expected risk: High
Reason: Fake account verification, suspicious link, urgency, and bank impersonation.
```

Test 2:

```text
Pasted input: Your email storage is full. Login now at http://mailbox-update-support.com and enter your password to keep your account active.
Clicked link: Yes
Entered sensitive information: Yes
Expected risk: Critical
Reason: User entered sensitive information after a login/password request.
```

Test 3:

```text
Pasted input: Reminder: Your monthly statement is ready. Open your official banking app to view it.
Clicked link: No
Entered sensitive information: No
Expected risk: Low or Medium
Reason: No direct suspicious link, credential request, or urgent threat is visible, but the user should still verify through official channels.
```

### Parcel Scam Test Samples

Test 1:

```text
Pasted input: Your parcel delivery failed. Pay RM2.00 redelivery fee now at http://pos-delivery-update.xyz or your parcel will be returned.
Expected delivery: No
Already paid: No
Installed app/APK: No
Expected risk: High
Reason: Redelivery fee request, urgency, and suspicious delivery link.
```

Test 2:

```text
Pasted input: J&T Express: Your package is held by customs. Download this APK to confirm your delivery details: http://delivery-check-app.xyz/app.apk
Expected delivery: No
Already paid: No
Installed app/APK: Yes
Expected risk: Critical
Reason: APK installation from a delivery message is a severe warning sign.
```

Test 3:

```text
Pasted input: DHL: Your package tracking number is 123456789. Track it using the official DHL website.
Expected delivery: Yes
Already paid: No
Installed app/APK: No
Expected risk: Low or Medium
Reason: No payment request, APK instruction, or suspicious urgent action is visible, but the user should verify through the official courier site.
```

### Fake Bank Alert Test Samples

Test 1:

```text
Pasted input: RM0.00 Maybank: Suspicious login detected. Secure your account now at http://maybank-security-check.xyz or call 012-5558888 immediately.
Clicked or called: No
Shared sensitive info: No
Transferred money: No
Expected risk: High
Reason: Bank impersonation, suspicious login claim, urgent wording, and suspicious security link.
```

Test 2:

```text
Pasted input: Bank Negara alert: Your account is involved in suspicious activity. Transfer your funds to a safe account now to avoid legal action.
Clicked or called: Yes
Shared sensitive info: No
Transferred money: Yes
Expected risk: Critical
Reason: Safe-account transfer instruction and money transfer after the message.
```

Test 3:

```text
Pasted input: Your bank statement is available. Please check it through your official banking app.
Clicked or called: No
Shared sensitive info: No
Transferred money: No
Expected risk: Low or Medium
Reason: No visible OTP request, suspicious link, fake hotline, safe-account instruction, or urgent threat.
```

### Fake Job Offer Test Samples

Test 1:

```text
Unsolicited message: Yes
Upfront fee: Yes
Unrealistic pay: Yes
Forward money: No
Overseas no contract: No
Deposit to unlock tasks: No
Expected risk: High
Reason: Unsolicited approach, upfront payment request, and unrealistic income claim.
```

Test 2:

```text
Unsolicited message: Yes
Upfront fee: No
Unrealistic pay: Yes
Forward money: Yes
Overseas no contract: No
Deposit to unlock tasks: No
Expected risk: Critical
Reason: Receiving and forwarding money may indicate money mule recruitment.
```

Test 3:

```text
Unsolicited message: No
Upfront fee: No
Unrealistic pay: No
Forward money: No
Overseas no contract: No
Deposit to unlock tasks: No
Expected risk: Low
Reason: No major job scam warning signs are present.
```

### Investment Scam Test Samples

Test 1:

```text
Guaranteed returns: Yes
Licensed verified: No
Pressure: Yes
Online contact: No
Withdrawal fee: No
Suspicious platform: Yes
Expected risk: High
Reason: Guaranteed returns, unverified licensing, pressure to invest, and suspicious platform.
```

Test 2:

```text
Guaranteed returns: Yes
Licensed verified: No
Pressure: Yes
Online contact: Yes
Withdrawal fee: Yes
Suspicious platform: Yes
Expected risk: Critical
Reason: Withdrawal fee before receiving profits is a critical investment scam indicator.
```

Test 3:

```text
Guaranteed returns: No
Licensed verified: Yes
Pressure: No
Online contact: No
Withdrawal fee: No
Suspicious platform: No
Expected risk: Low
Reason: No major investment scam warning signs are present, assuming the entity is genuinely verified through official regulator channels.
```

### Romance Scam Test Samples

Test 1:

```text
Met online only: Yes
Serious quickly: Yes
Refused verification: Yes
Asked money: Yes
Encouraged investment: No
Already sent: No
Expected risk: High
Reason: Online-only relationship, fast emotional escalation, refusal to verify identity, and money request.
```

Test 2:

```text
Met online only: Yes
Serious quickly: Yes
Refused verification: Yes
Asked money: Yes
Encouraged investment: Yes
Already sent: Yes
Expected risk: Critical
Reason: User already sent money or sensitive content, with romance-investment and money-request indicators.
```

Test 3:

```text
Met online only: Yes
Serious quickly: No
Refused verification: No
Asked money: No
Encouraged investment: No
Already sent: No
Expected risk: Low or Medium
Reason: Online-only contact alone is not enough for high risk, but caution is still appropriate.
```
