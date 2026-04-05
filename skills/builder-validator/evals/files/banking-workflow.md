# Corporate Payment Approval Workflow

## 1. Overview

Payment processing system for corporate clients. Handles payment initiation, multi-level approval, and execution.

## 2. Payment Initiation

An authorized operator creates a payment order with:
- Beneficiary account (IBAN)
- Amount and currency
- Payment purpose
- Value date

The system validates: IBAN format, sufficient funds, daily/monthly limits, sanctions screening.

## 3. Approval Chain

Payments require approval based on amount thresholds:
- Up to $10,000: single approval by Department Head
- $10,001 - $100,000: Department Head + Finance Director
- Over $100,000: Department Head + Finance Director + CEO

Each approver can: approve, reject with reason, or return for correction.

## 4. Payment States

- Draft: created, not yet submitted
- Pending Approval: waiting for next approver
- Approved: all approvals received
- Rejected: rejected by any approver
- Returned: sent back to operator for correction
- Scheduled: approved, waiting for value date
- Executed: sent to banking system
- Failed: execution error

## 5. Limits and Controls

- Daily limit per operator: configurable per role
- Monthly cumulative limit per department
- Single transaction limit per approval level
- Sanctions list check before execution

## 6. Audit

All actions are logged with timestamp, user, IP address, and action details.

## 7. Business Hours

Payment execution is limited to banking business hours (9:00-17:00 local time, business days only). Payments approved outside business hours are queued for next business day.
