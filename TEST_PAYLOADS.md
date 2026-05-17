# ScamShield Bot Test Payloads

This file contains test cases for each scam branch. Each scam type includes Low, Medium, High, and Critical scenarios. Use these in the Botpress Emulator to verify routing, captured answers, risk level, warning signs, and recommended next steps.

## Phishing Links and Fake Account Verification

### Low Risk

Pasted message:

```text
Your monthly Microsoft account activity summary is ready. Open your Microsoft account dashboard by typing microsoft.com into your browser or using the official app.
```

Answers:

```text
Clicked link: No
Entered OTP/password/PIN/banking/personal info: No
```

Expected risk:

```text
Low
```

Expected reason:

```text
No suspicious link, credential request, urgency, or user exposure is visible.
```

### Medium Risk

Pasted message:

```text
Your email storage is almost full. Please review your mailbox settings soon to avoid service interruption.
```

Answers:

```text
Clicked link: No
Entered OTP/password/PIN/banking/personal info: No
```

Expected risk:

```text
Medium
```

Expected reason:

```text
The message creates mild account-pressure wording, but there is no visible link, credential request, or confirmed user exposure.
```

### High Risk

Pasted message:

```text
Your Microsoft 365 mailbox has been temporarily locked due to unusual sign-in activity. Verify your account within 2 hours to avoid permanent suspension:

https://microsoft-security-verify-login.com

Failure to verify may disable your Outlook and OneDrive access.
```

Answers:

```text
Clicked link: Yes
Entered OTP/password/PIN/banking/personal info: No
```

Expected risk:

```text
High
```

Expected reason:

```text
User clicked the link and the message contains a suspicious unofficial domain, impersonation, urgency, and account-verification pressure.
```

### Critical Risk

Pasted message:

```text
Security Alert: Your Maybank2u account has been locked. Verify your account immediately at:

https://maybank2u-secure-verify.com

Enter your username, password, and TAC to restore access.
```

Answers:

```text
Clicked link: Yes
Entered OTP/password/PIN/banking/personal info: Yes
```

Expected risk:

```text
Critical
```

Expected reason:

```text
The user entered sensitive banking or authentication information after receiving a suspicious verification link.
```

## Fake Parcel-Delivery Messages

### Low Risk

Pasted message:

```text
DHL: Your parcel tracking number is 123456789. Track your parcel using the official DHL website or app.
```

Answers:

```text
Expected delivery: Yes
Already paid fee: No
Installed app/APK: No
```

Expected risk:

```text
Low
```

Expected reason:

```text
The user expected the parcel and the message does not request payment, app installation, credentials, or urgent action through a suspicious link.
```

### Medium Risk

Pasted message:

```text
Your parcel could not be delivered today. Please check your delivery status using the courier website.
```

Answers:

```text
Expected delivery: No
Already paid fee: No
Installed app/APK: No
```

Expected risk:

```text
Medium
```

Expected reason:

```text
The parcel message is unexpected, but there is no visible payment request, suspicious URL, APK instruction, or confirmed user exposure.
```

### High Risk

Pasted message:

```text
POS Malaysia: Your parcel delivery failed because of incomplete address details. Pay RM2.00 redelivery fee within 24 hours or your parcel will be returned.

https://pos-redelivery-update.com
```

Answers:

```text
Expected delivery: No
Already paid fee: No
Installed app/APK: No
```

Expected risk:

```text
High
```

Expected reason:

```text
The message contains an unexpected parcel claim, payment request, urgency, and suspicious redelivery link.
```

### Critical Risk

Pasted message:

```text
J&T Express: Your package is held by customs. Download this delivery verification app to confirm your address:

http://delivery-check-app.xyz/app.apk
```

Answers:

```text
Expected delivery: No
Already paid fee: No
Installed app/APK: Yes
```

Expected risk:

```text
Critical
```

Expected reason:

```text
The user installed an app/APK from a suspicious parcel message, which creates possible malware or account-compromise risk.
```

## Fake Bank Alerts and Bank-Impersonation Scams

### Low Risk

Pasted message:

```text
Your bank statement is available. Please check it through your official banking app.
```

Answers:

```text
Clicked link or called number: No
Shared OTP/PIN/password/banking details: No
Transferred money: No
```

Expected risk:

```text
Low
```

Expected reason:

```text
No suspicious link, fake hotline, OTP request, safe-account instruction, or user exposure is visible.
```

### Medium Risk

Pasted message:

```text
Bank alert: A login attempt was detected on your account. If this was not you, contact your bank using the official number on your card.
```

Answers:

```text
Clicked link or called number: No
Shared OTP/PIN/password/banking details: No
Transferred money: No
```

Expected risk:

```text
Medium
```

Expected reason:

```text
The message uses bank-alert wording, but it does not provide a suspicious link or number and tells the user to use official contact channels.
```

### High Risk

Pasted message:

```text
RM0.00 MAYBANK ALERT: Suspicious login detected from a new device. Secure your Maybank2u account immediately at:

https://maybank2u-security-verify.com

If this was not you, call our fraud department now at 011-2388 9044.
```

Answers:

```text
Clicked link or called number: Yes
Shared OTP/PIN/password/banking details: No
Transferred money: No
```

Expected risk:

```text
High
```

Expected reason:

```text
The user interacted with a suspicious bank alert containing an unofficial-looking login link and phone number, but has not shared credentials or transferred money.
```

### Critical Risk

Pasted message:

```text
Bank Negara Malaysia alert: Your account is involved in illegal activity. To avoid arrest, transfer your money to a secure account for investigation now.
```

Answers:

```text
Clicked link or called number: Yes
Shared OTP/PIN/password/banking details: No
Transferred money: Yes
```

Expected risk:

```text
Critical
```

Expected reason:

```text
The message uses a safe-account transfer tactic and the user already transferred money.
```

## Fake Job Offers

### Low Risk

Answers:

```text
Unsolicited message: No
Asked to pay upfront fee: No
Unrealistic high income: No
Asked to receive and forward money: No
Overseas job without proper contract: No
Asked to deposit money to unlock tasks: No
```

Expected risk:

```text
Low
```

Expected reason:

```text
No major job scam warning signs are present based on the answers.
```

### Medium Risk

Answers:

```text
Unsolicited message: Yes
Asked to pay upfront fee: No
Unrealistic high income: Yes
Asked to receive and forward money: No
Overseas job without proper contract: No
Asked to deposit money to unlock tasks: No
```

Expected risk:

```text
Medium
```

Expected reason:

```text
The offer is unsolicited and promises unusually high income, but there is no payment request, money forwarding, or task-deposit instruction.
```

### High Risk

Answers:

```text
Unsolicited message: Yes
Asked to pay upfront fee: Yes
Unrealistic high income: Yes
Asked to receive and forward money: No
Overseas job without proper contract: No
Asked to deposit money to unlock tasks: No
```

Expected risk:

```text
High
```

Expected reason:

```text
The job offer is unsolicited, promises unrealistic income, and asks for upfront payment.
```

### Critical Risk

Answers:

```text
Unsolicited message: Yes
Asked to pay upfront fee: No
Unrealistic high income: Yes
Asked to receive and forward money: Yes
Overseas job without proper contract: No
Asked to deposit money to unlock tasks: Yes
```

Expected risk:

```text
Critical
```

Expected reason:

```text
The user is being asked to receive/forward money or deposit money to unlock tasks, which may involve money mule activity or financial loss.
```

## Investment Scams

### Low Risk

Answers:

```text
Guaranteed or unusually high returns: No
Licensed/registered verified: Yes
Pressure to invest quickly or keep secret: No
Recommended by online contact: No
Asked to pay fee/tax/deposit before withdrawal: No
Already transferred money, paid withdrawal fee/tax/deposit, or cannot withdraw funds: No
Unverified or unfamiliar platform: No
```

Expected risk:

```text
Low
```

Expected reason:

```text
No major investment scam warning signs are present, assuming the licence or registration was verified through official regulator channels.
```

### Medium Risk

Answers:

```text
Guaranteed or unusually high returns: Yes
Licensed/registered verified: Yes
Pressure to invest quickly or keep secret: No
Recommended by online contact: No
Asked to pay fee/tax/deposit before withdrawal: No
Already transferred money, paid withdrawal fee/tax/deposit, or cannot withdraw funds: No
Unverified or unfamiliar platform: No
```

Expected risk:

```text
Medium
```

Expected reason:

```text
Guaranteed or unusually high returns are a warning sign, but the entity is claimed to be licensed and no pressure, online-contact, withdrawal-fee, or suspicious platform indicator is present.
```

### High Risk

Answers:

```text
Guaranteed or unusually high returns: Yes
Licensed/registered verified: No
Pressure to invest quickly or keep secret: Yes
Recommended by online contact: No
Asked to pay fee/tax/deposit before withdrawal: No
Already transferred money, paid withdrawal fee/tax/deposit, or cannot withdraw funds: No
Unverified or unfamiliar platform: Yes
```

Expected risk:

```text
High
```

Expected reason:

```text
The offer involves guaranteed returns, unverified licensing, pressure, and an unfamiliar platform.
```

### Critical Risk

Answers:

```text
Guaranteed or unusually high returns: Yes
Licensed/registered verified: No
Pressure to invest quickly or keep secret: Yes
Recommended by online contact: Yes
Asked to pay fee/tax/deposit before withdrawal: Yes
Already transferred money, paid withdrawal fee/tax/deposit, or cannot withdraw funds: Yes
Unverified or unfamiliar platform: Yes
```

Expected risk:

```text
Critical
```

Expected reason:

```text
The case matches fake investment and romance-investment patterns, and the user has already transferred money, paid a withdrawal fee/tax/deposit, or cannot withdraw funds.
```

## Romance Scams

### Low Risk

Answers:

```text
Met online only: No
Relationship became serious quickly: No
Refused video call or identity verification: No
Asked for money/crypto/gift cards/emergency help: No
Encouraged investment platform: No
Already sent money/images/sensitive info: No
```

Expected risk:

```text
Low
```

Expected reason:

```text
No major romance scam warning signs are present based on the answers.
```

### Medium Risk

Answers:

```text
Met online only: Yes
Relationship became serious quickly: Yes
Refused video call or identity verification: No
Asked for money/crypto/gift cards/emergency help: No
Encouraged investment platform: No
Already sent money/images/sensitive info: No
```

Expected risk:

```text
Medium
```

Expected reason:

```text
Online-only contact and fast emotional escalation are warning signs, but there is no money request, investment pressure, refusal to verify, or user exposure.
```

### High Risk

Answers:

```text
Met online only: Yes
Relationship became serious quickly: Yes
Refused video call or identity verification: Yes
Asked for money/crypto/gift cards/emergency help: Yes
Encouraged investment platform: No
Already sent money/images/sensitive info: No
```

Expected risk:

```text
High
```

Expected reason:

```text
The case contains online-only contact, fast emotional escalation, refusal to verify identity, and a money request.
```

### Critical Risk

Answers:

```text
Met online only: Yes
Relationship became serious quickly: Yes
Refused video call or identity verification: Yes
Asked for money/crypto/gift cards/emergency help: Yes
Encouraged investment platform: Yes
Already sent money/images/sensitive info: Yes
```

Expected risk:

```text
Critical
```

Expected reason:

```text
The user already sent money, intimate images, or sensitive information, creating possible financial loss, blackmail, or personal-data exposure.
```
