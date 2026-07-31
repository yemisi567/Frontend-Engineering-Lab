# Frontend Engineering Lab

> A community-driven collection of frontend engineering knowledge, patterns, architecture decisions, performance techniques, testing strategies, real-world lessons from building production software etc

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Contributions](https://img.shields.io/badge/contributions-open%20to%20everyone-orange.svg)](CONTRIBUTING.md)
[![Code of Conduct](https://img.shields.io/badge/code%20of%20conduct-contributor%20covenant-purple.svg)](CODE_OF_CONDUCT.md)

**Frontend Engineering Lab is an open-source initiative.** The long-term goal is to create one of the most practical collections of frontend engineering knowledge on GitHub, built collaboratively by engineers across different experience levels and backgrounds.

This repository is designed for engineers who want to move beyond tutorials and explore how frontend systems are **designed, maintained, and scaled** in real-world environments.

---

## Why This Exists

Most frontend content online teaches you *how to use a tool*. Very little teaches you *how to think like an engineer*:

- Why did the team choose this architecture?
- What broke at scale, and how was it fixed?
- What tradeoffs were made, and were they worth it?

That knowledge usually lives in private Slack threads, internal docs, and the heads of senior engineers. **This repo is where we write it down, together.**

## What You'll Find Here

| Section | What's Inside |
|---|---|
| [`react/`](react/) | React patterns, anti-patterns, and best practices |
| [`next/`](next/) | Next patterns, anti-patterns, and best practices |
| [`typescript/`](typescript/) | TypeScript techniques, type design, and gotchas |
| [`architecture/`](architecture/) | Frontend architecture, project structure, and design decisions |
| [`performance/`](performance/) | Performance optimization strategies and measurement |
| [`testing/`](testing/) | Testing approaches — unit, integration, E2E, and what to test |
| [`accessibility/`](accessibility/) | Building inclusive, accessible interfaces |
| [`state-management/`](state-management/) | State management patterns and when to use what |
| [`frontend-system-design/`](frontend-system-design/) | Designing frontend systems at scale |
| [`case-studies/`](case-studies/) | Real-world stories: incidents, migrations, refactors, lessons learned |
| [`interview-prep/`](interview-prep/) | Frontend interview preparation grounded in real engineering |

> **Don't see your topic?** The sections above are a starting point, not a limit. You can propose a new top-level topic (e.g. `animations/`, `security/`, `build-tooling/`), it just has to follow the same structure as the existing ones. See [Proposing a New Top-Level Topic](CONTRIBUTING.md#proposing-a-new-top-level-topic).

## Who Can Contribute?

**Anyone.**

- **Junior developers** => share something you recently learned. Explaining it helps you *and* the next person.
- **Mid-level engineers** => document a solution you shipped, a bug you debugged, a pattern you adopted.
- **Senior engineers** => share architecture decisions, tradeoff analyses, and war stories from production.

The goal is to build a high-quality knowledge base that helps frontend engineers learn from each other's experiences. Your first open-source contribution is welcome here, this is a friendly place to start.

## Contribution Principles

We value:

- **Practical examples** over theory
- **Real-world experience** over hype
- **Tradeoffs** over absolutes
- **Clear explanations** over jargon
- **Learning in public**

## Getting Started

1. **Fork** the repository
2. **Create a branch** `git checkout -b add/react-error-boundaries`
3. **Copy a template** from [`templates/`](templates/) and write your contribution
4. **Open a Pull Request**

All contributions follow a consistent structure so the knowledge base stays high-quality and easy to navigate. **Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting**, it takes 3 minutes and explains the article format, naming conventions, and review process.

### Not sure what to write?

- Check the [open issues](../../issues) for requested topics
- Open a [content proposal](../../issues/new/choose) to discuss an idea first
- Look at the article template in [`templates/article-template.md`](templates/article-template.md) for inspiration

## Repository Structure

```text
frontend-engineering-lab/
├── react/
├── next/
├── typescript/
├── architecture/
├── performance/
├── testing/
├── accessibility/
├── state-management/
├── frontend-system-design/
├── case-studies/
├── interview-prep/
├── <your-new-topic>/       ← propose new sections via an issue
├── templates/              ← copy these to start writing
├── CONTRIBUTING.md         ← read this before your first PR
├── CODE_OF_CONDUCT.md
└── LICENSE
```

## Support the Project

If you find this repository useful:

- **Star it** => it helps other engineers discover it
- **Share it** with your team or on social media
- **Contribute** => even fixing a typo counts

## License

This project is licensed under the [MIT License](LICENSE).

---

*Built collaboratively, in public, by frontend engineers everywhere.*
