# Banking Domain Attack Patterns

Apply these patterns when reviewing banking/financial specifications. They supplement generic attack categories with industry-specific risks.

## Patterns

- **Threshold splitting (structuring)**: Submitting multiple transactions below approval thresholds to bypass controls. Check: are cumulative limits enforced? Sliding window or calendar-based?
- **KYC/AML workflow interruptions**: What happens when identity verification is pending but the customer needs urgent service?
- **Concurrent session balance checks**: Two devices initiate transfers simultaneously. Each sees sufficient balance independently. Both succeed. Double debit.
- **Multi-currency edge cases**: Exchange rate changes between approval and execution. Which rate applies?
- **Approval authority matrix conflicts**: Approver is replaced between approval stages. Does the replacement inherit pending approvals?
- **Regulatory hold states**: Account frozen mid-transaction. What happens to pending payments?
- **End-of-day cutoff timing**: Payment approved at 16:59, execution window closes at 17:00. Queued or rejected?
- **Dormant account reactivation**: Reactivated account triggers pending actions from before dormancy.
- **Sanctions list timing**: List updates while payment is pending approval. Re-screen required?
