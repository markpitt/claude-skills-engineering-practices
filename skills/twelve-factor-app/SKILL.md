---
name: twelve-factor-app
description: 'Applies the Twelve-Factor App methodology when designing, building, reviewing or auditing cloud-native and SaaS applications. Use when asked about 12-factor compliance, configuration in the environment, stateless processes, build, release and run separation, backing services, port binding, scaling by process, graceful shutdown, dev and prod parity, logs as event streams, or one-off admin tasks. Do NOT use for choosing a cloud provider, writing Kubernetes or Terraform manifests with no 12-factor question, or general code review.'
license: Apache-2.0
metadata:
  source: https://12factor.net/
  author: Adam Wiggins
  version: 1.1.0
  tags: [architecture, cloud-native, saas, devops, best-practices]
---

# Twelve-Factor App

Twelve rules for building services that deploy cleanly to any platform and scale by adding processes. Adam Wiggins and the Heroku team wrote them in 2011. Since November 2024 the text at 12factor.net has been an open-source community project, and the maintainers are revising it, so check the site when a factor's current wording matters.

The factors apply in any language and with any mix of backing services. They are a checklist of failure modes more than a design method: each one names a way an app becomes hard to deploy, scale or debug.

## When to Load Which Resource

| Task / Topic | Load Resource | Key Factors |
|---|---|---|
| Source control, versioning, or package management | `resources/codebase-and-dependencies.md` | I. Codebase, II. Dependencies |
| Environment variables, secrets, or external service connections | `resources/config-and-backing-services.md` | III. Config, IV. Backing Services |
| CI/CD pipelines, release management, or deployment separation | `resources/build-release-run.md` | V. Build, Release, Run |
| Stateless design, port binding, or horizontal scaling | `resources/processes-and-concurrency.md` | VI. Processes, VII. Port Binding, VIII. Concurrency |
| Startup time, graceful shutdown, or environment parity | `resources/disposability-and-parity.md` | IX. Disposability, X. Dev/Prod Parity |
| Logging strategy or administrative/management tasks | `resources/logs-and-admin.md` | XI. Logs, XII. Admin Processes |
| Full audit, compliance check, or new app setup | `resources/implementation-guide.md` | All 12 factors |

## Quick Reference: The 12 Factors

| # | Factor | Principle |
|---|---|---|
| I | Codebase | One codebase in version control, many deploys |
| II | Dependencies | Explicitly declare and isolate all dependencies |
| III | Config | Store config in the environment, not in code |
| IV | Backing Services | Treat databases, queues, etc. as attached resources |
| V | Build, Release, Run | Strictly separate build and run stages |
| VI | Processes | Execute as stateless, share-nothing processes |
| VII | Port Binding | Export services via port binding, not server injection |
| VIII | Concurrency | Scale out via the process model |
| IX | Disposability | Fast startup and graceful shutdown |
| X | Dev/Prod Parity | Keep all environments as similar as possible |
| XI | Logs | Treat logs as event streams written to stdout |
| XII | Admin Processes | Run admin tasks as one-off processes |

## Working through a question

### Find the factor

Most questions map to one or two factors:

- **Structuring code or repos** → Factor I (Codebase)
- **Library versions or system tools** → Factor II (Dependencies)
- **Secrets, credentials, or deploy-specific values** → Factor III (Config)
- **Database, cache, queue, or third-party API** → Factor IV (Backing Services)
- **Deploy pipeline, release artifacts, rollback** → Factor V (Build, Release, Run)
- **Session state, filesystem writes, or in-memory caches** → Factor VI (Processes)
- **Embedding a web server vs. self-hosting** → Factor VII (Port Binding)
- **Scaling, worker types, or process formation** → Factor VIII (Concurrency)
- **Restart speed, SIGTERM handling, idempotent jobs** → Factor IX (Disposability)
- **SQLite in dev / Postgres in prod, or parity gaps** → Factor X (Dev/Prod Parity)
- **Log files, log aggregation, or structured logging** → Factor XI (Logs)
- **Migrations, one-off scripts, or REPL access** → Factor XII (Admin Processes)

### Apply it

Load the resource for that factor, then:

1. Identify the current state against the factor's principle
2. Highlight any violations or gaps
3. Propose concrete remediation steps with examples
4. Note dependencies between factors (e.g., Factor III enables Factor IV)

### Check the result

After the changes, confirm that:
- deploy-specific config is not in code;
- nothing assumes local state survives a restart;
- the pipeline has separate build, release and run stages;
- logs go to stdout, not files.

## Common Task Workflows

### Auditing an Existing App for 12-Factor Compliance

1. Load `resources/implementation-guide.md` for the full compliance checklist
2. Work through each factor systematically using the checklist
3. For deep dives on violations, load the relevant resource file
4. Produce a prioritised remediation plan (high-impact violations first)

### Designing a New Cloud-Native Service

1. Start with `resources/codebase-and-dependencies.md` to establish repo and dependency strategy
2. Review `resources/config-and-backing-services.md` to design config injection and service binding
3. Review `resources/build-release-run.md` to plan the CI/CD pipeline
4. Review `resources/processes-and-concurrency.md` to design stateless process architecture
5. Use `resources/implementation-guide.md` for language-specific examples

### Reviewing Config / Secrets Management

1. Load `resources/config-and-backing-services.md`
2. Verify all environment-specific values use environment variables
3. Check that no credentials are hardcoded or in config files checked into source control
4. Confirm backing services are treated as attached resources (swappable via config)

### Fixing a Scaling Problem

1. Load `resources/processes-and-concurrency.md`
2. Verify processes are stateless and share-nothing
3. Check for sticky sessions, local filesystem state, or in-process caches
4. Review process types (web, worker) and confirm horizontal scale-out is possible

## Resource File Summaries

- **codebase-and-dependencies.md**: Factors I & II: version control strategy, one-repo-per-app rule, dependency declaration manifests, and isolation tooling
- **config-and-backing-services.md**: Factors III & IV: environment variable patterns, secrets management, treating all external services as attached resources
- **build-release-run.md**: Factor V: separating build artifacts from configuration, immutable releases, rollback strategies, CI/CD pipeline design
- **processes-and-concurrency.md**: Factors VI, VII & VIII: stateless process design, port binding, process types, horizontal scaling, the Unix process model
- **disposability-and-parity.md**: Factors IX & X: fast startup, graceful SIGTERM handling, idempotent jobs, eliminating dev/prod gaps in tools and data
- **logs-and-admin.md**: Factors XI & XII: stdout-based logging, log routing, structured logging, running migrations and one-off tasks as isolated processes
- **implementation-guide.md**: Full compliance checklist for all 12 factors with practical examples across Python, Node.js, Ruby, and Java

## Best Practices Summary

- **Config litmus test**: Would open-sourcing the codebase expose any secrets? If yes, Factor III is violated.
- **Process litmus test**: Can any process be killed and restarted without data loss? If no, Factor VI or IX is violated.
- **Parity litmus test**: Does the local dev stack use the same type of backing service as production? If no, Factor X is violated.
- Factors are interdependent: Factor III (Config) enables Factor IV (Backing Services swappability). Factor VI (Processes) enables Factor VIII (Concurrency) and IX (Disposability).

## External References

- [12factor.net](https://12factor.net/), the methodology itself
- [Beyond the Twelve-Factor App](https://www.oreilly.com/library/view/beyond-the-twelve-factor/9781492042631/), Kevin Hoffman's 2016 extension to fifteen factors
- [Twelve-Factor is now open source](https://12factor.net/blog/open-source-announcement), the November 2024 announcement and how to contribute
