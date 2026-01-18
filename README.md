# Claude Code AI Governance Framework

> How a credit risk model validator built production-grade AI governance into his personal development workflow using Claude Code custom agents.

## The Problem

AI tools are powerful but ungoverned. Personal use becomes production code. There are no audit trails. Standards drift.

After 5 years building models in a highly regulated environment, I asked myself: **If I can't govern my own AI use, how can I help a company govern theirs?**

## The Solution

I built a personal AI governance framework using Claude Code with:

- **Hooks** — Guaranteed execution that runs automatically on every tool call
- **6 specialized agents** with clear triggers and responsibilities
- **7 workflow commands** for repeatable, validated processes
- **Guardrails** that enforce production-grade discipline
- **Context management** via CLAUDE.md files across all projects

This isn't theoretical—I used this framework to build four production-grade projects with 86%+ test coverage, verified metrics, and zero untracked changes.

**The key insight:** Agents and CLAUDE.md are suggestions the AI *might* follow. Hooks are guarantees that *will* execute. Real governance requires both.

## Specialized Agents

| Agent | Purpose | Trigger |
|-------|---------|---------|
| **compliance-checker** | Validates code meets regulatory documentation standards | Before commits, after major changes |
| **code-reviewer** | Reviews for quality, security, production readiness | After completing features |
| **debugger** | Systematic error diagnosis and resolution | When tests fail or errors occur |
| **learning-coach** | Tests understanding, identifies knowledge gaps | During skill development |
| **planner** | Breaks complex tasks into actionable steps | Before starting multi-step work |

### Why Specialized Agents?

General-purpose AI is like having one employee do everything. Specialized agents are like having a team with clear roles:

- **Predictable behavior** — Each agent has defined responsibilities
- **Better outputs** — Agents optimized for specific tasks outperform generalists
- **Auditable decisions** — Clear ownership of recommendations
- **Composable workflows** — Agents can be chained for complex tasks

## The Compliance-Checker Agent

This is my most unique contribution. It applies SR 11-7 model validation principles to code:

```markdown
You are a model risk compliance expert.

Check that:
- Changes are documented with rationale
- Versions are tracked (dependencies pinned, git commits meaningful)
- Tests exist for all public functions
- Outputs are reproducible (random seeds set, deterministic where possible)
- There is an audit trail (logging, change history)

Flag gaps with severity levels and suggest specific fixes.
```

**Why this matters:** In regulated industries, "it works" isn't enough. You need to prove it works, explain why, and trace every decision. This agent embeds that thinking into daily development.

## Hooks: Guaranteed Execution

This is the key differentiator. Agents advise. CLAUDE.md instructs. **Hooks enforce.**

Hooks are shell commands that execute automatically before or after every tool call. The AI doesn't decide whether to run them—they just run. No human memory required. No hoping the AI follows instructions.

### My Production Hooks

**1. Access Control (PreToolUse: Edit|Write)**

Blocks writes to sensitive files:

```python
# Blocks any edit to files containing .env, secrets, .git/, credentials
sys.exit(2 if any(p in path for p in ['.env', 'secrets', '.git/', 'credentials']) else 0)
```

*Tested: Asked Claude to create a .env file. Blocked with error message.*

**2. Dangerous Command Prevention (PreToolUse: Bash)**

Blocks destructive operations:

```python
# Blocks rm -rf, git push, writes to /dev
```

*The AI can suggest these commands, but cannot execute them.*

**3. Audit Trail (PostToolUse: Edit|Write)**

Logs every file change:

```
2026-01-18T08:03:44.469777 | C:\Projects\test-hook.txt
```

*Every file the AI touches gets timestamped and logged. Complete audit trail.*

### Why Hooks Matter for Governance

| Control Type | Implementation | SR 11-7 Mapping |
|-------------|----------------|-----------------|
| Access Control | PreToolUse blocks sensitive paths | Segregation of duties |
| Validation Gates | PreToolUse checks before execution | Independent validation |
| Audit Trail | PostToolUse logs all changes | Change management |

**The regulatory translation:** Hooks implement what SR 11-7 calls "effective challenge"—independent controls that operate regardless of the primary actor's intentions.

### Suggestions vs. Guarantees

| Layer | Type | Enforcement |
|-------|------|-------------|
| CLAUDE.md | Suggestions | AI might forget |
| Agents | Recommendations | AI might ignore |
| Guardrails | Instructions | AI might skip |
| **Hooks** | **Guarantees** | **Always executes** |

This is the difference between governance theater and real controls.

## Guardrails

These rules are embedded in my global configuration:

```markdown
## Guardrails
- NEVER commit or push to GitHub without explicit approval
- NEVER make up performance metrics, ROI, or time savings
- ALWAYS show planned changes before executing
- If installing packages, ask first
- Stay in project directory - don't wander
```

**The philosophy:** Guardrails aren't restrictions—they're trust builders. Every "NEVER" and "ALWAYS" exists because the opposite would create ungoverned behavior.

**Note:** Guardrails rely on the AI following instructions. Hooks ensure critical controls execute regardless.

## Context Management

Every project has a `CLAUDE.md` file in its root:

```markdown
## Project: [Name]
One-line description

## Tech Stack
- Languages, frameworks, key dependencies

## What's Implemented
- Feature 1 (status)
- Feature 2 (status)

## What's Planned
- Upcoming feature

## Key Files
- `src/main.py` - Entry point
- `tests/` - Test suite

## Project-Specific Rules
- Any constraints unique to this project
```

**Why this works:** AI assistants need context. Instead of re-explaining every session, the CLAUDE.md file provides instant orientation. It's documentation that actually gets used.

## Real-World Results

Projects built using this framework:

| Project | Metrics | Governance Applied |
|---------|---------|-------------------|
| **AutoDoc AI** | 100% source fidelity, $328K-592K estimated value | RAG with citation trails |
| **CreditNLP** | 60% → 93.9% accuracy improvement | Reproducible training, versioned models |
| **EvalOps** | 204 tests, 86% coverage | compliance-checker on every commit |
| **MCP Banking** | 950+ lines, 10 production tools | code-reviewer for security checks |

## The Bridge Builder Positioning

I'm transitioning from credit risk model development to AI orchestration. This framework demonstrates:

1. **I understand governance** — Not as a checkbox, but as a design principle
2. **I can build production systems** — Four projects with real metrics
3. **I bridge both worlds** — Technical enough for engineers, governed enough for compliance

This is what "bridge between AI and regulated industries" looks like in practice.

## Example Agent Configurations

### Compliance-Checker

See [examples/agents/compliance-checker.md](examples/agents/compliance-checker.md)

Validates code against regulatory documentation standards. Checks for reproducibility, audit trails, version tracking, and test coverage.

### Code-Reviewer

See [examples/agents/code-reviewer.md](examples/agents/code-reviewer.md)

Reviews code for production readiness: type hints, error handling, security considerations, and maintainability.

## Try It Yourself

1. Install [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
2. **Configure hooks first** — Start with an audit trail hook (PostToolUse logging). Add access controls for sensitive paths.
3. Create a global `CLAUDE.md` with your standards and guardrails
4. Add project-level `CLAUDE.md` files for context
5. Configure specialized agents for your workflow
6. Start with one agent (I recommend compliance-checker) and expand

**Start with hooks.** Everything else is helpful. Hooks are essential.

## What I Learned

1. **Suggestions aren't governance** — CLAUDE.md and agents are helpful, but the AI can ignore them. Real governance requires hooks that execute regardless of AI compliance.

2. **Hooks map directly to regulatory controls** — Access controls, validation gates, audit trails. The same concepts from SR 11-7 implemented as code.

3. **Governance doesn't slow you down** — It makes you confident. I ship faster knowing every commit is validated and every change is logged.

4. **Specialization beats generalization** — Six focused agents outperform one trying to do everything.

5. **Production mindset starts at home** — If my personal projects aren't governed, why would enterprise projects be?

## Connect

I'm seeking AI orchestration and governance roles where I can help organizations adopt AI safely.

- **Background:** 8 years SR 11-7 model validation, PhD in Public Policy
- **Focus:** Bridge between AI innovation and regulatory compliance
- **Open to:** AI governance, model risk, ML platform, AI orchestration roles

---

*Built with Claude Code. Governed with hooks. Real controls, not governance theater.*
