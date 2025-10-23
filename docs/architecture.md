# TRM Integration: Detailed Architecture

## Table of Contents
1. [Interface Specifications](#interface-specifications)
2. [Orchestration Protocols](#orchestration-protocols)
3. [GeoBench Integration](#geobench-integration)
4. [BYON Module Design](#byon-module-design)
5. [Blind Self-Audit Synergy](#blind-self-audit-synergy)
6. [Cost-Benefit Analysis](#cost-benefit-analysis)
7. [Risk Assessment](#risk-assessment)

---

## 1. Interface Specifications

### TRM Agent Interface (YAML/PSL)
```yaml
trm_agent:
  interface_spec:
    input:
      problem_structure:
        format: "PSL contract"
        fields:
          - question: string
          - constraints: list[constraint]
          - domain: enum[geometric, logical, constraint_satisfaction]
          - max_cycles: int
      
      initial_state:
        y_init: tensor  # optional starting point
        z_init: tensor  # optional latent
    
    output:
      solution:
        y_final: tensor
        confidence: float
        reasoning_trace: list[step]
        
      metadata:
        cycles_used: int
        convergence: bool
        computation_cost: float
```

### PSL Contract Example
```yaml
!psl v0.1
context: geometric_reasoning
goal: solve_arc_agi_task
constraints: [grid_constraints, color_consistency, pattern_rules]

[FACT]
input_grid = [[1,0,1], [0,1,0], [1,0,1]]
pattern_type = "symmetry"

[PLAN]
- route_to: trm_agent
- max_cycles: 10
- expected_output: transformed_grid

[CHECKLIST]
- constraints_satisfied: ✓
- pattern_consistent: ✓
- confidence > 0.8: ?
```

---

## 2. Orchestration Protocols

### Librarium Routing Logic
```yaml
routing_decision_tree:
  
  structured_reasoning:
    condition: "task.type == 'reasoning' AND task.structure == 'well_defined'"
    domains: [geometric, logical, constraint_satisfaction]
    route_to: trm_agent
  
  open_ended:
    condition: "task.type == 'generation' OR task.structure == 'open_ended'"
    route_to: llm_agent
  
  critical_verification:
    condition: "task.critical == true"
    route_to: parallel[trm_agent, blind_auditor]
    consensus: required
```

### Workflow Patterns

**Pattern 1: Simple Routing**
```
PSL Contract → Librarium Analyzer → TRM (if structured) → PSL Validator → Result
```

**Pattern 2: Hybrid Reasoning**
```
Complex Task → LLM (candidate) → TRM (refine) → Blind Auditor (verify) → Consensus
```

**Pattern 3: Iterative Refinement**
```
TRM (v1) → Auditor (finds issues) → PSL (update constraints) → TRM (v2) → Repeat
```

---

## 3. GeoBench Integration

### Test Suite Structure
```yaml
geobench_domains:
  
  geometric_reasoning:
    tasks: [arc_agi_style, pattern_completion, spatial_transform]
    volume: 1000 problems
    augmentation: 1000x per base
  
  logical_reasoning:
    tasks: [constraint_satisfaction, sudoku_variants, graph_problems]
    volume: 500 problems
    augmentation: 1000x
  
  symbolic_reasoning:
    tasks: [rule_application, program_synthesis, algebra]
    volume: 500 problems
    augmentation: 1000x
```

### Evaluation Metrics
```yaml
metrics:
  accuracy: "% correct solutions"
  efficiency: "params / quality ratio"
  convergence_rate: "avg cycles to solution"
  OG: "Objectivity Gain (blind vs aware)"
  HDR: "Hallucination Detection Rate"
  cost_per_task: "USD inference cost"
```

### Training Data Generation
```yaml
synthetic_generation:
  method: "PSL templates → variants → validation"
  
  example_template:
    !psl v0.1
    context: pattern_synthesis
    goal: generate_training_example
    
    [FACT]
    base_pattern = rotation_90deg
    grid_size = [8, 8]
    num_variants = 1000
    
    [PLAN]
    - generate_base: apply rotation
    - augment: [flip, scale, color_shift]
    - verify: pattern_consistency == true
    - save: to training_dataset
```

---

## 4. BYON Module Design

### Module Interface
```yaml
byon_trm_module:
  type: "reasoning_specialist"
  
  capabilities:
    recursive_reasoning: true
    domains: [geometric, logical, constraint]
    parameters: 7M
    latency_ms: "<100"
    cost_per_call: "$0.0001"
  
  api:
    endpoint: "/trm/reason"
    method: POST
    
    request:
      problem: "PSL encoded"
      max_cycles: int
      domain: string
    
    response:
      solution: object
      confidence: float
      trace: array
      cycles_used: int
```

### Deployment Architecture
```
Librarium (Orchestrator)
│
├─ BYON Module Registry
│  ├─ TRM (reasoning specialist)
│  ├─ GPT-4 (general LLM)
│  ├─ Claude (general LLM)
│  └─ BlindAuditor (verification)
│
└─ Task Queue
   └─ Distributes to appropriate modules
```

---

## 5. Blind Self-Audit Synergy

### Enhanced Workflow
```
Step 1: LLM generates solution_A
Step 2: Anonymizer removes attribution
Step 3 (Parallel):
  - Blind LLM audits anonymized solution
  - TRM independently solves → solution_B
Step 4: Compare solutions
  - If match: HIGH confidence → ACCEPT
  - If differ: Blind Auditor chooses best
  - If uncertain: ESCALATE
```

### Enhanced Metrics
```yaml
OG_enhanced:
  formula: "(trm_independent_score - llm_aware_score)"
  interpretation: "TRM even more objective than Blind LLM"

HDR_cross_check:
  formula: "issues_found_by_trm / total_issues"
  benefit: "Cross-architecture verification"

Agreement_Rate:
  formula: "solutions_match / total_tasks"
  interpretation:
    high (>80%): "LLM reliable on domain"
    low (<50%): "LLM struggles, prefer TRM"
```

---

## 6. Cost-Benefit Analysis

### Cost Comparison
```yaml
TRM:
  training_once: "$2k-5k (4x H100 × 3 days)"
  inference: "$0.0001 per task"
  latency: "10-100ms"
  
LLM:
  training: "N/A (pre-trained)"
  inference: "$0.01-0.03 per task"
  latency: "500-2000ms"
```

### ROI Calculation
```
Scenario: 10k structured tasks/day

Without TRM: 10k × $0.02 = $200/day = $73k/year

With TRM (80% structured → TRM):
  8k × $0.0001 = $0.80
  2k × $0.02 = $40
  Total = $40.80/day = $14.9k/year

Annual savings: $58k - $5k training = $53k
ROI: 10.6x in first year
Break-even: 31 days
```

---

## 7. Risk Assessment

### Risk Matrix

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Specialization trap | High | Medium | Clear scope definition, fallback to LLM |
| Training data quality | High | Medium | PSL validation, human review |
| Convergence failures | Medium | Low | Hard limits, timeout handling |
| Integration complexity | Medium | Medium | Staged rollout, graceful degradation |
| Cost overrun | Low | Low | Pilot first, measure actual costs |

### Monitoring & Alerts
```yaml
monitoring:
  accuracy_drift:
    metric: "TRM accuracy on novel tasks"
    alert: "If drops >10%"
    
  convergence_issues:
    metric: "Timeout rate"
    alert: "If >5%"
    
  cost_tracking:
    metric: "Actual cost vs projection"
    alert: "If >20% over budget"
```

---

## Implementation Roadmap

See [`integration-roadmap.md`](integration-roadmap.md) for phased implementation plan.

