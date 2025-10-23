# TRM Integration in Symbion.space

## Overview

**TRM** is a 7M-parameter recursive reasoning model from Samsung SAIL Montreal that achieves 45% on ARC-AGI-1 through iterative solution refinement. Unlike massive LLMs, TRM demonstrates that structured recursive reasoning can solve complex tasks with minimal parameters.

**Symbion.space** integrates TRM as a specialized reasoning component within the Librarium orchestration layer, complementing general-purpose LLMs for structured problem-solving.

---

## Architecture Position
```
Librarium (Orchestrator)
├── LLM Agents (GPT/Claude) → General intelligence
├── TRM Agent (7M params) → Structured reasoning specialist  
├── Blind Auditor → Verification layer
└── PSL Validator → Constraint checking
```

**Routing Logic:**
- Structured reasoning (geometric, logical, constraint satisfaction) → **TRM**
- Open-ended generation/knowledge retrieval → **LLM**  
- Critical validation → **Both** (cross-verification)

---

## Key Integration Points

### 1. GeoBench Benchmark
TRM serves as the reasoning engine for structured problem evaluation:
- **Domains:** Geometric reasoning, pattern completion, constraint satisfaction
- **Advantage:** 100x cheaper inference than LLM ($0.0001 vs $0.02 per task)
- **Performance target:** >80% accuracy on structured tasks

### 2. BYON Module (Bring Your Own Neurons)
TRM as a pluggable reasoning specialist:
```yaml
Interface:
  Input: PSL-encoded problem + constraints
  Output: Solution + reasoning trace + confidence
  Latency: <100ms (vs 500-2000ms for LLM)
```

### 3. Blind Self-Audit Enhancement
TRM provides independent verification:
- **Pattern:** LLM generates → TRM independently reasons → Blind Auditor compares
- **Benefit:** Eliminates consistency bias through architectural diversity
- **Metric:** Enhanced OG (Objectivity Gain) and HDR (Hallucination Detection Rate)

---

## PSL Integration Example
```yaml
!psl v0.1
context: geometric_reasoning
goal: solve_arc_pattern
constraints: [grid_preserved, color_valid, pattern_consistent]

[FACT]
input_grid = [[B,B,R], [B,R,B], [R,B,B]]
transform = rotate_90_invert

[PLAN]
- route_to: trm_agent
- max_cycles: 8
- expected_output: transformed_grid

[CHECKLIST]
- constraints_satisfied: ✓
- trm_converged: ✓
- confidence > 0.8: ✓
```

TRM recursively refines the solution across 6-8 cycles, exposing the reasoning trace for verification.

---

## Cost-Benefit

**Without TRM (100% LLM):**
- 10k structured tasks/day × $0.02 = $200/day = $73k/year

**With TRM (80% → TRM, 20% → LLM):**
- 8k × $0.0001 + 2k × $0.02 = $40.80/day = $14.9k/year
- **Savings:** $58k/year after $5k training investment
- **ROI:** 10.6x in year one

---

## Implementation Status

**Current:** Conceptual architecture + interface specifications  
**Phase 1 (4-6 weeks):** Prototype with single domain (geometric)  
**Phase 2 (8-12 weeks):** Full GeoBench integration + evaluation  
**Phase 3 (12-16 weeks):** Production deployment in Librarium

---

## Key Advantages

1. **Efficiency:** 7M params vs billions — drastically lower inference cost
2. **Explainability:** Recursive reasoning trace shows solution evolution
3. **Specialization:** Outperforms general LLMs on structured tasks
4. **Orchestration:** Complements LLMs rather than replacing them
5. **Objectivity:** No consistency bias (small model, no narrative ego)

---

## Technical Specs

- **Model Size:** 7M parameters (~28MB)
- **Architecture:** Recursive refinement (H_cycles=3, L_cycles=4-6)
- **Training:** ~3 days on 4x H100 for domain-specific data
- **Inference:** <100ms latency, $0.0001 per task
- **Domains:** Geometric, logical, constraint satisfaction reasoning

---

## Architecture Details

See [`docs/architecture.md`](docs/architecture.md) for:
- Detailed component specifications
- Interface definitions (YAML/PSL)
- Orchestration workflows
- State management
- Risk analysis and mitigation

See [`docs/integration-roadmap.md`](docs/integration-roadmap.md) for phased implementation plan.

---

## Philosophy Alignment

Both TRM and Symbion.space reject "bigger is better":
- **TRM:** Small model + recursive reasoning > massive parameters
- **Symbion:** Orchestration + structure > brute-force scaling

**"The Geologist vs. The Hammer"** — structured intelligence beats raw computational force.

---

## References

- **TRM Paper:** [arXiv:2510.04871](https://arxiv.org/abs/2510.04871) "Less is More: Recursive Reasoning with Tiny Networks"
- **TRM Code:** [Samsung SAIL Montreal GitHub](https://github.com/SamsungSAILMontreal/TinyRecursiveModels)
- **Symbion Protocols:** PSL (Problem-Structured Language), Blind Self-Audit, GeoBench

---

## Status & Contributing

**Status:** Architecture design phase (RFC)  
**Version:** 0.1 (Conceptual)

This is a design document for integrating TRM into the Symbion.space ecosystem. Implementation pending resource availability and validation.

---

