# Chapter 1: Diagnosing Agent Failures

## Learning Objectives

By the end of this chapter, you will be able to:

1. Identify temporal coordination failures in multi-agent systems
2. Diagnose cost explosion patterns caused by inefficient agent designs
3. Understand prompt brittleness and its impact on production reliability
4. Apply systematic diagnosis frameworks to real agentic systems

**Duration**: 60 minutes (35 min theory + 25 min examples)

---

## Prerequisites

Before starting this chapter:

- Complete [setup/04_verify_setup.ipynb](../setup/04_verify_setup.ipynb)
- Read research paper: "Empirical Characterization of Temporal Constraint Processing in LLMs"
- Basic understanding of LLM agents (what they are, how they work)

---

## 1. Introduction: Why Agents Fail in Production

### The Demo-to-Production Gap

Agentic AI systems often demonstrate impressive capabilities in controlled demonstrations but fail systematically when deployed in production environments. This gap emerges from fundamental architectural constraints, not implementation details.

**Key insight**: Multi-agent systems fail in predictable ways that can be diagnosed systematically.

### What We Mean by "Failure"

In this course, failure means:

1. **Performance degradation**: System accuracy drops below acceptable thresholds
2. **Cost explosion**: API expenses exceed budgeted amounts
3. **Coordination breakdown**: Agents produce conflicting or redundant outputs
4. **Temporal failures**: Systems miss deadlines or make time-inappropriate decisions

**Not covered**: Security vulnerabilities, data privacy issues, infrastructure failures (these are important but outside scope).

---

## Tips

**Teaching strategy**: Start with a real production failure story from your experience. Students remember concrete examples better than abstract theory.

**Common misconception**: Students often think failures are "bugs" they can fix with better prompts. Emphasize that many failures are architectural and require redesign, not prompt engineering.

**Time management**: Theory section runs long if you take all questions. Save detailed discussions for examples section.

---

## 2. Temporal Coordination Failures

### The Problem

Multi-agent systems must coordinate actions across time. When one agent's output depends on timing (urgency, deadlines, windows of opportunity), systems often fail to propagate temporal constraints correctly.

**Research foundation**: Based on "Empirical Characterization of Temporal Constraint Processing in LLMs" (Marin, 2025).

### Why LLMs Struggle with Time

Large language models process temporal information as discrete tokens in sequences. They see "token 1, token 2, token 3" but lack:

1. **Continuous temporal state representation**: Cannot maintain evolving time tracking
2. **Explicit constraint checking**: No mechanism separate from pattern matching
3. **Compositional temporal reasoning**: Struggle to combine multiple time constraints

Mathematical formulation:

Let $T$ be a temporal constraint (e.g., deadline), and $A(t)$ be an action proposed at time $t$. The agent must evaluate:

$$\text{viable}(A, t, T) = \begin{cases}
\text{true} & \text{if } t + \text{duration}(A) \leq T \\
\text{false} & \text{otherwise}
\end{cases}$$

**Current LLMs**: Perform this check via pattern matching over training data, not explicit computation.

**Result**: High error rates when patterns don't match training distribution.

### Empirical Evidence

Experimental results from 8 production-scale models (2.8-8B parameters):

| Failure Mode | Accuracy Drop | Explanation |
|--------------|---------------|-------------|
| Explicit urgency markers | 95%+ | Pattern matching succeeds |
| Implicit urgency (must infer) | 65-70% | 30pp drop from pattern failure |
| Novel scenarios | 40-50% | Further 20pp drop |
| Multi-agent coordination under time pressure | 25-40% success | Systematic breakdown |

**Key finding**: Models achieve either >95% or <50% accuracy (bimodal distribution), with prompt formatting changes causing 30-60 percentage point swings.

### Real-World Example: Medical Triage

Emergency medical triage requires agents to:
- Assess patient urgency from symptoms
- Coordinate care across multiple specialists
- Respect treatment windows (e.g., stroke intervention within 4.5 hours)

**Observed failure**: Agent recommends treatment that requires 6-hour procedure when patient has 3 hours until critical window closes.

**Root cause**: Agent pattern-matches "stroke symptoms → tPA therapy" without checking temporal viability.

---

## Tips

**Demonstration**: Show the temporal coordination failure example live. Students need to see the breakdown, not just hear about it.

**Technical depth**: This section has mathematical formulation. Gauge audience technical level and adjust detail accordingly. If students struggle, move to concrete examples quickly.

**Research reference**: Have the paper open in another tab to show figures if students want deeper detail.

---

## 3. Cost Explosion Patterns

### The Problem

Agentic systems can experience exponential cost growth when agents make redundant LLM calls or enter infinite loops.

**Typical pattern**:
1. Agent A calls LLM to generate plan
2. Plan requires information from Agent B
3. Agent B calls LLM to process request
4. Agent B needs clarification, calls Agent A
5. Agent A calls LLM again to clarify
6. Repeat until token/cost limits reached

### Cost Structure

Anthropic Claude Sonnet 4 pricing (as of course date):
- Input tokens: $3 per million tokens
- Output tokens: $15 per million tokens

**Simple agent**: Single-turn conversation
- Input: ~500 tokens
- Output: ~300 tokens
- Cost: $(500 \times 3 + 300 \times 15) / 1,000,000 = $0.006$

**Multi-agent system**: 5 agents, 3 exchanges each
- Total turns: $5 \times 3 = 15$ turns
- Cost per deployment: $15 \times 0.006 = $0.09$
- Running 1000x/day: $0.09 \times 1000 = $90/day = $2,700/month

**With redundancy**: If agents repeat failed calls 3x each:
- Actual turns: $15 \times 3 = 45$ turns
- Monthly cost: $2,700 \times 3 = $8,100/month

**Result**: 3x cost multiplier from retry logic without validation.

### Common Anti-Patterns

**Anti-pattern 1: No caching**
- System regenerates identical responses for repeated queries
- Solution: Implement semantic caching (covered in Chapter 3)

**Anti-pattern 2: Unbounded recursion**
- Agent A asks Agent B, which asks Agent C, which asks Agent A
- Solution: Depth limits and cycle detection

**Anti-pattern 3: Redundant validation**
- Every agent independently validates same information
- Solution: Shared validation state (covered in Chapter 2)

**Anti-pattern 4: Over-specified prompts**
- Including unnecessary context in every call
- Solution: Context compression and prompt optimization

### Diagnosis Framework

To identify cost drivers:

1. **Instrument every LLM call**
   - Token count (input/output)
   - Call timestamp
   - Calling agent ID
   - Cost per call

2. **Aggregate by agent**
   - Which agent consumes most tokens?
   - Which agent makes most calls?
   - Cost per agent per task

3. **Identify redundancy**
   - Detect duplicate or near-duplicate calls
   - Find retry patterns
   - Measure cache hit rate (if implemented)

4. **Calculate waste**
   - Calls that don't contribute to final output
   - Redundant information gathering
   - Failed calls that are retried

**Metric**: Waste ratio = $\frac{\text{Total cost} - \text{Minimum necessary cost}}{\text{Total cost}}$

Target: <20% waste ratio for production systems

---

## Tips

**Cost transparency**: Many students underestimate LLM costs. Show real monthly bills if you have them (redacted company info).

**Interactive exercise**: Have students calculate costs for their planned systems. Reality check often motivates optimization.

**Tool preview**: Mention that Chapter 2 builds the instrumentation to track these metrics automatically.

---

## 4. Prompt Brittleness

### The Problem

Small changes to prompt formatting cause large performance drops, even when semantic content remains identical.

**Definition**: A system is "brittle" if $\Delta$ (small input change) → $\Delta\Delta$ (large output change).

### Experimental Evidence

From temporal constraint experiments:

| Prompt Format | Accuracy |
|---------------|----------|
| Conversational: "Is there still time to..." | 100% |
| Structured: "Given current time T, deadline D, evaluate..." | 37.5% |
| JSON: `{"current_time": T, "deadline": D}` | 50% |

**Observation**: 62.5 percentage point drop from conversational to structured format, despite identical information.

### Why This Happens

LLMs learn statistical patterns from training data. When prompt format doesn't match training distribution:
- Pattern matching fails
- Model falls back to generic responses
- Performance degrades unpredictably

**Key insight**: Training data is predominantly conversational, not structured. Models excel at natural language but struggle with formal specifications.

### Implications for Production

**Problem**: Production systems often require structured I/O for:
- Integration with databases (JSON, XML)
- API contracts (OpenAPI specifications)
- Regulatory compliance (EU AI Act Article 13: explainability)

**Tension**: Need for structure conflicts with model's conversational strengths.

**Result**: Systems that work in demos (conversational) fail in production (structured).

### Diagnosis Checklist

To identify prompt brittleness:

1. **Format variation test**
   - Run identical semantic query in 3-5 different formats
   - Measure accuracy variance
   - High variance (>20pp) indicates brittleness

2. **Production format validation**
   - Does your production prompt match training distribution?
   - Can you show the model has seen similar formats?
   - What happens if format slightly changes?

3. **Fallback behavior**
   - What does model do when it doesn't understand format?
   - Does it fail gracefully or generate nonsense?
   - Can you detect format failures automatically?

### Mitigation Strategies

**Strategy 1: Staged processing** (recommended)
- Step 1: Conversational extraction (natural for model)
- Step 2: Deterministic transformation to required format
- Cost: Extra processing step
- Benefit: Leverages model strengths

**Strategy 2: Format-specific fine-tuning**
- Train model on your specific formats
- Cost: Training data + compute
- Benefit: Direct adaptation
- Risk: May hurt general capability

**Strategy 3: Hybrid validation**
- Use structured format but validate with conversational check
- Cost: 2x LLM calls
- Benefit: Catches format-related errors
- When: High-stakes decisions only

We'll implement Strategy 1 in Chapter 4.

---

## Tips

**Live demonstration**: This concept is abstract until students see it. Use Example 1 to show identical semantic content with different formats producing wildly different results.

**Debugging mindset**: Teach students to always test multiple formats during development, not just production format.

**EU AI Act tie-in**: Article 13 requires explanations. Structured outputs help compliance but hurt performance. This is the compliance-safety tradeoff from your research.

---

## 5. Multi-Agent Coordination Failures

### The Problem

When multiple agents must coordinate to achieve a goal, systemic failures emerge from:
1. Inconsistent world models (each agent has different understanding)
2. Communication bottlenecks (agents can't share state efficiently)
3. Conflicting objectives (agents optimize locally, not globally)

### Coordination Taxonomy

**Type 1: Information asymmetry**
- Agent A knows fact X
- Agent B needs fact X but doesn't know to ask
- Result: Suboptimal decision from Agent B

**Type 2: Conflicting goals**
- Agent A optimizes for speed
- Agent B optimizes for thoroughness
- Result: Agents make incompatible plans

**Type 3: Temporal misalignment**
- Agent A plans assuming 2-hour window
- Agent B plans assuming 30-minute window
- Result: Plans cannot be executed together

**Type 4: Resource contention**
- Both agents call same expensive API
- Neither knows other is calling
- Result: Redundant cost and rate limiting

### Experimental Results

From multi-agent coordination experiments:

**Static scenario** (no time pressure):
- Success rate: 75-85%
- Agents produce compatible plans
- Conflicts resolved through communication

**Dynamic scenario** (time pressure, changing constraints):
- Success rate: 35-50%
- 25-40% drop in coordination success
- Agents maintain static plans despite changing conditions
- No evidence of urgency propagation

**Conclusion**: Current LLM agents lack shared temporal awareness required for dynamic coordination.

### Real-World Example: Emergency Response

**Scenario**: Building fire, 3 agents coordinate response
- Fire Chief: Extinguish fire
- Medical Lead: Treat casualties
- Evacuation Coordinator: Clear building

**Static planning** (works):
- Fire Chief plans firefighting approach
- Medical Lead stages ambulances
- Evacuation Coordinator plans exit routes
- Plans are compatible

**Dynamic constraint** (fails):
- Fire spreads rapidly → building collapse in 15 minutes
- Fire Chief continues firefighting plan (2-hour approach)
- Evacuation Coordinator prioritizes orderly exit (30-minute process)
- Medical Lead sets up triage outside building
- **Result**: Plans no longer compatible, people trapped

**Root cause**: No shared "urgency update" mechanism. Each agent maintains initial plan without adapting to new temporal constraint.

### Coordination Observability

To diagnose coordination failures, track:

**Agent communication graph**:
- Who talks to whom?
- How often?
- What information is shared?

**Decision dependency graph**:
- Which agent decisions depend on others?
- Are dependencies satisfied?
- Where are information bottlenecks?

**Temporal synchronization**:
- Do agents share temporal state?
- Are deadlines propagated?
- How do agents handle urgency?

We build these monitoring tools in Chapter 2.

---

## Tips

**Complexity warning**: This is the most conceptually difficult section. Use concrete examples (like emergency response) before abstract frameworks.

**Visualization**: Draw the coordination graph on screen/whiteboard during live session. Visual representation helps students grasp dependencies.

**Foreshadowing**: Chapter 3 shows how to crystallize coordination patterns into deterministic workflows, solving these issues.

---

## 6. Systematic Diagnosis Framework

### The Diagnostic Process

When an agentic system fails in production, follow this process:

**Step 1: Reproduce the failure**
- Capture exact inputs that caused failure
- Run in controlled environment
- Verify failure is consistent, not random

**Step 2: Isolate the failure mode**
- Is it temporal? (wrong timing, missed deadline)
- Is it cost? (unexpected expense)
- Is it coordination? (agents conflict)
- Is it brittleness? (format sensitivity)

**Step 3: Trace the failure path**
- Which agent(s) involved?
- What was agent state at failure point?
- What LLM calls were made?
- What were the outputs?

**Step 4: Identify root cause**
- Pattern matching failure?
- Missing constraint check?
- Coordination breakdown?
- Architectural limitation?

**Step 5: Classify remediation approach**
- Can prompt engineering fix it? (rarely)
- Needs observability infrastructure? (Chapter 2)
- Requires workflow crystallization? (Chapter 3)
- Demands architectural redesign? (Chapter 4)

### Diagnosis Decision Tree

```
Failure observed
├── Performance degradation?
│   ├── Prompt format changed? → Brittleness issue
│   ├── Novel scenario? → Pattern matching failure
│   └── Time pressure added? → Temporal coordination failure
├── Cost explosion?
│   ├── Redundant calls? → Missing caching
│   ├── Infinite loops? → Unbounded recursion
│   └── Over-specified prompts? → Context optimization needed
└── Coordination failure?
    ├── Conflicting outputs? → Shared state needed
    ├── Information gaps? → Communication bottleneck
    └── Temporal mismatch? → Urgency propagation missing
```

### When to Escalate

Some failures indicate fundamental architectural problems that cannot be fixed with monitoring or optimization:

**Escalation triggers**:
1. Failure persists across multiple prompt formulations
2. Cost reduction attempts increase failure rate
3. Agents cannot coordinate even with perfect information
4. Temporal reasoning fails on simple scenarios

**Action**: Move to hybrid architecture (Chapter 4), combining LLM flexibility with deterministic reliability.

---

## Tips

**Framework as tool**: Don't just teach the framework—have students apply it to example cases during live session.

**Decision tree**: Keep this visible throughout examples. After each failure demonstration, walk through the tree to classify it.

**Homework preview**: Students will use this framework to diagnose their chosen system in homework.

---

## 7. Key Takeaways

### Core Concepts

1. **Agentic systems fail predictably**: Not random bugs, but systematic architectural limitations
2. **Temporal reasoning is hard**: LLMs lack continuous time representation
3. **Cost scales non-linearly**: Small inefficiencies compound in multi-agent systems
4. **Brittleness is ubiquitous**: Format changes cause large performance drops
5. **Coordination requires infrastructure**: Shared state, communication protocols, temporal sync

### What You Can Do Now

After this chapter, you can:
- Identify which of the four failure modes is affecting your system
- Use the diagnosis framework to trace failures to root causes
- Estimate cost impact of coordination inefficiencies
- Recognize when failures are architectural vs. fixable

### What You Cannot Do Yet

You still need:
- Observability infrastructure to monitor systems (Chapter 2)
- Techniques to extract and crystallize workflows (Chapter 3)
- Architectural patterns for hybrid systems (Chapter 4)

### Preparation for Chapter 2

Next chapter builds monitoring infrastructure to track:
- LLM call patterns and costs
- Agent communication graphs
- Temporal constraint propagation
- Failure pattern detection

**Action**: Complete homework to solidify diagnosis skills before learning to monitor systems.

---

## Tips

**Closure**: End with clear "you can / you can't" distinction. Students should feel they learned something valuable but know they need next chapters.

**Homework motivation**: Explain that diagnosis skills matter little without monitoring tools, which they'll build in Chapter 2.

**Questions**: Reserve 5 minutes for questions before moving to examples.

---

## References

### Research Papers

1. Marin, J. (2025). "Empirical Characterization of Temporal Constraint Processing in LLMs." arXiv:2411.XXXXX
2. Marin, J. (2024). "The Compliance-Safety Tradeoff in Medical AI Systems."
3. Marin, J. (2024). "Neural-to-Symbolic Program Extraction Framework."

### Additional Reading

- Anthropic. "Claude 4 Model Family: Technical Report"
- LangChain Documentation: "Agent Concepts" - https://python.langchain.com/docs/modules/agents/
- EU AI Act: "Article 13 - Transparency and provision of information to deployers"

### Tools Referenced

- Anthropic Console: https://console.anthropic.com
- LangSmith (observability): https://smith.langchain.com
- Course repository: https://github.com/Javihaus/agents_observability_bootcamp

---

## Next Steps

1. Watch instructor demonstrate three failure modes in live examples
2. Complete homework: Audit an agentic system using diagnosis framework
3. Read Chapter 2 content before next session
4. Optional: Review referenced research papers for deeper understanding

---

**End of Chapter 1 Theory Content**
