---
name: root-cause-analysis
description: "Structured root cause analysis using named methodologies: Toyota's 5 Whys, Ishikawa fishbone, Pareto analysis and fault tree analysis. Use when asked for \"the root cause\", \"why does this keep happening\", \"5 whys\", \"a fishbone\", \"a post-mortem\" or \"an incident analysis\"; when a problem has recurred after being fixed once already; or when a symptom has several candidate causes that need separating systematically. Applies across software, hardware, process failures and personal problems. Do NOT use for ordinary debugging where the cause is findable by reading the code or the logs. That is normal work, and a 5 Whys on a typo wastes everyone's time."
license: Apache-2.0
metadata:
  version: "2.1"
  source: Toyota Production System, plus standard quality-engineering methodologies
  author: Taiichi Ohno (5 Whys), Kaoru Ishikawa (fishbone diagrams)
  attribution: Methodologies restated with original worked examples.
---

# Root Cause Analysis Orchestration Skill

This skill helps you systematically identify the root cause of any problem using proven methodologies from the Toyota Production System and other industry-standard techniques.

## Quick Reference: When to Load Which Resource

| Your Problem Type | Load Resource | Why |
|---|---|---|
| Need to understand 5 Whys, Fishbone, Pareto, Fault Tree methodology | `resources/rca-methodologies.md` | Learn each method step-by-step with examples |
| Looking for common root causes in your domain | `resources/common-root-causes.md` | Pattern match against known causes: software, hardware, process, personal |
| Want to see complete worked examples | `resources/example-analyses.md` | Study real cases: software bugs, vehicle maintenance, system failures, personal problems |
| Advanced: need barrier analysis, complex cause mapping | `resources/advanced-techniques.md` | Formal methods: Fault Tree, Barrier Analysis, multi-methodology chains |

## Core Principle

**Do not treat symptoms. Find and fix the root cause.** As Taiichi Ohno, architect of the Toyota Production System, said: "By repeating why five times, the nature of the problem as well as its solution becomes clear."

## Orchestration Protocol

### Phase 1: Problem Classification

Quickly identify your problem domain and complexity:

**Problem Domain:**
- **Software**: Code bugs, system failures, performance, deployment
- **Hardware**: Equipment, mechanical, electrical, maintenance
- **Process**: Workflow, procedures, organisational, communication
- **Personal**: Life challenges, productivity, habits, wellbeing

**Complexity Level:**
- **Simple**: Clear failure chain, 1-2 likely causes → Use 5 Whys
- **Complex**: Multiple possible causes, unknown scope → Start with Fishbone
- **Critical/Safety**: High stakes, needs rigor → Use Fault Tree
- **Multiple Issues**: Many competing problems → Use Pareto first

**Action:** Load appropriate resource file(s) based on classification.

### Phase 2: Methodology Selection

Based on problem type, select your approach:

| Situation | Recommended | Load |
|---|---|---|
| **Single clear failure** | 5 Whys | rca-methodologies.md |
| **Complex/multiple possibilities** | Fishbone → 5 Whys | rca-methodologies.md |
| **Competing priorities** | Pareto → 5 Whys | rca-methodologies.md |
| **Safety/high-stakes** | Fault Tree | advanced-techniques.md |
| **Process breakdown** | Barrier Analysis | advanced-techniques.md |
| **Pattern matching** | Common causes + 5 Whys | common-root-causes.md |

### Phase 3: Execution & Verification

**During Analysis:**
1. Define problem clearly (What/Where/When/Impact)
2. Gather evidence systematically
3. Apply selected methodology
4. Document reasoning at each step
5. Verify root cause with Forward/Backward tests

**Before Finalising:**
- Validate conclusion against evidence
- Check for red flags (see common-root-causes.md)
- Confirm actionability (can you fix this?)
- Develop solutions addressing root cause

## Problem Definition Framework

Create a clear problem statement before analysis:

**Essential Elements:**
- **What:** Observable symptom (not assumed cause)
- **Where:** Location/system/component affected
- **When:** Timeline, frequency, pattern
- **Impact:** Users/systems affected, severity

**Example:**
"Users in EU region experience 3-5 second dashboard load delays during 9-11 AM UTC peak hours, affecting ~2,000 daily active users. Started after v2.4 deployment on Nov 18th."

## Evidence Gathering (Go and See)

Follow Toyota's principle: collect facts, not opinions.

**Key Evidence Sources:**
- Logs, metrics, monitoring data
- Timeline of events and changes
- System/code/configuration changes before problem
- Environmental factors (load, traffic, season)
- User reports and reproduction steps
- System state before/during/after

## RCA Methodologies

See `resources/rca-methodologies.md` for complete methodology guide.

## Resource Files Summary

### `resources/rca-methodologies.md`
Methodology guide covering:
- **5 Whys**: Step-by-step process with software examples
- **Fishbone Diagram**: Structure, 6 M's categories, process
- **Pareto Analysis**: Prioritisation using 80/20 rule
- **Fault Tree Analysis**: Top-down formal analysis
- **Barrier Analysis**: Control failure examination
- Structured 6-phase RCA process, domain-specific guidance, templates

### `resources/common-root-causes.md`
Pattern reference catalogue by domain:
- **Software Engineering**: Code defects, configuration, dependencies, deployment
- **Hardware & Equipment**: Mechanical, electrical, operational, maintenance
- **Process & Operations**: Workflow, design, resources
- **Personal/Life**: Health, habits, environment, skills
- Red flags, recurring themes, pattern recognition

### `resources/example-analyses.md`
Four worked examples with full analysis:
1. **Software Bug**: JWT authentication (5 Whys)
2. **Vehicle Maintenance**: Overheating (5 Whys)
3. **System Failure**: E-commerce checkout (Fishbone + 5 Whys)
4. **Personal Productivity**: Missed deadlines (Fishbone + 5 Whys)

### `resources/advanced-techniques.md`
Formal methods for complex problems:
- **Fault Tree Analysis**: Boolean logic, safety systems
- **Barrier Analysis**: Control failures
- **Multi-Methodology Chains**: Complex orchestration
- **Verification Frameworks**: Full testing

## How This Skill Works

1. **Clarify your situation**: Domain, observations, evidence, time
2. **Recommend approach**: Complexity analysis, methodology, resources
3. **Guide through analysis**: Problem statement, evidence, methodology, verification
4. **Deliver output**: Analysis, root cause, solutions, implementation

## Quick Start: 5-Minute RCA

1. State problem (What/Where/When/Impact)
2. First Why: fact-based answer
3. Second Why: dig deeper
4. Third Why: dig deeper again
5. Verify: would fixing this prevent it?

## Templates & Examples

- **5 Whys Template** in `resources/rca-methodologies.md`
- **Fishbone Template** in `resources/rca-methodologies.md`
- **Worked Examples** in `resources/example-analyses.md`
- **Solution Structures** in `resources/example-analyses.md`

## Next Steps

1. Identify problem domain (software/hardware/process/personal)
2. Load appropriate resource from table above
3. Select methodology based on complexity
4. Follow step-by-step process in resource
5. Verify root cause (Forward/Backward tests)
6. Develop actionable solutions

---

**Remember**: Goal is systematic investigation, disciplined questioning until you reach a cause you can actually fix.
