# Installing Gitea for HR change control

How a company running JudgeWorks sets up its **own local Gitea**, so the HR department controls what its agents may do. Why we do this: [decision 0003](../../docs/decisions/0003-gitea-change-control.md).

> The JudgeWorks project itself lives on GitHub. These instructions are for the company deploying it. Version tested in the docs: Gitea **28.3.0**. Check [gitea.com](https://about.gitea.com) for the current release.

## 1. Choose where it runs

An always-on machine inside the company network: a small server, a VM, or a Mac or Linux box. Don't expose it to the internet. Staff reach it on the internal network or VPN.

## 2. Install

### Option A: Docker (recommended for servers)

Create `docker-compose.yml`:

```yaml
networks:
  gitea:
    external: false

services:
  server:
    image: docker.gitea.com/gitea:28.3.0
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "222:22"
```

```sh
docker compose up -d
docker compose ps
```

### Option B: macOS with Homebrew

```sh
brew install gitea
brew services start gitea
```

Logs are written to `$(brew --prefix)/var/log/gitea.log`.

## 3. First-run setup

1. Open `http://<server>:3000` and complete the installation wizard.
2. **Database:** SQLite is fine for one HR department. Use PostgreSQL if many teams will use it.
3. **Admin account:** create it in the wizard and store the password in the company's password manager, never in a repo.
4. **Turn off public sign-up:** Site Administration → set "Disable self-registration".
5. Gitea creates its secret keys on first run. **Don't change `SECRET_KEY` afterwards**, and back up `app.ini` securely.

## 4. Organization, teams and repos

Create an organization (for example `hr-ai`) with these teams:

| Team | Members | Permission |
|---|---|---|
| `hr-policy-owners` | HR policy leads | Write on `hr-policies` |
| `hr-ai-admins` | HRIS and IT owners of the harness | Admin |
| `payroll-leads` | Payroll managers | Write on `agent-config` (payroll rules) |
| `auditors` | Internal audit, compliance | Read |

Create three repos from the JudgeWorks templates: `hr-policies`, `agent-config` and `evals`.

## 5. Protect `main`

In each repo, go to Settings → Branches → add a protection rule for `main`:

- Require a pull request; no direct pushes
- Require **at least 1 approval** (2 for `agent-config`)
- Require approval from **code owners**
- Require status checks to pass (the eval run, step 7)
- Dismiss old approvals when new commits are pushed

## 6. Code owners

Add `.gitea/CODEOWNERS` (a file listing who must approve changes to which files) to each repo. Example for `agent-config`:

```
# Autonomy levels and tool permissions: HR and IT must both approve
/autonomy/        @hr-ai/hr-ai-admins @hr-ai/hr-policy-owners
/tools/           @hr-ai/hr-ai-admins
# Payroll rules
/payroll/         @hr-ai/payroll-leads
```

## 7. Automatic evals (Gitea Actions)

1. Site Administration → Actions: enable Actions.
2. Install a runner (`act_runner`) on the same or another internal machine, and register it with the token from Site Administration → Actions → Runners.
3. Add a workflow at `.gitea/workflows/evals.yml` that runs the JudgeWorks eval suite on every pull request. The workflow will be published with the first eval suite.

## 8. How HR staff use it (no git knowledge needed)

1. Open the repo in the browser and find the policy file.
2. Click **Edit**, make the change, and choose **"Create a new branch and start a pull request"**.
3. The named approvers review it, the evals run automatically, and the change merges once both pass.
4. Agents load only what is merged to `main`.

## 9. Back up

Back up the `/data` volume (Docker) or `$(brew --prefix)/var/gitea` (Homebrew) daily. Restore-test once a quarter.
