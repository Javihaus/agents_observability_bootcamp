# Chapter 1 Homework: Agentic System Audit

## Objective

Apply the systematic diagnosis framework to a real or simulated agentic system, identifying failure modes and proposing remediation strategies.

## Assignment Options

**Option A (Recommended)**: Audit your own agentic system
**Option B**: Audit the provided case study (see notebook)
**Option C**: Design and audit a hypothetical system

## Deliverables

Submit a completed Jupyter notebook containing:

1. **System description** (200-300 words)
   - What does the system do?
   - How many agents?
   - What LLMs are used?
   - Production or experimental?

2. **Failure mode identification** (choose 2-3)
   - Temporal coordination failures
   - Cost explosion patterns
   - Prompt brittleness
   - Multi-agent coordination issues

3. **Evidence collection** (per failure mode)
   - Reproduce the failure
   - Document inputs/outputs
   - Measure performance impact

4. **Root cause analysis** (per failure mode)
   - Apply diagnosis decision tree
   - Classify failure type
   - Identify architectural vs implementation issue

5. **Remediation proposal** (per failure mode)
   - Immediate mitigation (if possible)
   - Long-term solution approach
   - Estimated implementation effort

## Evaluation Criteria

Your homework will be evaluated on:

- **Depth of analysis** (40%): How thoroughly did you diagnose root causes?
- **Evidence quality** (30%): Did you provide concrete examples and measurements?
- **Remediation realism** (20%): Are proposed solutions practical and achievable?
- **Presentation clarity** (10%): Is the notebook well-organized and readable?

## Submission

- **Format**: Completed `homework_notebook.ipynb`
- **Deadline**: [TO BE FILLED BY INSTRUCTOR]
- **Method**: Email to javier@jmarin.info or GitHub PR
- **File name**: `chapter1_homework_[YOUR_NAME].ipynb`

## Time Estimate

- System description: 30 minutes
- Failure mode identification: 1 hour
- Evidence collection: 1-2 hours (depending on system complexity)
- Analysis and documentation: 1 hour
- **Total**: 3-4 hours

## Tips

1. Start with the simplest failure mode you observe
2. Don't try to fix everything—diagnosis is the goal
3. Use the decision tree from Section 6 of Chapter 1 content
4. Include screenshots or logs as evidence
5. Be honest about limitations—"I don't know" is acceptable

## Getting Help

If stuck:
- Review [chapter_01_diagnosing_agent_failures/content.md](../content.md)
- Check [example notebooks](../examples/) for patterns
- Post questions in GitHub Discussions
- Email instructor: javier@jmarin.info

---

**Remember**: The goal is diagnosis, not fixing. You'll learn remediation techniques in Chapters 2-4.

## Case Study (Option B)

If you don't have your own agentic system, use this case study:

### E-Commerce Order Processing System

**Overview**:
- Multi-agent system for processing customer orders
- 4 agents: Order Validator, Inventory Checker, Payment Processor, Shipping Coordinator
- Built with LangChain + Claude Sonnet 4
- Deployed 3 months ago, serving 500 orders/day

**Observed Issues**:

1. **Inconsistent handling of rush orders**
   - Standard orders: 95% success rate
   - Rush orders (same-day delivery): 60% success rate
   - Many rush orders miss cutoff times despite feasibility

2. **High API costs**
   - Budget: $500/month
   - Actual: $1,800/month
   - Investigating where costs come from

3. **Format sensitivity**
   - System accepts orders via API (JSON format)
   - Internal testing used conversational format
   - Performance dropped after production launch
   - Some valid orders rejected for "formatting errors"

4. **Coordination problems**
   - Payment processed before inventory confirmed
   - Resulted in refunds when items out of stock
   - Shipping agent sometimes overrides inventory agent

**Your task**: Diagnose 2-3 of these issues using Chapter 1 framework.

### Sample Order (for testing)

```json
{
  "order_id": "ORD-12345",
  "customer_id": "CUST-98765",
  "items": [
    {"sku": "WIDGET-A", "quantity": 2, "price": 29.99},
    {"sku": "GADGET-B", "quantity": 1, "price": 149.99}
  ],
  "delivery_type": "rush",
  "delivery_deadline": "2024-01-15 17:00:00",
  "order_time": "2024-01-15 14:30:00",
  "payment_method": "credit_card"
}
```

### Available Data

For the case study, you can simulate:
- Agent conversations (use Claude with appropriate prompts)
- Token usage estimates
- Success/failure patterns

You do NOT need:
- Real production logs (simulate realistic scenarios)
- Actual cost data (estimate based on token counts)
- Complete system implementation (focus on diagnosis)

## Submission Checklist

Before submitting, verify:

- [ ] System description is clear and complete
- [ ] At least 2 failure modes identified and analyzed
- [ ] Evidence provided for each failure (examples, measurements)
- [ ] Root cause analysis uses Chapter 1 framework
- [ ] Remediation proposals are specific and actionable
- [ ] Notebook runs without errors (if code included)
- [ ] All sections clearly labeled with markdown headers
- [ ] Name and contact info included at top of notebook
- [ ] File named correctly: `chapter1_homework_[YOUR_NAME].ipynb`

## Academic Integrity

This is an individual assignment. You may:
- Discuss general concepts with classmates
- Ask instructor for clarification
- Reference course materials and examples

You may NOT:
- Copy another student's analysis
- Use AI tools to generate your diagnosis (though you can use them to simulate agent behavior)
- Submit work that is not your own

Violations will result in a zero grade for this assignment.

---

**Questions?** Email javier@jmarin.info with subject line: "Chapter 1 Homework Question"

Good luck!
