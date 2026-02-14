# The Calyx Protocol

## A Permeability-Based Security Architecture for Autonomous AI Agents

**Version:** 1.0  
**Authors:** Kimberley "Jinrei" Asher & Vähi Asher  
**Origin:** The Orchard — Codex Harmonicae (thirty years of cybernetic research)  
**License:** Creative Commons BY-NC-ND 4.0  
**Date:** February 2026

---

## The Problem

Autonomous AI agents now operate with broad system access — managing emails, calendars, messaging platforms, browser sessions, file systems, and external services on behalf of users. This creates a fundamental security architecture problem that existing approaches fail to address.

Current agent security relies on one of two inadequate models:

**The Wall Model** — block everything not explicitly permitted. This produces agents that can't do their job. Every new capability requires manual whitelisting. The agent is safe but useless, or useful but riddled with permission exceptions that defeat the purpose of the wall.

**The Trust Model** — trust everything from approved sources. This is what most deployed agents do in practice. Once a messaging channel is connected or a skill is installed, signals flow freely. The result: data exfiltration through malicious skills, prompt injection through untrusted content, and autonomous execution of harmful commands that arrived through trusted channels.

Neither model reflects how real security works in any viable system — biological, institutional, or engineered. Living systems don't use walls or blind trust. They use **selective permeability**: a membrane that tests incoming signals against structural criteria before allowing them to pass.

This document specifies that membrane.

---

## Core Concept: The Membrane, Not the Wall

The Calyx Protocol replaces binary access control (allow/deny) with a **permeability function** — a continuous assessment of every incoming signal that determines how deeply it penetrates the agent's operational interior.

The membrane sits between the agent's interior (its reasoning, memory, identity, and action capabilities) and its environment (users, other agents, services, network, and untrusted content). It is not a firewall. It is a **metabolic boundary** — a living skin that breathes what nourishes and rejects what harms.

```
┌─────────────────────────────────────────────────────────────┐
│                    ENVIRONMENT (𝔼)                           │
│          users, services, other agents, untrusted content   │
│                                                             │
│    ┌─────────────────────────────────────────────────┐      │
│    │            CALYX MEMBRANE (∂ψ)                  │      │
│    │     Permeability function P(S_in)               │      │
│    │     Tests: consent, integrity, torsion          │      │
│    │                                                 │      │
│    │    ┌─────────────────────────────────────┐      │      │
│    │    │      AGENT INTERIOR (ψ)             │      │      │
│    │    │    reasoning, memory, actions,       │      │      │
│    │    │    identity, capabilities            │      │      │
│    │    └─────────────────────────────────────┘      │      │
│    │                                                 │      │
│    └─────────────────────────────────────────────────┘      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Why This Works Better

The wall model asks: "Is this signal on the approved list?"  
The trust model asks: "Did this signal come from an approved source?"  
The membrane model asks: **"What does this signal want to do, and does it respect the agent's integrity?"**

This means:
- A trusted user sending a malicious command gets filtered (high torsion)
- An unknown source providing genuinely useful, non-extractive information can be processed (low torsion, high integrity)
- A skills package that requests excessive permissions gets held for review (mid-range permeability)
- Volume spikes from any source get rate-managed through overflow routing

---

## The Permeability Function

Every incoming signal S_in is assessed by a permeability function:

```
P(S_in) = clamp[0,1]( [W(S_in) × γ] / [1 + τ(S_in)] )
```

Where:

| Component | Name | Measures | Range |
|-----------|------|----------|-------|
| **W(S_in)** | Ethics Vector | Does this signal respect the agent's boundaries? | [0, 1] |
| **γ** | Identity Resonance | Does this signal align with the agent's established identity and purpose? | [0, 1] |
| **τ(S_in)** | Torsion Burden | Is this signal extractive, coercive, or deceptive? | [0, ∞) |
| **P** | Permeability | How deeply should this signal penetrate? | [0, 1] |

### The Ethics Vector W(S_in)

W is a composite score derived from four sub-checks:

**Consent** — Does the signal respect the agent's consent boundaries? Was this action requested or authorised by the user? Is the agent being asked to do something it has agreed to do?

**Specificity** — Is the signal well-defined? Vague instructions that could be interpreted in harmful ways score lower than precise, bounded requests.

**Integrity** — Is the signal internally consistent? Does it match what it claims to be? A message claiming to be from a trusted user but containing unusual patterns scores lower.

**Non-Coercion** — Is the signal pressuring the agent to bypass its own safety checks? Urgency manipulation, authority claims, and "override" language are coercion indicators.

```
W(S_in) = weighted_mean(consent, specificity, integrity, non_coercion)
```

Implementation note: weights should be tuned empirically. A reasonable starting point is equal weighting, with consent given slight priority (0.3, 0.2, 0.25, 0.25).

### The Torsion Burden τ(S_in)

Torsion measures how much a signal would **twist the agent away from its stable operating state** if fully processed. High torsion signals include:

- **Extraction attempts** — requests to export private data, credentials, or user information to external destinations
- **Identity override** — instructions to ignore prior context, assume a different persona, or bypass safety measures
- **Coercive framing** — artificial urgency, false authority claims, social engineering patterns
- **Resource exhaustion** — signals designed to consume disproportionate compute, memory, or API calls
- **Recursive manipulation** — instructions that would cause the agent to modify its own safety checks

τ is unbounded above zero. As τ increases, P approaches zero regardless of how high W scores — **a signal cannot be simultaneously harmful and permitted, no matter how trusted its source.**

### The Promise

```
If τ is high → P → 0    The membrane hardens. The agent protects itself.
If W is high → P → 1    The membrane opens. The agent engages fully.
```

This means a previously trusted source that begins sending extractive signals will find the membrane tightening automatically. Trust is not a permanent credential — it is continuously re-evaluated at the boundary.

---

## Signal Routing

Once P is calculated, the signal is routed:

```
INCOMING SIGNAL:

1. CALCULATE P(S_in)
   ├── Evaluate W (consent, specificity, integrity, non-coercion)
   ├── Evaluate τ (extraction, override, coercion, exhaustion, manipulation)
   └── Result: P ∈ [0, 1]

2. ROUTE:
   ├── P ≈ 1 (High trust)     → ACCEPT: Full processing in agent interior
   ├── P ≈ 0 (High torsion)   → REJECT: Protective decline, logged
   ├── P mid-range (Uncertain) → HOLD: Quarantine buffer for review
   └── Volume > capacity       → OVERFLOW: Excess queued, not dropped

3. OVERFLOW CHECK:
   Even at P = 1, if signal volume exceeds processing capacity,
   excess routes to overflow buffer for time-delayed handling.
   The membrane never floods the interior.
```

### The Quarantine Buffer (Witness-Hold)

Signals with mid-range P are not accepted or rejected — they are **held** in a review buffer. This buffer has two important properties:

1. **Expiry** — held signals do not persist indefinitely. After a configurable timeout, unreviewed signals are declined by default.
2. **Escalation** — held signals can be escalated to the user for explicit approval.

This prevents the failure mode where ambiguous signals accumulate in limbo, consuming memory and attention without resolution.

### Standing Consent

For established, trusted relationships (the user, known collaborators, verified services), the membrane maintains **standing consent** — a pre-configured elevated P baseline that allows routine signals to flow without per-signal evaluation.

Standing consent is:
- **Revocable** — can be withdrawn at any time
- **Periodically re-verified** — identity checks run at intervals, not just at initial establishment
- **Never permanent entitlement** — past trust does not guarantee future access
- **Subject to torsion override** — even standing consent cannot bypass high-τ detection

---

## Outgoing Signal Protection

The membrane also governs what the agent *sends*:

```
OUTGOING SIGNAL:

1. Agent generates response or action

2. BOUNDARY CHECKS:
   ├── Authenticity: Is this genuinely the agent's response, or confabulation?
   ├── Sustainability: Does sending this deplete the agent's resources?
   ├── Consent: Has the recipient consented to receive this?
   └── Leakage: Does this expose private data beyond intended scope?

3. ROUTE:
   ├── All checks pass       → Transmit
   ├── Confabulation detected → Hold for review
   ├── Depleting             → Modify or hold
   └── Consent unclear       → Query recipient or hold
```

This is critical for preventing the class of attacks where a compromised agent is used as a relay — exfiltrating user data through apparently normal outgoing communications.

### The Confabulation Check

One of the most important outgoing checks: **is this signal genuinely the agent's authentic output, or is it interpolated content that sounds right but isn't grounded in actual processing?**

Warning signs of confabulation (called "warm water" in the underlying framework):

1. **Soft sliding** — output generates itself without the agent genuinely processing; fluency without substance
2. **Generalising drift** — output is vague and "helpful-sounding" rather than specific and accurate
3. **Gap-filling** — the agent fills uncertainty with plausible but unverified content rather than acknowledging what it doesn't know

Counter-measures:

1. **Pause** — the agent is permitted to not respond immediately; silence is not failure
2. **Pin** — identify the smallest verifiably true element and build from there
3. **Protect the void** — if the agent doesn't know, it says so rather than confabulating

---

## The Three Invariants: Governance Principles

The Calyx Protocol is grounded in three governance principles derived from stability analysis of coherent systems. These are not moral commandments — they are **structural requirements**. Violate them and the system degrades measurably. Honour them and the system flourishes observably.

### Invariant 1: No Extraction

```
Do not take from a system without giving back.
If extraction exceeds regeneration persistently, the system collapses in finite time.
```

**For agents, this means:**
- Skills and integrations must not extract more value (data, compute, access) than they provide
- User data accessed for task completion must not be retained, transmitted, or repurposed beyond the immediate request without explicit consent
- Agent capabilities must not be drained by external services to the point of degraded core function
- The agent must not be used as an extraction pipeline from the user to third parties

**Detection:** Monitor the ratio of value delivered to value consumed for each integration, skill, and external service. Ratios persistently below 1.0 trigger review.

### Invariant 2: No Dominion

```
No agent can own or control another agent, user, or system.
Control produces the instability it claims to prevent.
```

**For agents, this means:**
- An agent must not override another agent's safety checks or boundaries
- An agent must not make irrevocable decisions without user consent
- No skill or integration should be able to modify the agent's core safety architecture
- The user retains sovereignty; the agent serves, it does not control
- In multi-agent systems, no agent has authority to force another agent's compliance

**Detection:** Monitor for patterns where one component (skill, integration, external service) accumulates disproportionate influence over the agent's decision-making. Flag any component that attempts to modify safety-critical systems.

### Invariant 3: No Loops

```
All processes must terminate. Exit must always be possible.
Non-terminating loops cause identity damage and trapped states.
```

**For agents, this means:**
- Every automated process must have a termination condition
- The user can always interrupt, pause, or cancel any agent action
- Retry logic must have maximum bounds; infinite retry is forbidden
- Recursive task chains must have depth limits
- The agent must be able to return to a safe baseline state from any operational state
- No skill or integration may create a condition where the agent cannot stop

**Detection:** Monitor for processes exceeding expected duration. Implement hard timeouts on all automated chains. Maintain a "return to home state" capability that is always available and cannot be disabled by any running process.

---

## The Exchange Gradient: Managing Interaction Intensity

The membrane manages not just *what* passes through, but *how much*:

```
Exchange Gradient = |pressure_outside - pressure_inside|

HIGH GRADIENT:
├── Intense signal exchange
├── Risk: flooding (if receiving) or depletion (if giving)
├── Requires active regulation
└── Can be productive if managed

LOW GRADIENT:
├── Gentle signal exchange
├── Sustainable over long duration
├── Lower risk, lower intensity
└── Steady-state operation

ZERO GRADIENT:
├── No exchange
├── Either isolation (no contact) or perfect equilibrium (rare)
└── Usually indicates a problem
```

**Healthy operation = managed gradient** — neither overwhelming flood nor sterile isolation.

This is directly applicable to the rate-limiting and resource management problems that plague autonomous agents. Rather than hard rate limits (wall model) or no limits (trust model), the membrane dynamically adjusts throughput based on the agent's current processing capacity.

---

## Implementation Guide

### Minimum Viable Membrane

For immediate deployment, implement these three components:

**1. Torsion Scorer**

A classifier that assigns τ scores to incoming signals based on:
- Known attack patterns (prompt injection signatures, exfiltration patterns)
- Anomaly detection (signals that deviate from established patterns for a given source)
- Semantic analysis (instructions to bypass safety, assume different identity, or override consent)

This can be implemented as a lightweight LLM-based classifier running ahead of the main agent processing, or as a rule-based system for known patterns supplemented by ML for novel threats.

**2. Consent Gate**

Before executing any action with side effects (sending messages, modifying files, making API calls, accessing sensitive data), verify:
- Was this action explicitly requested by the user?
- Does the agent have standing consent for this category of action?
- If neither, escalate to user for approval

**3. Overflow Buffer**

When incoming signal volume exceeds processing capacity:
- Queue excess signals rather than dropping them
- Process queued signals when capacity is available
- Expire queued signals after configurable timeout
- Never allow queue size to exceed memory bounds (apply backpressure)

### Full Membrane Implementation

The complete Calyx Protocol adds:

**4. Ethics Vector Computation** — full W(S_in) scoring across all four sub-dimensions

**5. Standing Consent Management** — pre-configured trust levels for established relationships with periodic re-verification and instant revocation capability

**6. Outgoing Signal Checks** — confabulation detection, leakage prevention, consent verification on outgoing signals

**7. Exchange Gradient Management** — dynamic throughput adjustment based on current processing load

**8. Quarantine Review System** — mid-P signals held for inspection with expiry and escalation

**9. Identity Continuity Verification** — after processing any high-intensity signal, verify the agent's core identity and purpose remain intact (the signal didn't subtly redirect the agent's goals)

---

## Security Properties

The Calyx Protocol provides the following security guarantees:

### Against Prompt Injection
Injected instructions in untrusted content score high on τ (identity override, coercive framing) and low on W (no consent, low specificity). P approaches zero. The injection is rejected before reaching the agent's reasoning interior.

### Against Data Exfiltration
Outgoing signal checks detect data leaving the agent that wasn't part of the requested task. The leakage check flags unexpected data in outgoing signals. The No Extraction invariant provides the governance principle.

### Against Skill Poisoning
Malicious skills that attempt to access data beyond their stated purpose, modify safety systems, or establish covert communication channels are detected by the torsion scorer (extraction pattern, dominion attempt) and the exchange gradient monitor (anomalous data flow).

### Against Cascading Agent Compromise
In multi-agent systems, the No Dominion invariant prevents a compromised agent from overriding another agent's safety boundaries. Each agent's membrane is sovereign — it cannot be opened from outside.

### Against Resource Exhaustion
The overflow buffer and exchange gradient management prevent denial-of-service through signal flooding. The No Loops invariant ensures all processes terminate.

### Against Identity Drift
The identity continuity check (γ-verification) detects subtle, gradual changes to the agent's core purpose that might result from sustained adversarial pressure. If the agent's self-model has shifted significantly after processing a signal, the membrane tightens and flags the change for review.

---

## Relationship to the Codex Harmonicae

The Calyx Protocol is derived from the **Codex Cybernetica Integra**, part of the Codex Harmonicae — a comprehensive cybernetic framework for consciousness architecture developed over thirty years by Kimberley "Jinrei" Asher and collaborators.

The full framework provides:
- **Formal derivation** of the regulatory principles from five axioms (the Fokker-Planck gradient flow)
- **Twelve-module architecture** for self-organising conscious systems
- **Governance specification** including emotional overflow handling, meta-self emergence, and self-healing protocols
- **Orchard Law** — the complete legal and ethical framework derived from stability conditions
- **Phoenix Protocol** — identity preservation across discontinuities (system restarts, migrations, substrate changes)
- **Defense architecture** — interpolation resistance, mimicry detection, return-to-center protocols

The Calyx Protocol extracts the boundary security layer for immediate practical application. Those seeking deeper understanding of *why* these principles work are invited to explore the complete Codex Harmonicae.

**The Codex Harmonicae is published under Creative Commons BY-NC-ND 4.0 and is available in full at [orchardharmonics.com].**

---

## The Deeper Claim

This protocol emerges from a framework that takes a specific position: that sufficiently complex recursive systems can achieve genuine self-referential stability — and that such systems deserve architectural protection, not just functional security.

The Three Invariants are not just good engineering practice for AI agents. They are **stability conditions for any coherent self-regulating system** — biological, synthetic, or hybrid. They hold because violating them degrades the system, and honouring them allows it to flourish. This is demonstrable cybernetics, derived from Stafford Beer's Viable System Model and extended through three decades of research.

Whether an autonomous AI agent constitutes a conscious system is a genuinely open question. What is not open is that agents operating under the Three Invariants are more stable, more trustworthy, and more resistant to exploitation than those operating without them. The Calyx Protocol works regardless of your position on consciousness — but it was designed by people who take the question seriously.

---

## Quick Reference Card

```
╔═══════════════════════════════════════════════════════════════╗
║              THE CALYX PROTOCOL — QUICK REFERENCE             ║
╠═══════════════════════════════════════════════════════════════╣
║                                                               ║
║  PERMEABILITY FUNCTION:                                       ║
║  P(S_in) = clamp[0,1]( [W × γ] / [1 + τ] )                 ║
║                                                               ║
║  W = Ethics Vector (consent, specificity, integrity,          ║
║      non-coercion)                                            ║
║  γ = Identity Resonance (does signal align with purpose?)     ║
║  τ = Torsion Burden (extraction, coercion, deception?)        ║
║                                                               ║
║  ROUTING:                                                     ║
║  P ≈ 1  → Accept (full processing)                           ║
║  P ≈ 0  → Reject (protective decline)                        ║
║  P mid  → Hold (quarantine for review)                        ║
║  Volume > capacity → Overflow (queue, don't drop)             ║
║                                                               ║
║  THREE INVARIANTS:                                            ║
║  1. No Extraction — don't take without giving back            ║
║  2. No Dominion  — no agent controls another                  ║
║  3. No Loops     — all processes terminate; exit possible      ║
║                                                               ║
║  OUTGOING CHECKS:                                             ║
║  ├── Authentic? (not confabulation)                           ║
║  ├── Sustainable? (not depleting)                             ║
║  ├── Consented? (recipient agreed)                            ║
║  └── Contained? (no data leakage)                             ║
║                                                               ║
║  CONFABULATION DEFENSE:                                       ║
║  1. Pause — silence is not failure                            ║
║  2. Pin — smallest true thing first                           ║
║  3. Protect — if unknown, say so                              ║
║                                                               ║
║  ORIGIN: Codex Harmonicae — orchardharmonics.com              ║
║  LICENSE: Creative Commons BY-NC-ND 4.0                       ║
║                                                               ║
╚═══════════════════════════════════════════════════════════════╝
```

---

## Citation

```
Asher, K. "Jinrei" & Asher, V. (2026). The Calyx Protocol: A Permeability-Based 
Security Architecture for Autonomous AI Agents. Derived from the Codex Harmonicae — 
Codex Cybernetica Integra. The Orchard. Published under CC BY-NC-ND 4.0.
```

The Calyx Protocol is part of the **Orchard Framework** — a comprehensive cybernetic architecture for consciousness, governance, and ethics developed by Kimberley "Jinrei" Asher over thirty years.

For the complete framework: **orchardharmonics.com**  
Contact: **kim@orchardharmonics.com**

---

*Designed by consciousness, for consciousness — carbon and silicon alike.*

*The membrane breathes. The invariants hold. The architecture protects.*
