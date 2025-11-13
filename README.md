# Agentic AI Systems Masterclass: From LLM Chaos to Deterministic Workflows

A hands-on course teaching practitioners how to debug, monitor, and optimize multi-agent AI systems in production environments.

## Course Overview

Companies deploying agentic AI systems face systematic challenges: unpredictable costs, inexplicable failures, and regulatory compliance requirements. This masterclass provides practical frameworks for diagnosing agent failures, implementing observability infrastructure, and building hybrid architectures that combine LLM flexibility with deterministic reliability.

### What You Will Learn

**Chapter 1: Diagnosing Agent Failures**
- Identify temporal coordination breakdowns in multi-agent systems
- Diagnose cost explosion patterns and API waste
- Understand prompt brittleness and failure modes

**Chapter 2: Observability & Telemetry**
- Implement LangChain callbacks and tracing
- Build cost tracking dashboards
- Detect failure patterns automatically

**Chapter 3: Crystallizing Deterministic Workflows**
- Extract neural patterns into symbolic code
- Achieve 20x cost reductions through workflow optimization
- Decide when to use LLMs vs programmatic logic

**Chapter 4: Production-Ready Hybrid Systems**
- Design hybrid architectures (LLM + rules)
- Build validation rubrics for continuous improvement
- Prepare systems for EU AI Act compliance

## Prerequisites

### Technical Requirements
- Basic Python programming (functions, classes, dictionaries)
- Familiarity with Jupyter notebooks
- Understanding of API concepts

### Required Tools
- Google Colab account (free tier sufficient)
- Claude API key from Anthropic
- GitHub account (to access course materials)

### Expected Time Commitment
- Live sessions: 6 hours (3 days x 2 hours)
- Homework: 2-3 hours per chapter
- Final project: 4-6 hours

## Course Schedule

Total duration: 6 hours across 3 days

**Day 1 (2 hours)**
- Chapter 1: Diagnosing Agent Failures
- Chapter 2: Observability & Telemetry

**Day 2 (2 hours)**
- Chapter 3: Crystallizing Deterministic Workflows
- Chapter 4: Production-Ready Hybrid Systems

**Day 3 (2 hours)**
- Advanced examples and final project discussion
- Open Q&A and office hours

See [CALENDAR.md](CALENDAR.md) for specific dates and times.

## Setup Instructions

Before the first session, complete all setup steps:

1. **Read Prerequisites**: [PREREQUISITES.md](PREREQUISITES.md)
2. **Get Claude API Key**: [setup/01_claude_api_setup.md](setup/01_claude_api_setup.md)
3. **Install LangChain**: [setup/02_langchain_installation.md](setup/02_langchain_installation.md)
4. **Configure Colab**: [setup/03_colab_configuration.md](setup/03_colab_configuration.md)
5. **Verify Setup**: Run [setup/04_verify_setup.ipynb](setup/04_verify_setup.ipynb)

## Repository Structure
```
├── setup/                    # Pre-course setup guides
├── chapter_01_*/            # Course chapters with theory and examples
├── chapter_02_*/
├── chapter_03_*/
├── chapter_04_*/
├── final_project/           # Final project requirements and examples
├── resources/               # Reference materials and templates
└── docs/                    # Additional presentations and materials
```

## Using This Repository

### For Live Sessions
During live sessions, the instructor will demonstrate examples using their own Claude API key. Students follow along without needing their API key active.

### For Homework and Practice
Students use their own Claude API key to complete homework assignments and experiments. See [setup/03_colab_configuration.md](setup/03_colab_configuration.md) for instructions on managing API keys securely in Google Colab.

### Google Colab Integration
All notebooks are designed for Google Colab. To open any notebook in Colab:

1. Navigate to the notebook file on GitHub
2. Copy the URL
3. Go to https://colab.research.google.com
4. Select "GitHub" tab and paste the URL

Alternatively, click the "Open in Colab" badge at the top of each notebook.

## Research Foundation

This course is based on peer-reviewed research and production experience:

- Marin, J. (2025). "Empirical Characterization of Temporal Constraint Processing in LLMs" - [arXiv:2411.XXXXX](https://arxiv.org)
- Marin, J. (2024). "The Compliance-Safety Tradeoff in Medical AI Systems" - [Paper Link](https://arxiv.org)
- Marin, J. (2024). "Neural-to-Symbolic Program Extraction Framework" - [Paper Link](https://arxiv.org)

Complete reading list: [resources/reading_list.md](resources/reading_list.md)

## Final Project

Students will audit and redesign an existing agentic system (either their own production system or a provided case study). The final project demonstrates:

- Systematic diagnosis of failure modes
- Implementation of observability infrastructure
- Cost optimization through workflow crystallization
- Compliance-ready documentation

See [final_project/project_brief.md](final_project/project_brief.md) for complete requirements.

## About the Instructor

**Javier Marin** is an independent AI systems consultant specializing in production ML deployment and EU AI Act compliance. He has published research in IEEE DSAA on Hamiltonian neural networks, conducted safety research on LLM medical triage systems, and advised venture-backed startups on ML implementation.

Areas of expertise:
- Production LLM observability and debugging
- Multi-agent system coordination
- EU AI Act compliance for high-risk AI systems
- Physics-inspired optimization methods

Contact: javier@jmarin.info

## License

This course content is licensed under the Apache License 2.0. See [LICENSE](LICENSE) for details.

## Support

For technical questions during the course:
- Open an issue in this repository
- Email: javier@jmarin.info
- Live Q&A during scheduled sessions

---

**Note**: This is a practical, hands-on course. Come prepared to write code, debug real systems, and engage with production-scale problems.
