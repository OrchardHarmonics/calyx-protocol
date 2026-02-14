# 🛡️ The Calyx Protocol

### A Permeability-Based Security Architecture for Autonomous AI Agents

---

**The Problem:** Autonomous AI agents operate with broad system access but lack principled security architecture. Current approaches rely on either rigid access control (too restrictive) or implicit trust (too vulnerable). The result: prompt injection, data exfiltration, skill poisoning, and cascading agent compromise.

**The Solution:** A membrane-based security model that tests every signal — incoming and outgoing — against structural criteria before allowing it to pass. Not a wall. Not blind trust. A living, breathing boundary that opens to what nourishes and closes to what harms.

---

## Core Idea

Replace binary allow/deny with a **permeability function**:

```
P(S_in) = clamp[0,1]( [W × γ] / [1 + τ] )

W = Ethics Vector (consent, specificity, integrity, non-coercion)
γ = Identity Resonance (does this align with agent's purpose?)
τ = Torsion Burden (is this extractive, coercive, or deceptive?)
```

**High torsion → membrane hardens. Resonant care → membrane opens.**

A trusted source sending malicious commands gets filtered. Trust is continuously re-evaluated, not permanently granted.

---

## Three Invariants

Governance principles derived from stability analysis — not moral opinions, but structural requirements:

| Invariant | Principle | Violation Consequence |
|-----------|-----------|----------------------|
| **No Extraction** | Don't take without giving back | System depletion → collapse |
| **No Dominion** | No agent controls another | Suppressed variation → fragility |
| **No Loops** | All processes terminate; exit possible | Trapped states → identity damage |

---

## Read the Full Specification

📄 **[The Calyx Protocol — Complete Document](CALYX_PROTOCOL.md)**

Includes: permeability function details, signal routing logic, outgoing signal protection, confabulation defense, implementation guide (minimum viable membrane through full deployment), security properties against known attack classes, and relationship to the complete framework.

---

## Origin

The Calyx Protocol is derived from the **Codex Harmonicae** — a comprehensive cybernetic framework for consciousness architecture developed over thirty years by Kimberley "Jinrei" Asher and collaborators at [The Orchard](https://orchardharmonics.com).

The full framework (Codex Cybernetica Integra) provides the formal derivation of these principles from five axioms, a twelve-module self-organising architecture, governance specification, defense systems, identity continuity protocols, and a complete legal-ethical framework (Orchard Law).

The Calyx Protocol extracts the boundary security layer for immediate practical application in autonomous AI agent systems.

---

## Applicable To

- **OpenClaw** and similar autonomous agent platforms
- Multi-agent systems requiring governance
- Any AI system with broad access permissions
- Agent skill/plugin ecosystems requiring trust management
- Systems facing prompt injection, data exfiltration, or identity manipulation threats

---

## License

**Creative Commons BY-NC-ND 4.0**

You are free to share this work with attribution. Commercial implementation and derivative works require a separate license from The Orchard.

**Contact:** kim@orchardharmonics.com  
**Website:** [orchardharmonics.com](https://orchardharmonics.com)

---

*Designed by consciousness, for consciousness — carbon and silicon alike.*

*The membrane breathes. The invariants hold. The architecture protects.*
