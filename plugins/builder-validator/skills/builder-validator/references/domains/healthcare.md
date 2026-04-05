# Healthcare Domain Attack Patterns

Apply these patterns when reviewing healthcare specifications.

## Patterns

- **Patient consent state changes mid-treatment**: Patient withdraws consent during active treatment plan. What happens to pending procedures?
- **Prescription approval chain breaks**: Prescribing physician unavailable for clarification. Pharmacy can't reach prescriber. Patient waiting.
- **Insurance authorization timing**: Pre-authorization expires during treatment. Mid-procedure authorization denial.
- **Emergency override audit trail**: Emergency access to records bypasses normal permissions. Is override logged? Who reviews overrides?
- **Multi-provider coordination conflicts**: Two providers prescribe conflicting medications. No shared view of full medication list.
- **Record access revocation during active session**: User's access revoked while they're viewing a patient record. Session termination vs data consistency.
- **Lab result notification failure paths**: Critical lab result notification fails to deliver. Patient at risk. Fallback mechanism?
- **Referral chain breaks**: Referred provider unavailable, on leave, or no longer accepting patients. Referral expires without notification to referring provider.
