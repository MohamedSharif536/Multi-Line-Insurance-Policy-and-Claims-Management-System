# Multi-Line Insurance Policy and Claims Management System

A Salesforce CRM application for an insurance business that sells **Vehicle, Property and Life** insurance. It brings quoting, policy issuance, premium calculation, claim routing, high-value claim approval and adjuster reporting into one Salesforce org.

| | |
|---|---|
| **Team ID** | SWTID-2026-5200 |
| **Team Leader** | Mohamed Sharif N |
| **Team Members** | Vishal Manikandan R, Vijaymanikandan S, Thirumalaivasan R, George Daniel Raj. A |
| **College** | Arasu Engineering College |
| **Platform** | Salesforce Developer Edition Org (Lightning Experience) |
| **Submission Date** | 3rd October 2026 |

## Project Links

- **Project Documentation (PDF):** [Multi-Line Insurance Policy and Claims Management System Mohamed Sharif N.pdf](https://github.com/MohamedSharif536/Multi-Line-Insurance-Policy-and-Claims-Management-System/blob/main/Multi-Line%20Insurance%20Policy%20and%20Claims%20Management%20System%20Mohamed%20Sharif%20N.pdf)
- **Demo Video:** [Watch on Google Drive](https://drive.google.com/file/d/19SW9gUKIuNwMoKWzwKEcDcM7fvfHYs08/view?usp=sharing)
- **Repository:** [GitHub](https://github.com/MohamedSharif536/Multi-Line-Insurance-Policy-and-Claims-Management-System)

## Problem Statement

Insurance operations handle several product lines at once, and each line needs different information. Without a central system, quoting takes many manual steps, claims are assigned by hand, high-value claims have no formal escalation route, and nobody has a complete view of customer, policy and claim data. This causes slow processing and inconsistent data.

## Objectives

- Store customer, policy and claim data for Vehicle, Property and Life insurance in one structured system.
- Standardise and speed up quoting through a guided Screen Flow.
- Calculate premiums automatically with Apex.
- Reject invalid vehicle data, such as a VIN that is not exactly 17 characters.
- Route every new claim to the correct queue based on policy type.
- Send high-value claims (above 50,000) through a two-step approval process.
- Give claims adjusters a focused dashboard of their assigned claims.
- Control access with state-based sharing rules and Permission Sets.

## Key Features

| Feature | Implementation |
|---|---|
| Multi-product policies | `Policy__c` with **Auto, Property, Life** Record Types and Field Sets |
| Claims management | `Claim__c` with **Accident, Property, Life** Record Types, linked to a policy |
| VIN validation | Validation rule `VIN_Must_Be_17_Characters` |
| Guided auto quoting | Screen Flow `AutoQuotingFlow` creates a draft policy |
| Premium calculation | Invocable Apex class `PremiumCalculator` |
| Claim routing | Record-triggered Flow assigns claims to the Auto, Property or Life queue |
| High-value claim approval | Approval process (Senior Adjuster, then Manager), `Submission Automation Flow`, `Claim Approver Screen Flow` and the `Approve/Reject Claim` quick action |
| Adjuster dashboard | LWC `claimsDashboardLwc`, `claimTileLwc` and Apex `ClaimsAdjusterController` |
| State-based access | Flow copies policy state to the claim; sharing rules for CA and TX |
| Role-based access | Permission Sets: Insurance Agent Access, Claims Adjuster Access, Claims Manager Access |

## Technology Stack

| Layer | Technology |
|---|---|
| Platform | Salesforce, Lightning Experience |
| Data model | Custom objects `Policy__c`, `Claim__c`; standard Contact and User; Record Types, Field Sets |
| Declarative automation | Screen Flow, Record-Triggered Flows, Validation Rule, Approval Process, Quick Action |
| Programmatic | Apex (`PremiumCalculator`, `ClaimsAdjusterController`), Apex test class |
| Interface and analytics | Lightning Web Components, reports and dashboards |
| Security | Permission Sets and state-based sharing rules |
| Tools | Setup, Object Manager, Flow Builder, Developer Console, Lightning Studio extension |

## How It Works

1. An agent runs the **Auto Quoting Screen Flow** and enters policy holder, start date, state, VIN and model year.
2. The validation rule rejects any VIN that is not 17 characters.
3. The flow creates a draft `Policy__c` with the Auto Record Type.
4. `PremiumCalculator` runs and the flow saves the result in the Premium field.
5. A `Claim__c` is created and linked to a policy.
6. A record-triggered Flow reads the policy type and sets the claim owner to the Auto, Property or Life queue.
7. If Claim Amount is above 50,000 and status is New, the claim is submitted for approval (Senior Adjuster, then Manager).
8. The approver chooses Approve or Reject with comments, and the status is updated.
9. Adjusters open the LWC dashboard to see their own claims with days open.

## Data Model

| Object | Key fields |
|---|---|
| **Policy (`Policy__c`)** | Customer (lookup Contact), Policy Start Date, Premium, Policy State, VIN, Model Year, Square Footage, Year Built, Beneficiary Name, Policy Term Months |
| **Claim (`Claim__c`)** | Policy (lookup), Adjuster (lookup User), Claim Amount, Date of Loss, Description, Approval Status (New, Submitted for Approval, Approved, Rejected), Policy Account Holder State |

Record names are auto-numbered: `P-{0000}` for policies and `C-{0000}` for claims.

## Premium Rules

| Rule | Effect |
|---|---|
| Base premium | 1,000.00 |
| Policy State is CA | Base x 1.15 |
| Policy State is TX | Base x 1.05 |
| Model Year earlier than 2018 | Add 100 |

Example: a CA policy with model year 2020 gives a premium of 1,150.00.

## Approval Process

- **Entry criteria:** Claim Amount greater than 50,000
- **Step 1:** Senior Adjuster (first response decides)
- **Step 2:** Manager review (first response decides; rejection performs all rejection actions)
- **Status updates:** Submitted for Approval, then Approved or Rejected

## Permission Sets

| Permission Set | Access |
|---|---|
| Insurance Agent Access | Policy: Create, Read. Contact: Read, Edit. Claim: Read. Flow User and `PremiumCalculator` enabled |
| Claims Adjuster Access | Policy: Read. Claim: Read, Edit. `ClaimsAdjusterController` enabled |
| Claims Manager Access | Policy: Read. Contact: Read, Edit. Claim: Read, Edit, Delete. Run Reports and Manage Approvals |

## Project Planning

The project followed an Agile approach with four sprints of 20 story points each (80 points total), from 29 Sep 2026 to 3 Oct 2026. The backlog covered data modeling, quoting, validation, Apex, claim automation, the LWC dashboard, approvals, security and testing.

## Testing

Features were tested from the Lightning interface: VIN validation, AutoQuotingFlow, premium calculation, claim routing, high-value approval, the Approve/Reject action, the dashboard filter, and state-based visibility. The Apex test class `ClaimsAdjusterControllerTest` covers `getAssignedClaims()`. See Section 6 of the project documentation for the full test table.

## Limitations

- The premium rule is a simple model (base amount, CA and TX, model year check).
- The Auto Quoting Flow covers the Auto line only; Property and Life policies are created manually.
- Queue IDs and approver users are set directly in the configuration and need maintenance when they change.
- Days Open does not stop counting when a claim is closed.
- Apex test coverage exists for `ClaimsAdjusterController` only; `PremiumCalculator` and the Flows need tests before production.
- Sharing rules exist for CA and TX only.

## Future Scope

- Integration with external vehicle and property databases for quote verification.
- More insurance products beyond Vehicle, Property and Life.
- Automated claim assessment and escalation.
- Richer reports and dashboards.
- Customer portal (Experience Cloud) and mobile access for agents and adjusters.
- AI assistance for agents and adjusters.

## Source Code

Full Apex and LWC source code is in **Appendix A** of the project documentation: `PremiumCalculator`, `ClaimsAdjusterController`, `ClaimsAdjusterControllerTest`, `claimsDashboardLwc` and `claimTileLwc`.

## Team

**Team ID:** SWTID-2026-5200
**Team Leader:** Mohamed Sharif N
**Members:** Vishal Manikandan R, Vijaymanikandan S, Thirumalaivasan R, George Daniel Raj. A
**College:** Arasu Engineering College
