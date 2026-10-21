# CYBO Attest MCP server

CYBO Attest is the authorization console for applications built on the cDSO (Community DevSecOps) pipeline. Its MCP server lets you, your agents and your bots work with Attest from any MCP client: see what blocks each build, get an ordered plan to fix it, start scans, and justify findings that cannot be fixed, until the build is approved to ship.

This repository describes the server. The server itself is hosted by Ulap; there is nothing to install.

**Server address:** `https://attest.ulapcybo.app/mcp` (Streamable HTTP)
**Registry name:** `app.ulapcybo/attest`
**Documentation:** https://docs.ulapcybo.app/developer/connecting-an-ai-agent/

## What it does

- **Security posture.** The newest scan of every container: its verdict, the gates that fail, why it is blocked and the next action.
- **Fix plans.** For one build, an ordered plan: the approved base image tag to rebuild on, the package versions to move to, and what must be fixed, with the failed job's log.
- **Findings.** Every finding in full, the manifest line and commands that fix a vulnerable package, and Explainable AI answers on what a finding means for your application.
- **Scans.** Start the scans of an application, follow its build and scans step by step, and read each scanner's evidence and job logs.
- **Applications.** Add a GitHub repository, sync an application with its repository's newest commits, use Attest's corrected Dockerfile, and send a version toward review.
- **Justifications.** Draft a justification for a finding that cannot be fixed, edit it, then propose it with a reason a Security Control Assessor can check.

Approving or rejecting a build stays with a Security Control Assessor in the console. No tool can do it.

## Before you start

You need a CYBO Attest account at https://attest.ulapcybo.app. The MCP server signs you in through your browser with OAuth, and every tool acts as you, with your own roles: it can do exactly what you can do in the console, and nothing more. Every change records your name and the client you used.

## Configure your client

### VS Code with GitHub Copilot

Add the server to `.vscode/mcp.json` in your workspace (or to your user MCP configuration):

```json
{
  "servers": {
    "cybo-attest": {
      "type": "http",
      "url": "https://attest.ulapcybo.app/mcp"
    }
  }
}
```

Start the server from the file or the MCP servers list, then sign in when VS Code opens your browser. Use the tools from Copilot Chat in agent mode.

### Claude Code

```bash
claude mcp add --transport http cybo-attest https://attest.ulapcybo.app/mcp
```

Then run `/mcp` in Claude Code and choose CYBO Attest to sign in.

### Other clients

Any MCP client that supports Streamable HTTP and OAuth can connect to `https://attest.ulapcybo.app/mcp`. The server publishes its sign in details at `https://attest.ulapcybo.app/.well-known/oauth-protected-resource/mcp`, so the client finds them on its own.

## How to use it

Ask in plain language. For example:

- "What is blocking my applications in CYBO Attest?"
- "Make a plan to fix the newest build of my sample app, then fix the Dockerfile and dependencies."
- "Explain this finding and tell me whether it can be fixed or must be justified."
- "Start the scans again and tell me when they finish."
- "Draft a justification for this finding so I can edit it."

A good order: `get_security_posture` to see what is blocked, `plan_fixes` for one build, fix the code, `start_scan`, then `get_application_progress` until the scans finish. Justify a finding only when it cannot be fixed.

## Tools

The tools your client sees depend on your roles and the access you granted when you signed in.

### Reading

| Tool | What it does |
|---|---|
| `whoami` | Who you are signed in as |
| `get_security_posture` | The newest scan of every container: verdict, failing gates, why it is blocked, next action |
| `plan_fixes` | An ordered plan to fix one build |
| `list_applications`, `get_application` | Your applications, their components and sources |
| `list_intake_applications` | Applications Attest provisioned for you |
| `get_application_progress` | The newest build and scans, step by step |
| `get_application_config` | The cDSO configuration Attest commits |
| `get_dockerfile_fix` | Your Dockerfile and Attest's corrected one |
| `list_authorization_candidates` | Versions that can be sent toward authorization |
| `get_github`, `list_github_repositories` | Whether GitHub is connected, and the repositories it reaches |
| `list_justifications` | Justifications, active and withdrawn |
| `console_link` | The console page for work done only in the console |
| `list_scans`, `find_scans`, `get_scan` | Scans, their steps, gates and verdict |
| `list_findings`, `get_finding`, `get_finding_change` | Findings and how to fix a vulnerable package |
| `explain_finding`, `draft_justification` | Explainable AI on a finding, and a draft justification |
| `explain_gate`, `explain_gate_controls`, `explain_job`, `explain_assessment` | Explainable AI on gates, their controls, failed jobs and a scan's impact assessment |
| `list_scan_runs`, `list_scan_jobs`, `get_scan_job_log` | Pipeline runs, jobs and logs |
| `list_base_images`, `list_base_image_tags` | Approved base images and their tags |
| `get_scan_evidence`, `get_scan_artifact`, `get_scan_source` | Scanner records, published files, and files of the scanned repository |
| `get_decision`, `get_review`, `get_review_summary` | Decisions and the assessor's review |

### Changing

| Tool | What it does |
|---|---|
| `start_scan` | Start the scans of an application |
| `choose_dockerfile`, `use_fixed_dockerfile` | Pick the Dockerfile, or build with Attest's corrected one |
| `add_github_repository` | Add a GitHub repository as an application |
| `sync_application` | Bring an application up to its repository's newest commits |
| `retry_provisioning` | Resume a failed provisioning |
| `request_authorization` | Tag a version and send it toward review |
| `propose_justification`, `withdraw_justification`, `apply_justifications` | Justify a finding, withdraw a justification, commit them again |
| `remove_application` | Remove an application (your client asks you first) |

The server also offers two prompts, `fix_blocking_findings` and `security_posture_report`, and two resources: the API contract and a reference of scanners, gates and terms.

## Security

- Repository read tokens are never tool arguments, so they never pass through a model. You enter them only in the console.
- Tokens are checked on every call against your organization and roles.
- Privacy: https://ulapcybo.app/privacy

## Support

Questions and access requests: https://ulapcybo.app
