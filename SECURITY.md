# Security

## What never goes in this repo

- API keys, access tokens, tunnel tokens, service tokens and passwords
- `.env` files or anything with real credentials
- Personal paths, usernames, device names, IP addresses or hostnames of private machines
- Logs that haven't been checked for the above

Examples use placeholders like `<your-token>` and `~/` paths.

## Rules the setup follows

- Ollama listens only on `127.0.0.1`. It has no authentication, so it is never exposed to the internet. Remote access goes through a private network, never a public port.
- Coding agents run in a sandbox folder kept under git. Destructive or outbound commands (`rm`, `sudo`, `curl`, `wget`, `git push`, `brew`) need human approval.
- Browser automation uses a separate browser profile with no saved logins.
- Cloud model providers are disabled in agent configs, so nothing falls back to a hosted model by accident.

## Reporting a problem

If you find a secret or a security issue here, please open a GitHub issue without including the secret, or email eljuez@eljuezproductions.com.
