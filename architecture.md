# Epistemic Correction Bot
// architecture v0.1 – April 2026

## // DESIGN GOAL
This architecture does not attempt to solve for shared training bias or ground truth verification. It addresses two specific failure modes:

1. **Sycophancy:** Models default to agreement, flattery, and user-pleasing behavior.
2. **Format Drift:** Models blend advocacy and criticism, present estimates as facts, and drop epistemic rigor when stakes seem low.

The design forces structured opposition and removes the model's discretion over whether rigor applies.

---

## 1. USER INPUT
**Raw Query**
User submits proposition in first person.
No framing constraints required.

---

## 2. FRAMING CONVERTER (Bengio Node)
**First Person → Third Party**
"I think X" → "A colleague proposes X"
Strip emotional investment. No editorializing.

Purpose: Reduce sycophancy by creating distance between the user's identity and the claim being evaluated. Named after Yoshua Bengio's technique of competing instances with no shared context.

---

## 2.5. ORCHESTRATOR
**Workflow Manager**
Receives the framed claim from the Bengio node.
Fans out to Steelman and Critique in parallel.
Collects both outputs.
Routes to Synthesis.
Feeds Synthesis output to Presentation.
Returns final labeled response.

Does not modify content. Coordinates flow.

---

## 3a. LLM A — STEELMAN
**Find the Case For**
Strongest supporting evidence. Charitable interpretation.

## 3b. LLM B — CRITIQUE
**Find the Case Against**
Weakest assumptions.
Disconfirming evidence.
Ways it could fail.

⚠️ designed opposition — not duplicate runs

---

## 4. SYNTHESIS LAYER (Judge)
**Evaluates the Outputs of Both Sides**
Does not average. Does not split the difference.

Weighs evidence quality, reasoning strength, and source reliability.
Issues a finding, not a compromise.

- One side clearly stronger → say so, with reasoning
- Both sides have valid evidence → identify what survives from each
- Disagreement is a framing artifact → resolve it
- Value difference, not factual → flag explicitly, don't adjudicate

If the claim is "the earth is flat" and the critique correctly identifies overwhelming evidence for an oblate spheroid, the finding is not "truth somewhere in the middle." The finding is that the steelman side has no surviving evidence. Local flatness in Dallas is a separate, valid observation that doesn't support the original claim.

*Risk: judge shares same training bias as both advocates. A biased judge is not neutral.*

---

## 5. PRESENTATION LAYER
**Primer Filter**
Applies epistemic standards: confidence levels, fact vs inference, calibrated uncertainty. Shapes presentation only.

*Primer prompt applied here*

---

## 6. OUTPUT
**Epistemically Labeled Response**
Explicit confidence levels. Divergence flags. What survived both framings vs what appeared in only one.

---

## // KNOWN VULNERABILITIES
- **Shared training bias**
  Both instances may converge on same wrong answer. Convergence ≠ truth.
  This architecture does not attempt to solve this. A separate approach (query classification with specialist API routing) is under development.

- **False neutrality**
  Synthesis of two biased outputs ≠ unbiased.
  Flat earth: if both say flat, truth not in middle.

- **Synthesis layer bias**
  Synthesis LLM subject to same substrate bias. Misclassifying disagreement type causes errors.

- **Primer reach limit**
  Cannot correct errors introduced at synthesis or raw output layers.

- **Cost per query**
  4–5 LLM calls vs 1. Value proposition must justify unit economics.

---

## // STATUS
Design phase. Not implemented. Published as a statement of intent and an invitation to critique.