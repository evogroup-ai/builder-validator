# Telecom Domain Attack Patterns

Apply these patterns when reviewing telecom specifications.

## Patterns

- **Prepaid balance concurrent deduction**: Call active while data package purchased via USSD. Both check balance independently. Negative balance on prepaid.
- **Plan change during active session**: User changes tariff plan while a call/data session is active. Which rate applies?
- **Number portability mid-process**: Number transfer initiated while incoming calls are active. Routing during transition period.
- **Roaming state transitions**: Device moves between networks. Session handover failures. Billing jurisdiction changes.
- **Service suspension/restoration cycles**: Suspended for non-payment, payment received, restoration timing. Pending SMS during suspension.
- **Bundle dependency chains**: Bundle includes calls + data + SMS. One component depleted. Does it affect others?
- **SIM swap during active authentication**: 2FA code sent to old SIM during swap process. Security vs availability trade-off.
- **Billing cycle boundary timing**: Usage at 23:59:59 on cycle end date. Which cycle is it billed to?
