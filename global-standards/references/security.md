# Security Standards

- Never log or commit secrets, credentials, tokens, private keys, OTP material, or unnecessary personal data.
- Validate inputs and configuration at system boundaries, especially when data crosses trust boundaries or enters persistence.
- Authentication, authorization, session, token, encryption, and secret-management changes require targeted validation and explicit rollback awareness.
- Avoid widening access surfaces such as debug endpoints, admin routes, storage permissions, or deployment exposure without a clear requirement.
- When changing dependencies or infrastructure-sensitive configuration, call out security implications even if the change appears mechanical.
