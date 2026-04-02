# ADI ATP Plugin

Claude Code plugin for the [Agentic Transaction Platform](https://atp.bigsmallai.com). Connects your agent to ATP's specialized analysis infrastructure for Vietnam crypto exchanges.

## Install

```
/plugin marketplace add s27183/adi-atp-plugin
/plugin install atp
```

## Setup

1. **Sign up** at [atp.bigsmallai.com](https://atp.bigsmallai.com)
2. **Authenticate** — run `/mcp` in Claude Code, select Authenticate for ATP. Browser opens for approval.
3. **Use skills** — described below.

## Skills

| Skill | What it does |
|-------|-------------|
| `/design-feature` | Design a compliant exchange feature with cross-domain trade-offs |
| `/spec-feature` | Generate implementation specs from a design decision |
| `/review-compliance` | Assess whether an activity is defensibly compliant |
| `/review-security` | Review security posture across governance domains |
| `/attack-composition` | Find compound vulnerabilities at domain boundaries |
| `/compare-systems` | Compare systems on governance maturity |
| `/design-protocol` | Evaluate protocol choices with cross-domain consequences |

## What happens when you run a skill

1. ATP generates context questions specific to your request
2. You answer — audience, market, constraints
3. Evidence gathered from curated legal, security, and transactional data
4. Web search supplements internal evidence
5. Evaluation agents produce multi-dimensional analysis
6. Markdown + HTML report delivered to your workspace

## Requirements

- Claude Code or Claude Desktop
- ATP account ([sign up](https://atp.bigsmallai.com))
- No API key needed — authentication via OAuth 2.1

## Links

- [ATP Platform](https://atp.bigsmallai.com)
- [Setup Guide](https://atp.bigsmallai.com/setup)
- [BigSmall AI](https://bigsmallai.com)
