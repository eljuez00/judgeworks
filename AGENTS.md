# Instructions for AI agents

You are working in JudgeWorks, a public repo. Follow these rules.

1. **Never add secrets.** No keys, tokens, passwords, personal paths (`/Users/<name>`), usernames, device names or private hostnames. Use `~/` and placeholders like `<your-token>`. See `SECURITY.md`.
2. **Keep docs truthful.** Only record steps and numbers that were actually run and measured. Mark anything untested as untested.
3. **Small, reviewable changes.** One topic per commit. Don't rewrite unrelated files.
4. **Decisions go in `docs/decisions/`** as numbered records (`NNNN-title.md`) with Context, Decision, Alternatives and Consequences.
5. **Build notes go in `docs/journal/`** as dated entries. Mistakes and fixes are worth keeping.
6. **Config in `config/` must be valid.** Check JSON with `python3 -m json.tool <file>`.
7. **Ask before anything irreversible**, such as deleting files, pushing, publishing or installing software.
8. **Writing style:** plain English, short sentences, commands in code blocks, and every command should work as pasted.
