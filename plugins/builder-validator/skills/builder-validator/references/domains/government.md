# Government Domain Attack Patterns

Apply these patterns when reviewing government/public sector specifications.

## Patterns

- **Document approval chain manipulation**: Initiator modifies approval chain to remove objecting approver. Audit trail required but is it enforced?
- **Approval authority delegation and substitution**: Delegate fails to act. Recursive delegation. All delegates unavailable during holidays.
- **Document lifecycle dead-end states**: Approved but no resolution. On execution but executor deactivated. Registered but never sent for approval.
- **Cross-department timing collisions**: Two departments act on the same document within the same minute. Conflicting actions.
- **Regulatory compliance gaps**: GOST, ISO, or local regulatory requirements not reflected in specification.
- **Archival and retention policy gaps**: What triggers archival? How long is retention? Can archived documents be retrieved? By whom?
- **User deactivation with active documents**: Employee leaves with 50 documents in various states. Ownership transfer mechanism.
- **Notification delivery failure handling**: Email fails, but business process continues. Who knows the notification was lost?
- **Concurrent editing conflicts**: Two users editing same document metadata simultaneously. Last-write-wins or locking?
- **Registration number collision**: Year boundary, format overflow, concurrent registration.
