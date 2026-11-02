# Hooks

Code that runs automatically before or after an agent action. **Status: planned.**

## Ideas

- **Before a tool call:** block commands outside the sandbox, scan for secrets before writes, require approval for listed actions
- **After a tool call:** append to the audit log, run the related eval, and flag failures
- **Before commit:** secret scan with gitleaks, plus JSON and config validation
