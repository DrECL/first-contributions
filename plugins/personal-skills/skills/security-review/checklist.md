# Security Checklist

- [ ] No secrets, API keys, or passwords committed in code or config
- [ ] All user input validated or sanitized before use
- [ ] Database queries parameterized (no string-built SQL)
- [ ] Authentication required on every non-public endpoint
- [ ] Dependencies free of known critical vulnerabilities
- [ ] Errors don't leak stack traces or internal paths to users
