# TMG — Tuition Management Gateway

## Table of Contents
- [Introduction](#introduction)
  - [What is TMG?](#what-is-tmg)
  - [Flow](#flow)
  - [Capabilities](#capabilities)
  - [Legal requirements](#legal-requirements)
  - [Students](#students)
  - [Scholarship — business overview](#scholarship)
  - [Payment](#payment)
- [Functional Specifications](#functional-specifications)
  - [Payment Plan Management](#payment-plan-management)
  - [Common Set Up](#common-set-up)
  - [Chart Of Account Set Up](#chart-of-account-set-up)
  - [Cost Centre/Product Line Catalog](#cost-centreproduct-line-dp-curriculumprogramme-catalog)
  - [Sponsor Management](#sponsor-management)
  - [Payment Plan -OLD](#payment-plan--old)
  - [Student Payment Plan](#student-payment-plan)
  - [Sponsor & Student Payment Plan - OLD](#sponsor--student-payment-plan---old)
  - [Sponsor Payment Plan - OLD](#sponsor-payment-plan---old)
  - [Scholarship — setup](#scholarship-1)
  - [Mailboxes for TransferMate](#mailboxes-for-transfermate)
  - [Student Payment Plan Overview](#student-payment-plan-overview)
- [Technical Specifications](#technical-specifications)
  - [Overview](#overview)
  - [Student Journey](#student-journey)
  - [Technical Changes](#technical-changes)
- [Decisions and ideas (suggestion)](#decisions-and-ideas-suggestion)
- [Glossary](#glossary)

---

# Introduction
## What is TMG?
1. **Small intro + def**
   A module is a clearly defined functional part of an information system responsible for a specific set of business activities. It groups related processes, rules, and data in a way that makes its purpose easy to understand and manage. By organizing a system into modules, responsibilities are clearly separated, reducing complexity and allowing each module to evolve independently while interacting with others through well-defined interfaces.

   The **Student Transaction Module (TMG - Tuition Management Gateway)** is responsible for managing all financial transactions related to a student. Its purpose is to record, organize, and track every event that affects a student's financial account, such as charges, deposits, payments, adjustments, or corrections. Transactions are handled in a structured and consistent manner so that a student's financial position can be understood at any time. The module maintains a complete transaction history, links financial events to their business context (programme, term, or payment plan), and ensures that amounts remain accurate and traceable over time. By acting as the single reference point for student-level financial activity, the module supports transparency, reconciliation, and control, while enabling reliable integration with invoicing, payment plans, and external financial systems.

2. **Systems working alongside it**
   - **PeopleSoft**: system of record / source of truth for student and payment plan data.
   - **AIP**: orchestration and transformation layer between PeopleSoft and TransferMate.
   - **TransferMate**: external payment gateway, handles payment processing, payout, and status updates. All Payments are processed outside of PeopleSoft using Transfermate moving forward.

## Flow
Diagram link: see [Student Journey](#student-journey) (Technical Specifications, "From INSEAD web site to Transfermate Student financial Account"). Diagrams/screenshots embedded in the original Word document are not reproduced here.

## Capabilities
Summary of the features:
- **Payment Plan Management** tile giving access to all payment setups:
  - Common Set Up: Legal Entity, Legal Entity & Currency, Payment Plan Type, Payment Type, Payment Item.
  - Scholarship setup: Scholarship Type, Scholarship Catalog.
  - Chart of Account Setup: Fund, Activity, Bank.
  - Cost Center/Product Line and payment bank detail.
  - Tuition Payment Plan Management, Student Payment Plan Assignment, Student Payment Plan, Student Payment Plan Overview, Scholarship File Upload.
  - Quick access to Maintain Applications and Term Activate pages.
- Linking deposits, invoices, and invoice items to the sold programme code (e.g. MBA25J, EMCA25Jun) to get a financial Customer Account grouped by Programme Code.
- Display of Global Invoice with deposits.
- Admission Programme and Payment Plan identification based on compound ADMISSION functional fields = {Academic Program, Admit Term, Campus}.
- Automatic mass upload of admitted students to the default Payment Plan associated with their parent Programme Code.

## Legal requirements
Regulations link & reporting standard template:
- The global invoice display must be compliant with the **2027 French financial law**: all deposit payments and their breakdowns must be accurately represented in a transparent and detailed manner, as required by the new regulations. The total of these payments is still displayed on the global invoice, but the detailed structure must align with the legal financial reporting standards set for 2027. This is an invoicing requirement; among DP programmes, only **EMC** is currently taxed in France and therefore in scope for this law.
- **Non-funded scholarships** are treated as discounts/waivers and therefore impact **GST** (applies specifically to SGP) and **VAT** (applies, to a lesser extent, to ADB).
- MFIN tuition fees with GST are configured with a GST SGD tax item.

## Students
### Deffered
1. **What is a deferred student?**
   A deferred student is one who has been admitted to a program but requests to postpone their start date to a future term of the same program. The Admissions team manages the deferral process. A student can request a deferral at any time before the program starts, even if the first instalment has not been paid. The deferral process involves the student paying an initial deposit, requesting a deferral, and then going through a series of steps including payment adjustments, coordination of financial aid, and ensuring that the student is admitted/enrolled in the new term. The student remains enrolled, but their start date is delayed until the next available term.

2. **Policy**
   - Students can request a deferral at any stage before the program starts, provided they have been admitted.
   - Deferral can occur whether a student has made no payment, only the first instalment, or even the second instalment.
   - **Rejection of Deferred Requests**: if a deferral request is rejected, the student may choose to start the current programme or to withdraw and reapply the following year.
   - If a student decides to withdraw and reapply later (Admissions gives a cutoff date to use the funds received), the €12,000 deposit already paid would be carried over to the next application cycle.
   - **Conditions for Deferred**: students are eligible for deferral as long as they remain admitted to the program and the program has not yet commenced.

3. **Process**
   1. **Initial Admission and Deposit Payment** — the student applies and is admitted, then pays the initial deposit to secure their place. *System Action*: a payment plan is created for the current intake, including deposit, instalment amounts, and deadlines.
   2. **Deferral Request** — the student contacts the admissions team to request deferral to a future term.
   3. **Processing the Deferral Request** — the admissions team evaluates and approves/rejects the request. If the first instalment has not been paid earlier, the student must pay it along with the deferral fee at this time. The deferral fee is a flat **€5,000**, paid via TransferMate as part of the new payment plan.
   4. **Fee Assessment and Communication** — the admissions team creates a new application number with Admit Type = DEF for the new intake, generating a new payment plan. The admission team updates the 2nd and 3rd instalments to zero in the original payment plan. The new payment plan includes deferral fees, and any payments already made on the original plan are displayed in the new plan. If the deferral is requested before the 2nd instalment is paid, that instalment is split into two lines in the new payment plan: the €5,000 deferral fee and the remaining balance of the 2nd instalment. If the 2nd instalment has already been paid, no additional deferral fee is charged.
   5. **Transition to Future Term** — the original application is closed with status WADM and a new application is created with Admit Type = DEF for the future term, carrying over amounts already paid (e.g. the initial deposit). *System Action*: carry forward paid amounts and adjust future instalment amounts accordingly. This transfer of previously paid amounts is currently handled manually.
   6. **Review Financial Aid and Scholarships** — previously awarded scholarships, discounts, or loans are reviewed and adjusted manually for the new deferred term; the student may need to reconfirm eligibility.
   7. **Payment Reminders and Flexibility** — Transfermate sends reminders for upcoming payments (1st instalment, deferral fees) to those who haven't paid.
   8. **Final Confirmation and Enrolment** — once the 1st instalment and deferral fees are paid, the student is officially enrolled (DATA/ENR) for the new intake (done by admissions in checklist).
   9. **Handling Data and Access Restrictions** — ensure the student gets access to MYINSEAD documents once the deferral fee is paid and updated in the checklist.
   10. **Invoicing rules**:
       - A deferred student will not receive an invoice if they have not started the program.
       - If a student has started the program but defers to a subsequent term, the original invoice is cancelled, a credit note is generated for the new term, and a re-issued invoice follows (e.g. €12,000 paid on Term 25D transfers fully to the invoice for Term 26J).
       - A payment plan includes all instalments in a single currency; previous payments in a different currency are converted to the new billing currency at the exchange rate applicable on the date of receipt/payment. *(If the billing currency itself changes between terms, the case is normally treated as a [Transferred Student](#transferred-students) rather than Deferred.)*
       - In the new payment plan, the deferred student only sees the outstanding balance due on the Transfermate dashboard — previous payments are not visible.
       - The outstanding balance is displayed in the billing currency determined by region/program: GEMBA Asia (SGD), EMC Asia (SGD), EMFIN (SGD); MBA FBL or SGP (EUR); TIEMBA (USD); GEMBA Europe (EUR); GEMBA Flex (EUR); GEMBA Middle East (USD); MIM (EUR).

4. **Use Case**
   Invoice: `25FBMBA24D-00678590` — Student 1124232 was admitted and matriculated in 2430 (FBL campus). He was deferred to 2510: Student Enrolment Program Action = DATA (tracking a data change), and admit term updated to 2510 using the same application number. He is matriculated in 2430 (FBL) in Student Program Plan, then goes on leave and returns in 2510 (SGP campus). The invoice must be generated for 2510 SGP, and is synced accordingly.

### Visiting & Exchange
1. **What's the difference?**
   Exchange (**EPW**) and Visiting (**VIS**) students must be excluded from any payment plan assignments. This ensures that the Admissions and Financial Aid teams don't accidentally assign tuition payments to them.
2. **Example + Visual explanation**
   Confirmed by Katya via email (no payment plan assignment for EPW/VIS students).

### Transferred Students
1. **Definition & specification**
   - Transferred students are those who stay on the same Admit Term (e.g. 1700) but change sections (e.g. from Europe to Asia) — note tuition fees & currency changes apply.
   - Students who transfer from one programme to another (e.g. GEMBA to MBA, etc.) are also considered transferred.
   - If an MBA student changes campus for the same intake, they are **not** considered transferred.
2. **Visual example**
   1 global invoice ⇒ Credit Note ⇒ New global invoice.

### LOA
1. **What is LOA?**
   Leave of Absence — a period where a student pauses their program.
2. **Scenario 1: LOA for more than 50% of the programme**
   - If a student paid the full amount 1–2 years ago (e.g. GEMBA program in FBL) and takes an LOA for more than 50% of the programme, the revenue for the class is reversed and deferred (moved to a clearing account / treated as an advance payment) without recognizing it for that class.
   - Upon the student's return 2 years later, the Financial Aid team decides on re-invoicing based on the updated program price.
   - If re-invoicing is required, the old invoice can be cancelled, and a new invoice is issued at the revised price.
3. **Scenario 2: LOA for less than 50% of the programme**
   - If a student paid the full amount 1–2 years ago and takes an LOA for less than 50% of the programme, the revenue for the class is recognized as earned.
4. **Edge cases** (per discussion between Katya and Yves) — each case is reviewed individually based on the student's situation, in coordination with the Finance team on fees and invoicing:
   - Students who stop the program early (before completing Period 1) and restart from scratch with a new intake usually have a new application created.
   - Students who complete most of the program with their initial intake but take a few courses with another intake keep their original admit term.
   - Students who split their studies roughly 50/50 between two intakes have it discussed which intake they are considered part of (e.g. for future alumni reunions).
   - Students who complete a few courses before moving to the next intake typically keep the same application number but have their last admit term updated to the new intake.

## Scholarship
### Introduction
1. **Scholarships & Invoice**
   Scholarships are entered into PeopleSoft by the Financial Aid team. There are two types:
   - **INSEAD-funded scholarships (non-funded)**: treated as discounts, so GST is deducted. The GST discount is calculated by the Accounts and Financial Aid teams and posted in PeopleSoft. GST applies only at invoicing and only for students starting in Singapore.
   - **Donor-funded scholarships (funded)**: no GST associated.

   *Wishlist for future*: business requested an item type that can automatically calculate GST, which would support the Scholarship Revamp process (today the Accounts team manually updates an Excel sheet with the calculated scholarship amounts, and Financial Aid manually enters them into PeopleSoft).

   Scholarships are either payments or waivers on an invoice; Scholarship Category (Funded/Non-funded) relates to an accounting (Fusion) memoline. The Scholarship Code is unique and is the key for the scholarship values.

### Sponsored
1. **Full Company Sponsored**
2. **Partial Company Sponsored**

   Two types of sponsors exist — Full and Partial. Key open points/context:
   - Whether sponsors are known before mass assignment of Tuition Payment Plans to students.
   - Use Case: BCG France (one Org ID) has several addresses in France and multiple regional contacts; BCG UK is a separate Org ID.
   - Open question (Q01): how do companies connect to Transfermate / how are they registered in Transfermate — no solution identified yet. SSO requires an INSEAD account, and students are in scope.
   - Roughly 20% of MBA and GEMBAS students are sponsored; no MIMs; 20% of MFIN.
   - Multiple representatives can exist, each with a unique ID equal to their contact email.
   - Sponsor data is currently entered in PeopleSoft by Financial Aid (an Excel file managing sponsor contact information is to be replaced).
   - The first instalment is managed by Admission, due one month after the admission date across the 4 admission rounds.
   - Functionality needed: enter sponsor information; search by Campus/Programme/Expected Graduation Term.

### Funded
1. **Funded (donations)**
   Financed externally, mainly through donations — like a Payment invoice item (natural account 46...), it increases liabilities/resources. Funded scholarships are categorized using Fund Codes derived from Segment 6 of the Horizon Account Key, classified into four types:
   - **XEM…** Endowed scholarships from the INSEAD Foundation (investment-based and recurring).
   - **XCM…** from the INSEAD Foundation.
   - **XED** — associated with loan support mechanisms.
   - **XCD** — general category for funded scholarships (source not specifically defined).
2. **Non-funded (Insead Funded)**
   Financed by INSEAD itself (using its own funds); essentially considered discounts/waivers, impacting GST (SGP) and VAT (ADB) — like a waiver invoice item (natural account 706, reducing revenue). INSEAD-funded scholarships are financed using a 6% share of programme revenue redistributed to students; these scholarships have no fund code.

## Payment
### Instalment Amount Update (to change)
1. **Payment Item Type and Payment Name based on Scholarship Code**
   The Scholarship File Upload includes the Scholarship Code along with the application number and student ID. With this key information, the Payment Item Type and Payment Item Name of the scholarship is determined and added in the Student Payment Plan:
   - From the Scholarship Catalog setup (Scholarship Code) and the Scholarship Type setup, the Invoice Item Type Code (payments or waivers) and Payment Item Type are identified.
   - From the Payment Item common setup for the student's application programme, the Payment Item Type and Invoice Item Type Code are looked up to get the correct Payment Item Type, Payment Item Name, and Invoice Item Type.

   **Auto-calculation rules** for Scholarship & Instalment Amount Update:
   - The scholarship upload is validated against the Student Plan only if all Payment Plan instalments have been assigned to the student; erroneous lines are rejected.
   - If successfully applied, the uploaded scholarship amount is automatically deducted from the last instalment amount, and if needed, from the next-to-last instalment.
   - When a scholarship is entered manually, the same automatic calculation applies.

   *Out of Scope: Phase 1* — In Phase 2, GEMBA SGP and ABU will be invoiced and their scholarships created.

### Payment Plan Currency and Actual Payment Currency
The programme revenue is based in the currency tied to the INSEAD bank account collecting the revenue in Transfermate; the payer can select any currency available in Transfermate.

Use Case: a loan of €60k is entered into the accounting system while the second deposit of €50k for the payment plan is entered in PeopleSoft but not yet paid. Transfermate continues to request the €50k until the payment plan is updated to reflect a second deposit of €0 — this change cannot be reflected in the PeopleSoft student financial invoice once it has already been generated and accounted for.

Open idea: move the Instalment Deadline to the Student assignment process. Food items are part of the tuition fees.

### Loan
**Types of Loans Available for Students**
- **Local or Personal Loans**: secured directly from local banks/financial institutions; disbursed to the student's personal account, who then pays INSEAD. Primarily for student convenience — INSEAD keeps a record for reference (loan providers, interest rates, etc.).
- **Global Loans**: e.g. Prodigy, Brain Capital, Lendwise, U.S. Federal Loans, U.S. Private Loans – Sallie Mae, Juno, Lendorse, Spark Finance — disbursed directly to INSEAD, can cover both tuition fees and living expenses. Students must pay the first instalment from personal funds to confirm commitment, since global loan funds are typically released only after the program starts.

**Process for Global Loans**
- INSEAD assists with loan certification depending on provider/country requirements.
- Once the program begins, global loan providers release the funds; funds first settle tuition fees, remaining balance goes to living expenses.

**Loan Currency**
- Entered currency: USD (Prodigy, US Federal loans, Juno, Sallie Mae), EUR (Brain Capital), GBP (Lendwise), AUD (Sparkle Finance).
- Accounted currency: Student Payment Plan currency — auto conversion is a nice-to-have based on the PeopleSoft exchange rate table.

### CPF
**Key Pain Points Identified in the Journey**
- Manual interventions: admissions officers currently handle many manual updates, increasing workload and risk of errors.
- Visibility of paid amounts: difficulty tracking carried-over payments from previous terms in the system.
- Payment flexibility: challenges managing partial payments/adjustments for deferred students.

**Use Cases discussed**
1. Self-Sponsor – With a Personal Bank Loan (or no bank loan). Student pays instalments via TransferMate as sent from PeopleSoft: 1st instalment (INST01/Deposit) €12K, 2nd (INST02/Payment) €62K, 3rd (INST03/Payment) €40K, all via TransferMate. *Note*: if two deposits are required by INSEAD, the system is updated manually; students may also pay all three instalments once a personal bank loan is secured.
2. Self-Sponsor – Student with One Scholarship / with Two Scholarships.
3. Self-Sponsor – With Loan and Scholarship.
4. Self-Sponsor – With a Global Loan (e.g. PRODIGY): 1st instalment (INST01/Deposit) €12K paid via TransferMate; 2nd instalment (INST02/Payment) €62K remains unpaid via TransferMate (€0 after the loan is applied). Manual adjustments: a loan payment (Prodigy or similar) is added to the plan (€100K), and the financial user manually sets the 2nd instalment to €0. Invoice handling: the €12K deposit is transferred to the existing invoice process.

### Refund
Current refunds are processed in two ways:
1. **Deposit Refunds**: the Accounts Payable (AP) team is asked to process the bank transfer; the transaction is recorded in Fusion with a debit to Prepayment and a credit to the Bank account.
2. **Overpayment and Loan Refunds**: the receipt shows the overpayment amount; a Debit Note (DN) is generated to debit Accounts Receivable (AR) and credit the Bank account, then applied to the relevant receipt.

Transfermate rule: if it is a Card/APM payment or bank transfer where INSEAD has the original payer's account details, the refund is processed as soon as funds are received from INSEAD. If not, the timing depends on when those details are obtained from the student/payer.


# Functional Specifications

## Payment Plan Management
This tile gives access to all payment setups: Common Set Up (used to ease data entry) covering Legal Entity, Legal Entity & Currency, Payment Plan Type, Payment Type, and Payment Item; Scholarship setup covering Scholarship Type and Scholarship Catalog; Chart of Account Setup covering Fund, Activity and Bank; and Cost Center/Product Line and payment bank detail.

The tile also includes access to Tuition Payment Plan Management, Student Payment Plan Assignment, Student Payment Plan, Student Payment Plan Overview, and Scholarship File Upload, plus quick access to the Maintain Applications and Term Activate page.

## Common Set Up
In all setup tables: Created By/Created and Last Modified By/Last Modified fields are implemented. Row deletion is disabled for records already used in another common setup, in Tuition Payment Plan Management, or in a Student Payment Plan transaction.

### Legal Entity
"Legal Entity"

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Fusion Legal Entity Code * | Shared Legal Entity code shared between Fusion & Peoplesoft | Free Text (10 char) | This ID cannot be changed and is visible only. |
| 2 | Legal Entity Name | Description to Legal Entity Name | Free Text (Char 100) | In the future to get it from Fusion |
| 3 | Campus * | Functional Code | LOV | Cannot be NULL, part of the functional key when combined with the currency code. FBL, ABU, SGP, INA |
| 4 | Active * | To hide the display of inactive items in the Payment Plan Module | Y/N | If Inactive, not displayed in any LOV of the payment plan transactions. |

### Legal Entity & Currency
"Legal Entity & Currency"

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Legal Entity Currency ID (Labelled ID) * | Unique Technical ID – System generated | Integer | Cannot be changed, visible only. Once used in child tables, deletion is not possible. Column "LegEntCurrID", not displayed to user. |
| 2 | Campus | | | Display Only |
| 3 | Currency Code * | Currency Code | LOV | |
| 4 | Fusion Accounting Key SEG1 * | Segment 1 of the accounting key | Integer | Can be Null. Used to account amount in GL |
| 5 | Active * | To hide the display of inactive items in the Payment Plan Module | Y/N | If Inactive, not displayed in any LOV of the payment plan transactions. |

*Nota Bene* — Principal LOV selection for Payment Plan creation: during creation, users select Legal Entity and Currency simultaneously via an LOV (e.g. SGP-SGD, SGP-EUR Campus-Currency), populated from the "Legal Entity Currency" table. In the payment plan table, either the technical or functional ID (or both) is saved; they cannot be null.

### Payment Plan Type
"Payment Plan Type"

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Payment Plan Type Code * | Unique functional code | 50 char | Cannot be changed. Primary Key. Once used in child tables, deletion is not possible. |
| 2 | Payment Plan Type Name | Description | 100 Char Free Text | |
| 3 | Individual Sponsor | | Y/N | Indicator for sponsored payment plan; when selected, the Student Payment Plan defaults the Payer to the sponsoring Organization. |
| 4 | Active_YN (Labelled Active) * | To hide the display of inactive items in the Payment Plan Module | Boolean | If Inactive, not displayed in any LOV |

### Payment Type
"Payment Type"

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Payment Type ID * | Unique technical ID | Integer | Cannot be changed – not shown. Once used in child tables, deletion is not possible. |
| 2 | Payment Type Code * | Unique functional code | Free text (20) | Unique, not null. INST-INSTPREPAYT, INSTPAYT, GLOANPAYT, PLOANPAYT, SCHOLARPAYT |
| 3 | Payment Type Name | Description | 50 Char Free Text | Instalment pre-payment, Instalment payment, Loan payment, Scholarship payment |
| 4 | Instalment Item | Scope payment items in Payment Plan creation | Y/N | Only Y items are displayed when managing a payment plan. |
| 5 | Invoice Item | Scope items for Posting to Customer Account | Y/N | Only Y items have an Item Type and are allowed for posting to the customer's account |
| 6 | Scholarship/Discount | Scholarships or Discount items | Y/N | To identify scholarships and discounts |
| 7 | Transfermate Item | Scope items for Transfermate sync | Y/N | Only Y items are allowed for Transfermate sync |
| 8 | Deductable Fund | Items deductable from Programme Price | Y/N | |
| 9 | Deduct When Paid Only | Items deductable from Programme Price only when Paid | Y/N | |

### Payment Item
"Payment Item"

Select the PeopleSoft Programme and oversee the Payment list, using the "Product Line" Set Up transaction to display the DP programmes LOV, or add a new PeopleSoft Programme Payment List.

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Payment Item Id * | Unique Technical ID | Integer | Cannot be changed – not shown. Once used in child tables, deletion is not possible. Read Only. |
| 2 | Payment Item Type | Categorize payments | LOV | Distinguishes instalments from other payments; identifies deposits (DEPOSIT) from other INSTPAYT. Enables a default type per payment plan item. Updatable |
| 3 | Payment Item Name | Instead.edu & Transfermate shared display names | Free Text | e.g. First Instalment, Global Loan, INSEAD Scholarship, External Scholarship |
| 4 | Invoice Item Type (display: Descr, Type Code & Currency) | Identifies the memoline (product sold) and currency | LOV | Link to the Invoice Item and its currency. Updatable |
| 5 | Order By | Orders the list by default | Integer | 1, 2, 3 — instalments displayed first, then Scholarships, then Loans. Updatable |
| 6 | Display in List * | To hide the display of items in the Payment Plan Module | Y/N | If N, not displayed in any LOV. Updatable |
| 7 | Display in Assigned by Default | To show items as default when creating Tuition Payment Plan | Y/N | If Y, defaulted in the Tuition Payment Plan creation |

*Nota Bene* — Behaviour during Payment Plan creation:
- **Defaulted Payment Item Type**: determines categorization during creation (e.g. first instalment as deposit, third as payment, or moving forward to Student Payment Plan Loan/scholarship payments); acts as a default type for each payment plan item.
- **Invoice Item Type**: required for payment items that must be included in the invoice. Within Common Set Up, only Deposit (DEPOSIT) and Scholarship (SCHOLARPAYT) payment items can be linked to an invoice item.

## Chart Of Account Set Up
"Chart Of Account Set Up" — a subset of the Oracle Fusion Chart of Accounts required to manage DP programme customer invoices. *(Idea: move Legal Entity and Legal Entity & Currency under Chart of Account below.)*

### Fund
"Fund"

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Fund Code * | Unique ID | Free Text | Fund Code ID |
| 2 | Description | | Free Text | Fund Code Description |
| 3 | Start Date | | Date | |
| 4 | End Date | | Date | |
| 5 | Scholarship Flag | | Y/N | Fund Code for scholarship item |
| 6 | Enabled * | | Y/N | If N, not displayed in any LOV. |

Source: Oracle Fusion ERP Finance Module, BIP report `FIN_INSEAD_FUND_CODE_RPT`.

### Activity
"Activity"

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Activity Code * | Unique ID | Free Text | Activity Code ID |
| 2 | Description | | Free Text | Activity Description |
| 3 | Start Date | | Date | |
| 4 | End Date | | Date | |
| 5 | Bank | | Y/N | Activity code applicable for Bank |
| 6 | Enabled * | | Y/N | If N, not displayed in any LOV. |

### Bank
"Bank" — in Phase 2, the Oracle Fusion webservice can automatically feed the PeopleSoft reference table below.

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Activity Code | | LOV | Fusion WS to feed this record in phase 2 — LOV from Activity |
| 3 | Bank Account Name | | Free Text | |
| 4 | Currency Code | | LOV | |
| 5 | Bank Name | | Free Text | |
| 6 | Bank Account End Date | If empty or in the future, the account is active | Date | |

*(Old design)* Source: Oracle Fusion ERP Finance Module, BIP report `FIN_INSEAD_BANK_ACCOUNTS_RPT` (`FIN_INSEAD_BANK_ACCOUNTS_RPT_FIN_INSEAD_BANK_ACCOUNTS_RPT.xlsx`). Dependency: Cost Centre/Product Line to be renamed DP Programme Catalog; Activity Code to be renamed Bank Account Name (LOV from the reference table, displaying currency and bank name).

### Programme Code
"Programme Code" — to be implemented in Phase 2.

### Cost Centre
"Cost Centre" — to be implemented in Phase 2.

### Legal Entity
"Legal Entity" — done, moved from Common Set Up.

### Activity Code
"Activity" — planned for Phase 2.

## Cost Centre/Product Line=> DP Curriculum/Programme? Catalog
"Cost Centre/Product Line" — already implemented and available in the Invoice WorkCentre. A left-menu URL is required in the "Payment Plan Set Up".

## Sponsor Management
"Sponsor Management" — this tile is dedicated to the creation of a sponsor for a student application.

### Add Person
PeopleSoft seeded transaction to search and add a person; used to create or modify a future company contact (must first be created as a PeopleSoft person). Menu navigation enhancement only.

### Add Organization
PeopleSoft seeded transaction to search and add an organization; used to create or modify a future sponsor company (must first be created as a PeopleSoft organization). Menu navigation enhancement only.

### Add Organization Contact
PeopleSoft seeded transaction to search and add a person as a contact. Menu navigation enhancement only.

### Add Sponsor to Student
A sponsor should be added to a student and linked to one and only one Degree Programme application number. A customized DP Application Number is required for each general material of group INVOICE and Type REC of a degree programme student. Up to 2 sponsors can be added (currently only one). The student seeded general material transaction (Material Group = 'INVOICE', Material Type = 'REC', to track the student sponsor) should maintain the "Comment" value = existing application number concatenated with the new application number; the seeded transaction must be updated similarly.

## Payment Plan -OLD
PeopleSoft sends only the instalments assigned to the Student or Sponsor Payment Plan to TransferMate. "Payment Plan": creation/update/inactivation of a payment plan — once used by a student, it cannot be deleted.

### Payment Plan Price
The Payment Plan Price equals the total of active & inactive instalment amounts. Transfermate can display the inactive instalments.

### "Payment Plan" Tab
"Tuition Payment Plan" — creation/update/inactivation of a payment plan; once used by a student, it cannot be deleted.

**Header**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Payment Plan Id * | Unique Technical ID | Integer | Cannot be changed. Once used in child tables, deletion is not possible. Read Only. |
| 2 | Payment Plan Code * | Meaningful Functional code | Free Text | Should start with the PeopleSoft Programme+CAMPUS; user can complete or change |
| 3 | Description | More meaning | Free Text | By default PeopleSoft Programme + CAMPUS |
| 4 | Admit Term * | | LOV | |
| 5 | Payment Plan Type | | | |
| 6 | Product Line * (display Description) | Identify the product line to be sold | LOV | |
| 7 | Legal Entity Campus | | LOV | From "Common Set Up" |
| 8 | Admit Campus | | LOV | |
| 9 | Currency | | LOV | |
| 10 | Expiration Date | | Date | |
| 11 | Active * | To hide the display of inactive items in the Payment Plan Module | Y/N | If Inactive, not displayed in any LOV; add as a criterion in Payment Plan Search. Updatable |
| 12 | Transfermate Instalment Qty | Calculated Amount | Integer | = count(all instalments regardless of status). Criterion allowing scholarship upload to a Student Payment Plan. |
| 13 | Programme Price Tax Included | Display Only | Number | From Programme Price tab |
| 14 | Instalment Total Amount | Calculated Amount | Number | = sum(all instalment amounts regardless of status) — Vishal to confirm with Transfermate |

**Instalment Grid**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Instalment ID (Default) * | Unique Technical ID | Integer | Cannot be changed. Not displayed to user. Auto-incremented per instalment row. |
| 2 | Payment Item Type (Default) * | Categorize payments | LOV | Distinguishes instalments from other payments; identifies deposits (DEPOSIT) from other INSTPAYT. Updatable |
| 3 | Payment Item Name * | Instead.edu & Transfermate shared display names | Free Text | e.g. First Instalment, Global Loan, INSEAD Scholarship, External Scholarship. Updatable |
| 4 | Payment Item Amount (display currency read only) | Amount synced to Transfermate for online payment | Number | Instalment amount to be paid by the student |
| 5 | Due Date | Date synced to Transfermate for student notification flow | Date | Latest possible payment date |
| 6 | Payment Plan Seq | | Integer | Unique instalment identifier |
| 7 | Active * | To hide the display of inactive items in the Payment Plan Module | Y/N | Only active instalments are assigned to student or sponsor |

Payment Item Type can be changed when creating a Payment Plan.

### Implementation Proposition 1
Add a Tab: Programme Price. The display of the currency is derived from the selected Legal Entity Currency value.

#### "Programme Price" Tab

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Programme Price Taxe Included (Display Currency) | Record the full programme price | Number | Updatable. Synced to Transfermate |

#### Tuition Payment Plan Management - Instalments Auto population
As soon as the Product Name, Legal Entity Campus, Admit Campus and Currency are specified, the Instalment table is auto-filled with instalments based on the Payment Item (Common Set Up) and Financial Product Lines (Cost Centre/Product Line) setup. The Financial Product Line Programme is linked to the same Programme from the Payment Item setup; all instalments with no currency, plus all instalments of the same currency under that programme flagged "Display in Assigned by Default", are auto-populated in the Instalments table.

### Student Payment Plan Assignment
#### Search Filter
At least 1 field must be filled in to allow a search. Payment Plan by default shows all Payment Plans set up in Tuition Payment Plan Management that are Active and have not passed their expiration date. When a Product Name and/or Admit Term is entered, the Payment Plan LOV is limited according to the selection.

#### Student Payment Plan Search
To ensure integrity of student assignment, a Payment Plan must be specified on search — this restricts the search to students within that Tuition Payment Plan setup only. e.g. searching for MBA_2630_FBL_SELF selects all student applications for the given Product Name Programme (based on Financial Product Lines setup), Admit Term, Admit Campus and Currency. For Full Sponsored payment plans, only student applications with sponsored organization and contact are selected.

#### Student Payment Plan Assignment (rules)
Assignment may be made per instalment (by specifying the instalment sequence), which assigns only that specific instalment; or for all instalments (by leaving the Payment Plan Instalment blank).

**Assignment Rule**:
1. When the Student Payment Plan is not yet created, the system creates one for that application number, then adds the selected instalment sequence (if given) or all instalments set up in the Tuition Payment Plan Management.
2. When the Student Payment Plan already exists, the system checks the instalment sequence: if it already exists, it is not overridden; otherwise it is appended.
3. If the existing Student Payment Plan for the application number has a different Payment Plan than the one selected for Assignment, the system overrides the Payment Plan, Payment Plan Type, and Programme Price if the new payment plan is valid (e.g. a student initially assigned to a Self-Funded plan later identified as Sponsored, or vice versa).
4. When a Payment Plan Instalment sequence has a different Due Date, the new Due Date is used rather than the one set up in Tuition Payment Plan Management.

## Student Payment Plan

**Header**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Transaction Identifier * | Unique Technical ID | Integer | Cannot be changed. Once used in child tables, deletion is not possible. Read Only. |
| 2 | Transaction Date and Time | Display Only | Datetime | Creation Date and Time |
| 3 | Last Modified Date | Display Only | Datetime | Last Modified Date and Time |
| 4 | Last Modified By | Display Only | Text | Operator ID who created or made the last change |
| 5 | Empl ID * | | LOV | |
| 6 | Application Number * | | LOV | Active or Admitted Student applications |
| 7 | Payment Plan | | LOV | Valid Payment Plans applicable for the Student application |
| 8 | Payment Plan Type | | LOV | Values from the Payment Plan Type setup in Common Set Up |
| 9 | Funding Info | | Free Text | |
| 10 | At Risk | | Y/N | |
| 11 | Taking Global Loans | | Y/N | |
| 12 | Non Standard Payment Plan | | Y/N | |
| 13 | External Scholarship Loan | | Y/N | |
| 14 | Transfermate Instalment Qty | Display Only | | Read-only |
| 15 | Currency | Display Only | | Read-only |
| 16 | Programme Price | Display Only | | Read-only — from Tuition Payment Plan Programme Price |
| 17 | Total Student Funding | Display Only | | Calculated & saved: sum of ALL components/lines recorded in the student's financial plan (instalments, scholarships, loans, discounts, deferrals) |
| 18 | Refunds Amount | Display Only | | Calculated & saved |
| 19 | Student Funding Balance | Display Only | | Calculated only — difference (Programme Price − Student Plan Total); if not 0, shown in red |
| 20 | Payment Expected | Display Only | | Calculated & saved — all payments expected excluding scholarships/discounts; recalculated from Transfermate via API |
| 21 | Payments Received | Display Only | | Calculated & saved — all payments received excluding scholarships/discounts (PAID = Paid + Funds Received); recalculated via API |
| 22 | Payment Balance | Display Only | | Calculated only |
| 23 | Transfermate Expected Amount | Display Only | | Calculated & saved — payments sent to TM; recalculated via API |
| 24 | Transfermate Received Amount | Display Only | | Calculated & saved — all payments paid via TM (PAID = Paid + Funds Received); recalculated via API |
| 25 | Transfermate Balance | | | Calculated only |

**Funding Items**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Instalment Seq* | Unique Technical ID | Integer | Cannot be changed. Not displayed to user. Auto-incremented per instalment row. |
| 2 | Payment Plan Id | | Integer | Unique ID from Tuition Payment Plan Management setup. Not displayed to user. Saved only when instalment is added via Payment Plan Assignment. |
| 3 | Instalment ID | | Integer | Unique ID from Tuition Payment Plan Management setup. Not displayed to user. Used to check whether the same instalment Seq ID already exists (not overwritten when adding via Payment Plan Assignment). |
| 4 | Payment Item Type | | LOV | Values from Payment Type setup in Common Set Up |
| 5 | Payment Item Name | | LOV | Values from Payment Item setup in Common Set Up for the selected Payment Item Type/Programme/currency (or without currency) |
| 6 | Invoice Item Type | Display Only | | Referenced from the Payment Item Type/Name combination in Common Set Up |
| 7 | Payment Item Amount | | | |
| 8 | Due Date | | Date | Required for Instalment Item |
| 9 | Posted | | Checkbox | Checked for instalments already posted |
| — | Item Number | | | Not displayed to user — technical Item Number posted in Customer Account (ITEM_LINE_SF) |
| — | Line Seq Number | | | Not displayed to user — technical Line Seq Number posted in Customer Account (ITEM_LINE_SF) |
| 10 | Select for Posting | | Checkbox | Enabled only for Scholarships, Discounts, and Instalments that are Invoice Items (per Payment Type setup) already Paid (TM Paid or External Paid) |
| 11 | Sync Status | | LOV | (D) Deferred No Sync — only for instalments with a Previous TM Reference (not in Payment Plan API); (N) Transfermate No Sync — non-Transfermate item or TM instalments paid externally (not in API); (Y) Transfermate Sync — available in the Payment Plan API |
| 12 | Comments | | Free Text | |

**Payment Status**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 13 | Transaction ID | | | Read-Only — `paymentTransactionId` synced from Transfermate |
| 14 | Bank Account | | | Read-Only — `bankAccountName` synced from Transfermate |
| 15 | Transfermate Payment Amount | | | Read-Only — `instalmentAmount` synced from Transfermate |
| 16 | Transfermate Payment Date | | | Read-Only — `paymentDate` synced from Transfermate |
| 17 | Transfermate Payment Status | | | Read-Only — 0 Pending, 1 Processing, 2 Funds Received, 3 Paid (`instalmentStatusId` synced from Transfermate) |
| 18 | Status Date | | | Read-Only — last sync date from Transfermate |
| 19 | Payment Payout ID | | | Read-Only — `payoutId` synced from Transfermate |
| 20 | Payment Method | | | Read-Only — `payerPaymentMethod` synced from Transfermate |
| 21 | External Payment Amount | | Free Text (numeric only) | |
| 22 | External Payment Date | | Date | |
| 23 | External Payment Status | | LOV | 0 Pending, 1 Processing, 2 Funds Received, 3 Paid. External Payment Date/Status required when status = Paid; Sync Status is set to Transfermate No-Sync when External Payment Status = Paid, removing the instalment from the Payment Plan API |

**Payer**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 24 | ID Type | | | |
| 25 | Payer ID | | | For ID Type = Organization: lists only Organizations added via Add Sponsor to Student. For ID Type = Person: lists and defaults to the Student ID itself. |
| 26 | Company Name | Display Only | | Organization Long Description |
| 27 | Contract Number | | | Enabled if payer is an Organization; required for posting to the Customer Account |

**Scholarship Details**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 28 | Scholarship ID | | LOV | Only valid Scholarships displayed based on defined logic |
| 29 | Scholarship Name | Display Only | | Reference from Scholarship Catalog setup |
| 30 | Fund Code | Display Only | | Reference from Scholarship Catalog setup |
| 31 | Scholarship Type | Display Only | | Reference from Scholarship Catalog setup |

**Previous TM Payment Details**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 32 | Previous Reference | | LOV | List of Paid instalments from another Payment Plan of the same student |
| 33 | Original Payment Amount | Display Only | | Read-only — Payment Item Amount of the selected instalment reference |
| 34 | Base Currency | Display Only | | Read-only — currency of the selected instalment reference |
| 35 | Conversion Rate | Display Only | | Read-only — exchange rate of the instalment reference as of payment date |

**Refund**

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 36 | Refund Amount | | Free Text (numeric only) | |
| 37 | Refund Date | | Date | |
| 38 | Refund PayoutID | | Free Text | |

### Sponsor and Student Payment Plan Linkage
The application number is the bridge between the student and the sponsor. The Student Payment Plan is saved with the application number; the Sponsor Payment Plan is saved in the same table with application number and student information. The Instalment sequence number is used to add rows in this common table.

### Transfermate Payment
As soon as payments are processed from Transfermate, instalments in the Student Payment Plan become un-editable apart from a few fields like Comments and Contract Number for an Organization payer — this ensures TransferMate payment collection isn't impacted by instalment updates in PeopleSoft. The Sync Status remains "Transfermate Sync" and stays available in the API until the Transfermate Payment Status becomes Paid; once Paid, the instalment is no longer visible in the API. Users with the `F1_PAYMENTPLAN_EDIT` role can still edit the Payment Item Type and Payment Item Name fields even if the instalment is already Paid.

### External Payment
As soon as the External Payment Status is set to Paid, fields become un-editable except Comments, Contract Number, and External Payment Status. The Sync Status is updated to "Transfermate No Sync" and marked as "delete" in the API so the instalment is removed in Transfermate to avoid double payment.

### Previous TM Payment
If a student withdraws or cancels while a deposit has already been paid, only paid instalments are retained; unpaid instalments are deleted. When the student is re-admitted (same or another programme), the paid instalment may be transferred to the new Payment Plan by adding the Previous Payment Plan reference. If the previous currency differs from the new payment plan, a conversion applies based on the rate as of the instalment's payment date. The Payment Item Type, Payment Item Name and Invoice Item Type are updated based on the Payment Item setup for "Transferred Payment" for the given currency; the Sync Status is updated to "Deferred No Sync" and marked as "delete" in the API.

### Student Payment Plan sync to Transfermate (Payment Plan API)
The Payment Plan API returns Student Payment Plan information for Transfermate to consume, for instalments where the Payment Item Type is set up as Transfermate Item.
- Sync Status defaulted to "Transfermate Sync" ⇒ instalment status in the API is "active" (added in Transfermate).
- Sync Status updated to "Transfermate No Sync" ⇒ instalment still in the API but status "delete" (removed in Transfermate).
- When a Transfermate Item instalment is explicitly deleted ⇒ still available in the API with status "delete".
- When the whole Student Payment Plan is explicitly deleted ⇒ the payment plan remains in the API with its Transfermate Item instalment status "delete".

### Posting Customer Account
Only items flagged as Invoice Item are available for posting. From the Student Payment Plan, posting is done by checking "Select for Posting" (enabled only for Paid Deposits and other Invoice Items) and clicking Post to Customer Account. Once posted, the Posted checkbox is ticked and the item becomes available in the View Customer Account.

Bulk posting can be done using the Posting to Customer Account page — a background Application Engine (`CY2_PP_STPST`) runs to minimize page waiting time (may take a while depending on volume). Search result exceptions on this page:
- Exclude non-funded scholarship and discounts/waivers, except for FBL and EMC program.
- Exclude Org-sponsored items that have no contract number.

## Sponsor & Student Payment Plan - OLD
### Common: Transfermate Instalment Status
- 0 – Registered (Payment Logged)
- 1 – Pending (Funds Received)
- 2 – Paid (Funds paid out)
- 3 – Inactive (Payment Cancelled)

### Common: Payment Plan Closure
Closure of the Student Payment Plan (Pond) is determined based on the PeopleSoft Student Payment Plan Instalment Total Amount, where: PeopleSoft Student Payment Plan Total Instalment Quantity ≥ PeopleSoft Payment Plan Total Instalment Quantity.

**User Responsibilities**: users must update the assigned instalment amount to 0 when a student decides not to pursue the INSEAD program. When Instalment Amounts are updated to 0 and status set to "Unpaid", instalment payment collection is closed.

### Common: Payment Plan Instalment Split
Rule for Transfermate Payment Plan Instalment status "1 – Pending (Funds Received)": disables the PeopleSoft instalment split. The Transfermate Payment Plan Instalment status is required in the PeopleSoft Student & Sponsor Payment Plan.

## Sponsor Payment Plan - OLD
"Sponsor Payment Plan" — menu: in the "Payment Plan" left menu, "Sponsor Payment Plan" link points to 2 tabs: "Sponsor Payment Plan" and "Convention de Stage".

### Sponsor Unique Identifier
The only unique identifier of a sponsor representative paying the applicant invoice is the application number, updated in the PeopleSoft application General Material transaction. Open question: how to share this ID with the sponsor so they can connect to Transfermate.

**Issue with Application Number as an Identifier**
- A single representative can be assigned to multiple students in PeopleSoft.
- When starting sponsor registration, they enter the application number along with their details (work email, name, etc.).
- They can only view instalments associated with that application number and pay.
- If they try to register again for another student, they get an error since their account is already registered with their email.

**Possible Solution Discussed**: combine Organization ID and Organization Representative ID (OrgID + OrgRepID) to uniquely identify each representative across multiple students. Remaining challenge: how to share this unique ID with the sponsor to connect to TransferMate.

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | OrgID | Unique Technical ID | Integer | From APOL, to be selected |
| 2 | Application Number | Unique Functional Code from existing Scholarship Application | Free Text | From APOL |
| 3 | Contact ID | Enter the contact | LOV (Char7) | Data entry by Admission or Financial Aids |
| 4 | Linked The Org | | Free Text Char150 | |
| 5 | Programme | | LOV | From CostCent&ProductLine Programme List (ask Leslie) |
| 6 | Active | To hide the display of inactive items in the Payment Plan Module | Y/N | If Inactive, not displayed in any LOV |

Constraint: a PeopleSoft organization has a unique identifier per country at least (e.g. BNP France and BNP Singapore have different identifiers). Example: Accenture France has 1 OrgID with multiple contacts, all seeing all payments for Accenture France regardless of participants.

### Sponsor Payment Plan Closure
Determined based on the PeopleSoft Sponsor Payment Plan Instalment Total Amount, where: PeopleSoft Sponsor Payment Plan Total Instalment Quantity ≥ PeopleSoft Payment Plan Total Instalment Quantity. Users must update the assigned instalment amount to 0 when a sponsor decides not to pursue the INSEAD program; when set to 0 and status "Unpaid", collection is closed.

### Convention de Stage
Involve Loic.

### Pay
**Instalments Webhook**: a webhook lets one system send real-time data/notifications to another when a specific event occurs, instead of the receiving system polling for updates — the data is pushed automatically as soon as the event happens (e.g. e-commerce payment processed ⇒ webhook sends payment data immediately to update inventory or send confirmation).

### instalment Split
When TransferMate instalment statuses are 0-Registered, 1-Pending Payment, 2-Paid, or 3-Inactive, the PeopleSoft instalment amount cannot be split.

| Transfermate Payment Status | Transfermate | PeopleSoft |
|---|---|---|
| Missing Status Code | Before Pay Event | PS updatable |
| 0-Registered | Pay Event | PS not updatable |
| 1-Pending | Fund Received | PS not updatable |
| 2-Paid | Fund Paid out (INSEAD) | PS not updatable |
| 3-Inactive | Payment Cancelled | PS not updatable |

When an instalment is not paid and status is not (0,1,2,3), the instalment amount can be updated to 0 (equivalent to marking it deleted, since deletion is not feasible) — this prevents further processing without an actual deletion. PeopleSoft instalment status "Unpaid" does not exist in Transfermate.

**Instalment splitting**: 8–10% of students request to split instalment payments. Business rule: cannot split once TransferMate instalment status is 0, 1, or 2 — the PeopleSoft instalment amount cannot be updated, and shortage instalment amounts cannot be adjusted in these conditions. Financial Aid teams should be informed of TransferMate instalment statuses to ensure proper tracking.

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Payment Status ID | Unique Technical ID | Integer | Cannot be changed. Once used in child tables, deletion is not possible. Read Only. |
| — | Tm Payment Status Code | | Integer | 0,1,2,3 — unique, not updatable, not null |
| 3 | Payment Status Description | Transfermate Payment status description | | Registered, Pending, Paid, Inactive |
| 4 | InstalmentSplit | Forbid instalment Split | Y/N | If Inactive, no instalment amount update |
| 5 | Active | To hide the display of inactive items in the Payment Plan Module | Y/N | If Inactive, not displayed in any LOV |

### Partial instalment amount
- TransferMate instalment amounts must match the corresponding PeopleSoft instalment amounts.
- If the amounts differ, the TransferMate instalment status remains 1 (Pending Payment) until the full expected amount is collected.
- If a customer pays €10,010 (including TransferMate fees), INSEAD should still receive exactly €10,000.
- TransferMate manages exchange rate discrepancies to ensure INSEAD receives the full expected instalment amount, and processes only the registered amount in PeopleSoft — TransferMate contacts the student if they fail to pay in full.
- In case of a shortfall, TransferMate holds the incomplete amount for 10 days (INSEAD requested an extension to 1 month) and notifies INSEAD via email before refunding the student (discussed with Katya on 04/02/2025).
- Refunds from TM to students/customers are not automatic — INSEAD must approve within 10 days or 1 month before TransferMate processes the refund (amount never received in INSEAD account).

*Instalment Creation/Update/Delete – OLD*: an API endpoint provided by TransferMate creates a new student payment plan — the request contains all active instalments; once created in PeopleSoft, it is synced to TransferMate with a payment status (initial value to be checked with Transfermate). Rule: student plan creation/assignment should not be allowed if a payment plan is already assigned.

### instalment Update
A separate API endpoint, with the same structure as the creation API, updates instalment information.

### Update Scenarios
- **Adding a New Instalment**: added in the Student Payment Plan (PS) and the new JSON is sent to TransferMate.
- **Updating an Instalment Due Date**: before modifying details, check the TransferMate status maintained in PS (to confirm with TM team). Instalment status is updated from TransferMate to PS via webhooks.
- **Preconditions for Updating an instalment**: if the TransferMate status is "Registered", "Pending", or "Paid", no modifications can be made in PS. If a discrepancy exists (TM status changed but not yet updated in PS) and PS attempts a modification, an error is thrown and recorded in PS.

### Instalment Deletion/Update
The Transfermate instalment statuses could enable/disable update or deletion of a shared instalment (between PSoft & TransferMate) — same preconditions as above apply.

### Shared instalment (For Deferred Cased)
PeopleSoft sends only instalments assigned to the Student or Sponsor Payment Plan to TransferMate. For deferred Student Payment Plans, paid instalments remain in the original PeopleSoft Student Payment Plan, and a new payment plan is assigned with the new program fee.

## Scholarship
"Scholarship Set Up" — menu: in the "Payment Plan" left menu, "Scholarship Set Up" link points to 2 tabs: "Scholarship Type" and "Scholarship Catalog".

### Scholarship Type
Each scholarship is categorized as:
- **Funded** scholarships — financed externally (mainly through donations); like a Payment invoice item (natural account 46), increasing liabilities/resources.
- **Non-funded** scholarships — financed by INSEAD itself; considered discounts, impacting GST (SGP) and VAT (ADB); like a waiver invoice item (natural account 706, reducing revenue).

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Scholarchip Type * | List of values: TEMPORARY, INSEAD, FUNDED-XCM, FUNDED-XCD, GHIBS FA, ADEK, FUNDED-XEM, FUNDED-XED | Free Text char7 | Cannot be NULL, acts as PK |
| 2 | Scholarship Category * | Funded or Non-funded | LOV | Cannot be NULL, acts as a hierarchy |
| 3 | Long Description | Description of the scholarship | Text | |
| 4 | Source | Where the funds come from; needed to identify the Invoice Item Type Code | | |
| 5 | Invoice Item Type Code | Separates scholarship allocations from external contributions and INSEAD's own revenue streams | LOV | Waiver, payment, Null |
| 6 | Has Expiration | To hide the display of inactive items in the Payment Plan Module | Y/N | |

| Scholarship Category | Scholarship Type | Description |
|---|---|---|
| | TEMPORARY | Temporary scholarship assignment used to ensure accurate payment routing to the new online payment gateway (TransferMate). |
| Non-funded | INSEAD | Non-funded scholarship financed through a percentage of INSEAD programme revenue redistribution. |
| Funded | FUNDED-XCM | Funded scholarship provided by the INSEAD Foundation. |
| Funded | FUNDED-XCD | General category for funded scholarships (source not specifically defined). |
| | GHIBS | External scholarship from Hoffmann Institute |
| | ADEK | *(Description needed — possibly Abu Dhabi Department of Education and Knowledge partnership)* |
| Funded | FUNDED-XEM | Endowed scholarship funded by the INSEAD Foundation; investment-based and recurring. |
| Funded | FUNDED-XED | Funded scholarship associated with loan support mechanisms. |

### Scholarship Catalog (Catalog)
Each Scholarship ID is assigned a type, further classified as funded or non-funded.

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Scholarship Id * | New Scholarship ID (should contain the fund_code concatenated to a promotion or family name to improve search) | Integer | Cannot be changed. Once used in child tables, deletion is not possible. Read Only. |
| 2 | Scholarship Code | Unique Functional Code from existing Scholarship Application | Free Text | Can be NULL; unique |
| 3 | Scholarship Type * | Used to identify the Invoice Item later (points to natural account 706 or 46, once integrated with Horizon) | LOV | Type displayed alongside category in read-only mode (Funded or Non-funded) |
| 4 | Fund Code | LOV from Fusion Accounting Key SEG6 (Accounting_key: SEG1.SEG2.SEG3.SEG4.SEG5.SEG6 = LE.CC.COA.PRGCODE.ACTIVITYCODE.FUNDCODE) | LOV (Char7) | Given by Fusion, table updated weekly via Fusion WS (manually in the meantime). Not mandatory, can be NULL. |
| 5 | Scholarship Name | | Free Text Char150 | |
| 6 | Payment Plan | | Boolean | Enable |
| 7 | Payment Item Type | | Boolean | If Inactive, not displayed in any LOV |
| 8 | Enable | Displays only enabled scholarships in transactions (e.g. Student Payment Plan) | Y/N | If Inactive, not displayed in any LOV |
| 9 | Modified By | UserID who adds/modifies the line | | System Generated |
| 10 | Modified | Last date of adding/modifying the line | Date | System Generated |

*Nota Bene*: table prefixed `F1_SC_` (scholarship application setup).

"Fund Code" — component of the INSEAD account key (segment 6), associated with a Fusion Fund Code Value List Referential (table `F1_SC_FUND`); this referential needs to be implemented and loaded.

| Number | Field | Role | Action/Type | Comment |
|---|---|---|---|---|
| 1 | Fund Code * | LOV from Fusion Accounting Key SEG6 (SEG1.SEG2.SEG3.SEG4.SEG5.SEG6 = LE.CC.COA.PRGCODE.ACTIVITYCODE.FUNDCODE) | Free Text char7 | Cannot be NULL, acts as PK. Given by Fusion, table updated weekly (manually in the meantime). |
| 2 | Description | | Char 200 – Free Text | |
| 3 | Start Date | | Date | |
| 4 | End Date | | Date | |
| 5 | Active | To hide the display of inactive items in the Payment Plan Module | Y/N | If Inactive, not displayed in any LOV |

### Manual Scholarship entry
No Student Plan Funding lines recalculation automatization.

### Student Scholarship Mass Upload
Required upload fields: Emplid, Application Number, Scholarship Code, Currency, Amount. During save, the Amount field is recorded twice — as Original Amount and as Accounted Amount.

- **Rule 001**: a scholarship award is uploaded only if Student Funding Balance = 0.
- **Rule 002**: recalculation applies only to funding items of payment type = Transfermate Item, to give an accurate amount to the Transfermate payment portal.
- **Rule 003**: recalculation takes only funding lines of type "TransferMate item" not yet paid.
- **Rule 004**: when adding the scholarship award amount, reduce the latest funding line amount based on due date.
- **Rule 005**: send deletion request to Transfermate where SP item amount = 0 and Sync Status = 'Transfermate Sync'.
- **Rule 006**: prevent uploading the same scholarship ID to a Student payment plan twice.
- **Rule 007**: if a scholarship award exceeds the student's pending payments, allow the upload and set all pending payments (Deposit and Instalment) to zero; Student Funding Balance won't be zero and Financial Aid must adjust the plan manually (refunds or other cases).
- **Rule 008**: scholarships can only be mass-assigned to students with admitted status (Last Action Reason UA or ENR); not uploaded for non-admitted statuses (e.g. 2C, CHAN).
- **Rule 008 (temporary scholarship overwrite)**: if there is only one existing scholarship in the Student Payment Plan and it is a temporary one (MBA Financial Assistance, code TEMP), it should be fully overwritten (both details and amount) by the new upload. If there are already two scholarships in the Student Payment Plan and one of them is temporary, no changes are allowed through mass upload, to avoid duplicates.
- **Rule 009**: if the same batch has 2 scholarships for the same student, both are allowed.

### Student Scholarship API
When integrating a scholarship award (from APOL): the Amount field is recorded twice (Original Amount and Accounted Amount, normally same currency).

- **Rule 001**: integrated only if Student Funding Balance = 0.
- **Rule 002**: items must be of payment type = Transfermate Item.
- **Rule 003**: recalculation takes only the Instalment not yet paid.
- **Rule 004**: when adding the scholarship award amount, reduce the latest amount based on due date.
- **Rule 005**: send deletion request to Transfermate where Transfermate-item related funding items (instalments) amount = 0 and Sync Status = 'No Transfermate Sync'.
- **Rule 006**: prevent uploading the same scholarship ID twice — an update of the existing award is expected instead.
- **Rule 007**: if a scholarship award exceeds pending payments, allow the insert and zero out/delete all pending payments (Deposit and Instalment) from Transfermate; Financial Aid adjusts the plan manually if the balance isn't zero.
- **Rule 008**: scholarships can only be inserted for students with admitted status (UA or ENR); not inserted otherwise (e.g. 2C, CHAN).
- **Rule 009**: only 'Accepted' scholarship awards are sent to and inserted/updated in the PeopleSoft Student Payment Plan.
- **Rule 010**: no 'Accepted' scholarship award can be deleted via API in PeopleSoft — only manual deletion is possible, requiring the user to recalculate the Funding Items amounts accordingly.

## Mailboxes for TransferMate
The email address `noreply.tuitionpayments@insead.edu` is used by Transfermate for customer notifications. Program-specific addresses:
- MIM: `mim.TMPayment@insead.edu`
- MBA: `mba.TMPayment@insead.edu`
- GEMBA + TIEMBA (all sections): `emba.TMPayment@insead.edu`
- EMC: `emc.TMPayment@insead.edu`
- EMFIN: `emfin.TMPayment@insead.edu`

A shared mailbox `tuition.transfermate@insead.edu` has 5 sub-folders with rules routing incoming emails from the above addresses automatically. TransferMate BCCs copies of all customer emails to the corresponding program mailbox for tracking.

**Test Environment Email Handling**: in INT/UAT, most student data is masked but preferred (real INSEAD) email addresses remain unchanged, causing reminder emails to reach actual students.
- Action: all outgoing emails from INT/UAT have been disabled in coordination with TransferMate.
- A new shared mailbox `dev-tuition.transfermate@insead.edu` was created to receive all INT/UAT test emails.
- Sponsor-related email flows should only use dummy/internal addresses for now — sponsor email redirection is under development on TransferMate's side.

## Student Payment Plan Overview
### Search Filter
At least 1 field must be filled in to allow a search. By default shows all Payment Plans set up in Tuition Payment Plan Management that are Active and not past their expiration date. When a Product Name is entered, the Payment Plan LOV is limited accordingly.

### Student Payment Plan Search
Aggregated totals per student payment plan display: Recruiter Name, Next Payment Amount, Next Payment Date, Transferred Paid Amount, Deposit Paid Amount, Total Remaining Amount to be Paid, Total Scholarship Amount, Prodigy Loan Amount, Total Global Loan Amount, Student Payment Balance, and Organization Payment Balance.

### Download to Excel
The downloaded Excel file has more columns than shown on the page.


# Technical Specifications

## Overview
The primary purpose of this specification is to outline detailed implementation protocols, security measures, and error handling procedures necessary for successful integration:
- **System Architecture Overview**: PeopleSoft acts as the source of truth for student and payment data, AIP manages orchestration and transformation, and TransferMate handles payment processing and status updates.
- **API Specifications**: REST-based endpoints for data extraction, payment plan creation/updates, and webhook APIs for real-time payment status notifications between systems.
- **Authentication Methods**: JWT (JSON Web Tokens), API keys, and Basic Authentication safeguard data communication across all integration layers.
- **Data Synchronization Criteria**: driven by batch processes, event-based triggers (webhooks), and timestamp-based updates, ensuring consistency of payment plans and statuses across all systems.
- **Error Handling Mechanisms**: classification of errors (validation, authentication, system), retry mechanisms for transient failures, and detailed logging for monitoring, auditing, and troubleshooting.

Reference: `Peoplesoft_AIP_Transfermate_Student_Payment_Integration.docx`

- **Integration Scope**: synchronization of student tuition payment plans between PeopleSoft and TransferMate via AIP, for a centralized and automated payment process for Degree Programme students.
- **Source and Target Systems**: PeopleSoft = system of record; AIP = orchestration/transformation; TransferMate = external payment gateway (payments and payout processing).
- **Payment Plan Synchronization**: active student payment plans are periodically extracted from PeopleSoft, transformed by AIP, and transmitted to TransferMate via REST APIs (creation and update, batch mode).
- **Event-Driven Payment Updates**: payment status updates triggered by TransferMate webhook events at the instalment level; AIP propagates real-time changes back to PeopleSoft via secured REST APIs.
- **Data Synchronization Mechanism**: AIP uses polling with watermark-based extraction from PeopleSoft, combined with message queueing and batch processing for scalable, reliable transfer.
- **API Interaction Model**: REST APIs for both upstream (PeopleSoft → TransferMate) and downstream (TransferMate → PeopleSoft) communication — retrieving plans, creating/updating records, receiving payment status events.
- **Authentication and Security**: OAuth2, HTTPS, and token-based authentication secure all API interactions between AIP, PeopleSoft, and TransferMate.
- **Error Handling and Retry Logic**: structured error handling with retry for transient failures, logging in AIP dashboards, message queues (including poison queues) for failed transactions and traceability.
- **Batch Processing Strategy**: payment plans processed in batches (e.g. 100 records/transaction), with transactional consistency and rollback for failed records.
- **Status Lifecycle Management**: payment statuses follow a defined lifecycle (Pending, Processing, Funds Received, Paid), each update triggering synchronization.
- **Limitations and Future Enhancements**: refund processing and multi-sponsorship support are out of scope, planned for future integration phases.

Reference: `AIP Integration Specification Tuition Management Gateway - v0.2.docx`

- **Data Transformation and Mapping**: AIP applies transformation rules aligning PeopleSoft data structures with TransferMate requirements (field mappings for student details, sponsor information, instalment attributes per the shared mapping specification).
- **Student and Sponsor Data Handling**: supports individual and sponsored payment scenarios, capturing student identity, contact details, and optional organization (sponsor) information.
- **Instalment Management**: payment plans include multiple instalments with amount, currency, due date, and payer identity, enabling flexible instalment-based tuition payment processing in TransferMate.
- **Field Mapping Governance**: relies on a comprehensive field mapping framework defining data types, formats, transformation rules, and CRUD operations, ensuring data consistency and integrity across systems.

Reference: `API_Field_Mapping_Shared.xlsx`

## Student Journey
### From INSEAD web site to Transfermate Student financial Account
*(Diagram in the original Word document — not reproduced here.)*

## Technical Changes
### Project Name
- `F1_TRANSFERMATE`
- `F1_TRANSFERMATE_API`
- `F1_TRANSFERMATE_SCHOLARSHIP`

### Component
- `CY2_PP_COMMON_SETUP` – Common Setup: Legal Entity, Legal Entity & Currency, Payment Plan Type, Payment Type, Payment Item
- `CY2_SCH_TYPE` – Scholarship Setup: Scholarship Type, Scholarship Catalog
- `F1_FUS_COA` – Chart of Accounts Setup: Fund, Activity, Bank
- `F1_BUDGET_PRODUCT` – (Existing) Cost Centre and Financial Product Lines pages
- `CY2_PP_SETUP` – Tuition Payment Plan Management: Payment Plan Setup and Programme Price
- `CY2_PP_ASGN_FL` – Payment Plan Assignment (fluid)
- `CY2_PP_TRAN` – Student Payment Plan
- `CY2_PP_OVRW_FL` – Payment Plan Overview (fluid)
- `CY2_PP_SPOST_FL` – Posting to Customer Account (fluid)

### Page
- `CY2_PP_LEGALENTITY` – Legal Entity setup page
- `CY2_PP_ENTITY_CUR` – Legal Entity Currency setup page
- `CY2_PP_TYPE` – Payment Plan Type setup page
- `CY2_PP_PAY_TYPE` – Payment Type setup page
- `CY2_PP_PAY_ITEM` – Payment Item setup page
- `CY2_SCH_TYPE` – Scholarship Type
- `CY2_SCH_CATALOG` – Scholarship Catalog
- `F1_FUS_FUND` – Fund
- `F1_FUS_ACTIVITY` – Activity
- `F1_FUS_BANK` – Bank
- `F1_BUDGET_PRODUCT` – (Existing) Cost Centre setup page
- `F1_PRODUCT_LINE` – (Existing) Financial Product Lines page
- `CY2_PP_SETUP` – Tuition Payment Plan Setup
- `CY2_PP_PRICE` – Programme Price
- `CY2_PP_ASGN_FL` – Payment Plan Assignment (fluid main page)
- `CY2_PP_ASG_SRCH_FL` – Assignment Search (fluid search page)
- `CY2_PP_ASGRSLT_SBF` – Assignment Result (fluid subpage)
- `CY2_PP_TRAN` – Student Payment Plan (main page)
- `CY2_PP_TRAN_SBP` – Student Payment Plan (subpage)
- `CY2_PP_OVERVIEW_FL` – Student Payment Plan Overview (fluid main page)
- `CY2_PP_OVW_SRCH_FL` – Overview Search (fluid search page)
- `CY2_PP_OVWRSLT_SBF` – Overview Result (fluid subpage)
- `CY2_PP_PST_FL` – Posting to Customer Account (fluid main page)
- `CY2_PP_PST_SRCH_FL` – Search (fluid search page)
- `CY2_PP_PSTRSLT_SBF` – Search Result (fluid subpage)

### Base Tables / Views
- `CY2_PP_INSTALL` – Installation Table for sequence numbers and defaults
- `CY2_PP_DERIVED` – Derived/Work Table
- `CY2_PP_ENTITY` – Legal Entity setup main table
- `CY2_PP_EN_CUR` – Legal Entity Currency setup main table
- `CY2_PP_TYPE` – Payment Plan Type setup main table
- `CY2_PP_PAY_TYPE` – Payment Type setup main table
- `CY2_PP_ITEM_PRG` – Payment Item program setup
- `CY2_PP_ITEMTYPE` – Payment Items per program setup
- `CY2_SCH_TYPE` – Scholarship Type
- `CY2_SCH_CATALOG` – Scholarship Catalog
- `F1_FUS_FUND` – Fund
- `F1_FUS_ACTIVITY` – Activity
- `F1_FUS_BANK` – Bank
- `F1_PRODUCT_LINE` – (Existing) Financial Product Line setup main table
- `F1_FUS_DPBUDGET` – (Existing) Cost Centre setup main table
- `CY2_PP_SETUP` – Tuition Payment Plan Setup main table
- `CY2_PP_SINSTALL` – Payment Plan Setup Instalments
- `CY2_PP_PRICE` – Programme Price
- `CY2_PP_ASGN_VW` – Payment Plan Assignment search view
- `CY2_PP_ASGN_WRK` – Payment Plan Assignment derived/work table
- `CY2_PP_TRAN` – Student Payment Plan main table
- `CY2_PP_TRN_INST` – Student Payment Plan Instalments
- `CY2_PP_TRN_TFMT` – Student Payment Plan Transfermate
- `CY2_PP_TRN_CMNT` – Student Payment Plan Instalments - Comments
- `CY2_PP_STOVW_VW` – Student Payment Plan Overview result view
- `CY2_PP_STPST_VW` – Posting to Customer Account result view

### Audit Tables
- `AUD_CY2_PP_TRN` – Audit record for `CY2_PP_TRAN` — trigger `PP_TRAN_TR`
- `AUD_CY2_PP_TRNI` – Audit record for `CY2_PP_TRN_INST` — trigger `PP_TRN_INST_TR`

### Fields
- `CY2_PP_TR_SYNC` – Student Payment Plan – Instalments Sync Status: D – Deferred No Sync, N – Transfermate No Sync, Y – Transfermate Sync
- `CY2_PP_TR_STAT` – Student Payment Plan – Transfermate Payment Status: 0 – Pending, 1 – Processing, 2 – Funds Received, 3 – Paid
- `CY2_PP_EXT_STAT` – Student Payment Plan – External Payment Status: 0 – Pending, 1 – Processing, 2 – Funds Received, 3 – Paid, 4 – Refund Registered, 5 – Refund In Progress, 6 – Refund Completed, 7 – Refund Cancelled

### Menu
- `CY2_PAYMENT_PLAN_MENU`

### Application Package
- `CY2_PAYMENT_PLAN`

### Application Engine
- `CY2_PP_STPST` – Posting to Customer Account (mass process)

### Component Interface
- `CY2_PP_TRAN`

### Stylesheets
- `CY2_PP_CSS`
- `CY2_PP_ASGN_FL`
- `CY2_PP_OVRW_FL`

### Message Catalog
- `32500`

### CY2 CE Transaction Messages
- `9001` – `9004`

### Permission List
- `F1_PAYMENT_PLAN_ADMIN`

### Role
- Payment Plan Admin – Payment Plan Generic Admin Role
- `F1_PAYMENTPLAN_EDIT` – Access to Edit Paid instalment (given to specific users only)

### Peoplesoft to Transfermate API Table
- `F1_ESB_PP_VW` – Payment Plan
- `F1_ESB_PPINS_VW` – Payment Plan Instalments
- `F1_ESB_PPST_VW` – Student
- `F1_ESB_PPSP_VW` – Student Phone
- `F1_ESB_PPSA_VW` – Student Address
- `F1_ESB_PPORG_VW` – Organization
- `F1_ESB_PPOCN_VW` – Organization Contact
- `F1_ESB_PPOCA_VW` – Organization Contact Address

### Service Operation
- `F1_ESB_PAYMENTPLAN_GET`
  - By Date: `{{ps_domain}}/PSIGW/RESTListeningConnector/PSFT_CS/paymentplan.v1/?from=2025-05-01T04:00:00.000Z&to=2025-07-26T04:05:00.000Z&page=1&page_size=100`
  - By ID: `{{ps_domain}}/PSIGW/RESTListeningConnector/PSFT_CS/paymentplan.v1/00590107`
- `F1_ESB_PAYMENTSTATUS_POST` — `{{ps_domain}}/PSIGW/RESTListeningConnector/PSFT_CS/payment_status.v1`
- `F1_ESB_SCHOLARSHIPS_POST` — `{{ps_domain}}/PSIGW/RESTListeningConnector/PSFT_CS/scholarships.v1`
- `F1_ESB_SCHOLARSHIP_AWARD_POST` — `{{ps_domain}}/PSIGW/RESTListeningConnector/PSFT_CS/scholarships_awards.v1`

### Entity
**Payment Plan**:
- `SCC_ENTITY_20250407034951` - `F1_ESB_PAYMENTPLAN`
- `SCC_ENTITY_20250407053508` - `F1_ESB_PAYMENTPLAN_STUDENT`
- `SCC_ENTITY_20250407055127` - `F1_ESB_PAYMENTPLAN_STUD_PHONE`
- `SCC_ENTITY_20250407060437` - `F1_ESB_PAYMENTPLAN_STUD_ADDR`
- `SCC_ENTITY_20250407065551` - `F1_ESB_PAYMENTPLAN_ORG`
- `SCC_ENTITY_20250407072044` - `F1_ESB_PAYMENTPLAN_ORG_CONTACT`
- `SCC_ENTITY_20250407080545` - `F1_ESB_PAYMENTPLAN_ORG_ADDRESS`
- `SCC_ENTITY_20250407084014` - `F1_ESB_PAYMENTPLAN_INSTALLMENT`

### PS Query Reports
- `F1_PP_SCHOLARSHIP` – Scholarship (base view: `PS_F1_PP_SCHSHP_VW`)
- `F1_PP_SPONSOR` – Sponsor (base view: `PS_F1_PP_SPNSR_VW`)
- `F1_PP_ADMISSION_PAID` – Admissions Funds Received/Paid (base view: `PS_F1_PP_ADM_VW`)
- `F1_PP_ADMISSION_PENDING` – Admissions Pending/Processing (base view: `PS_F1_PP_ADM_VW`)
- `F1_PP_OUTSTANDING_PAYMENT` – Outstanding Payment - Past Due (base view: `F1_PP_ADM_VW`)
- `F1_PP_WITHDRAWAL_PAYMENT` – Withdrawal - Payment Deletion (base view: `F1_PP_ADM_VW`)
- `F1_PP_FULL_SPP` – Full Student Payment Plan (base view: `F1_PP_FULL_VW`)
- `F1_PP_DUP_CONTACTEMAIL` – Duplicate Sponsor ContactEmail (base view: `F1_PP_DUPCONTVW`)


# Decisions and ideas (suggestion)
Keeps track of past changes and decisions taken to not lose them and keep the document clean.

- DP Term calculation functionality is dropped in the first phase of the project.
- Term Fee calculation is no longer required and is replaced by the payment plan module.
- Link to the invoice: the Payment Plan Set Up links the payment items (DEPOSIT & SCHOLARSHIP).
- New process to add charges (Term fees Career + Term (+ Campus), e.g. 100k euros) of a curriculum and link to the payment plan.
- Remove complete student balance in PeopleSoft financials for Curriculum MBA25J* (clean-up, SOW section 7).
- Open question: can we select the DP programme Code (Payment Plan Code) from Horizon via webservices? Can the business select the right code in PeopleSoft to create the Payment Plan based on pre-defined DP programme code? Get the right description from Horizon.
- Open question: how do we select the right students for a chosen programme code (e.g. admitted students for EMCF24J via Programme/Graduation Term/Admin Campus) — can be done at payment plan creation time.
- Action: Katya to provide value & business rule for mass selection of admitted students to a default payment plan.
- Idea: take the Application Data Tab Round 1/2/3 values as a filter.
- Out of Scope Phase 1: GEMBA SGP and ABU will be invoiced and their scholarships created in Phase 2.
- Idea: move the Instalment Deadline to the Student assignment process.
- Idea: move Legal Entity and Legal Entity & Currency setup pages under Chart Of Account Set Up.
- Idea: rename Cost Centre/Product Line to DP Programme Catalog; rename Activity Code to Bank Account Name (LOV from the Bank reference table).
- Open question (Q01): how do companies connect to / register in Transfermate? No solution identified yet.
- Action: Katya to provide the Excel file managing sponsor contact information (to be replaced by the new Sponsor Management setup).
- Wishlist: an item type that automatically calculates GST for scholarships, to support the Scholarship Revamp process.

**Phase 2 roadmap** (large developments, ~€100k estimated cost):
- Sponsor Management across Payment Plan applications, Student application general materials, invoices, and Third-Party Contracts.
- Payment Plan Instalments linked to invoice items (deposits, scholarships, charges).
- Programme Catalogue: Tuition (TU), meal packages, insurance — charges sold independently or in a package.
- Programme Pricing Management (Product Management): tax-included price items (charges) — Marketing programme price and accessory price (Proctoring test, application fees, insurance, meal package, Business Foundation).
- Invoice Process Management: enhancements to the invoice dashboard, PeopleSoft vs Fusion invoice printing, PeopleSoft financials customer accounts, alignment with Oracle Fusion invoice items (tax and receipt).
- Tax Management and Tax Engine: tax calculation for European countries and program packages.
- Synchronization between Oracle Fusion and PeopleSoft: send PeopleSoft DP invoices, receipts, taxes to Fusion.
- Scholarship Enhancement Process: integration between the new scholarship application and the Student Transactions Module in PeopleSoft.
- Refund Automation.
- Admin Dashboard in TransferMate.
- Partial Payment Processing.
- Improvement of Auto-Calculation Features.
- Report Creation and Management.
- Decommissioning of existing Student Instalment Excel File databases.
- Prodigy Automation Process — sync from Transfermate to PeopleSoft.


# Glossary

| Term | Meaning |
|---|---|
| **TMG** | Tuition Management Gateway — this project/module. |
| **DP** | Degree Programme. |
| **PS / PeopleSoft** | The university's student administration system; system of record for student and payment data. |
| **TM / Transfermate** | External payment gateway used to collect student/sponsor tuition payments. |
| **AIP** | Integration/orchestration layer between PeopleSoft and TransferMate. |
| **LOV** | List of Values — a dropdown/selectable list of valid options in PeopleSoft. |
| **LOA** | Leave of Absence. |
| **EPW** | Exchange student (PeopleSoft admit type code). |
| **VIS** | Visiting student (PeopleSoft admit type code). |
| **CPF** | Central Provident Fund — Singapore's national retirement/education savings scheme, referenced here for student payment use cases. |
| **GST** | Goods and Services Tax (Singapore). |
| **VAT** | Value Added Tax (applies, to a lesser extent, in Abu Dhabi/ADB). |
| **Fund Code** | Segment 6 of the INSEAD/Fusion accounting key, identifying the funding source of a scholarship. |
| **Horizon** | INSEAD's data/reporting layer sourcing programme and account key information. |
| **Fusion** | Oracle Fusion ERP — INSEAD's finance/accounting system. |
| **F1_ / CY2_ prefixes** | Naming convention for PeopleSoft custom objects (tables, pages, components) built for this project. |
| **Instalment** | A single scheduled payment within a Payment Plan (e.g. deposit, 2nd instalment). |
| **Sync Status** | Flag controlling whether an instalment is sent to / removed from TransferMate via the Payment Plan API (Transfermate Sync, Transfermate No Sync, Deferred No Sync). |
| **Payment Item** | A configured type of payment (e.g. First Instalment, Scholarship, Loan) usable in a Payment Plan. |
| **Payment Plan** | The template of instalments defined for a programme/campus/currency combination. |
| **Student Payment Plan** | The specific instance of a Payment Plan assigned to one student, tracking actual amounts, payments, and balances. |
| **Sponsor** | A company or individual (other than the student) paying part or all of the tuition. |
| **APOL** | External scholarship application system feeding scholarship awards into PeopleSoft. |