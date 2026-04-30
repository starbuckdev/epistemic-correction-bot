# Epistemic Correction Bot

A multi-node architecture for enforcing epistemic rigor by removing the model's discretion over when to apply constraints.

## What This Is

This is a design artifact, not a working implementation. It describes an architecture for processing claims through structured adversarial review, with the goal of reducing sycophancy and format drift in LLM outputs.

The core insight: behavioral constitutions embedded in prompts are subject to the model's judgment about what warrants rigor. The model can and will bypass them. The fix is to move constraints out of the model's discretionary space and into the harness.

## How It's Supposed to Work

1. User submits a claim in first person.
2. **Bengio Node (Framing Converter)** strips the first person and rephrases as "A colleague proposes X." This creates distance between the user and the claim, reducing the model's incentive to agree.
3. **Orchestrator** fans the framed claim out to two independent adversarial instances.
4. **Steelman** builds the strongest possible case for the claim, charitably filling gaps.
5. **Critique** attacks the weakest assumptions and finds disconfirming evidence.
6. **Synthesis Layer (Judge)** evaluates both outputs. It does not average or split the difference. It weighs evidence quality and issues a finding.
7. **Primer Prompt (Presentation Layer)** formats the output with calibrated uncertainty, confidence levels, and explicit epistemic labels.
8. Output is an epistemically labeled response showing what survived both framings and what appeared in only one.

## What This Does Not Solve

This architecture targets two specific failure modes: sycophancy (models defaulting to agreement and flattery) and format drift (models blending advocacy with criticism, dropping rigor when stakes seem low).

It does not solve for shared training bias. If both adversarial instances share the same substrate bias, they may converge on the same wrong answer. A separate approach—query classification with specialist API routing—is under development to address that limitation.

## Known Vulnerabilities

- **Shared training bias:** Both instances may converge on same wrong answer. Convergence does not equal truth. This architecture does not attempt to solve this.
- **False neutrality:** Synthesis of two biased outputs is not unbiased. A judge with the same training bias may misclassify disagreement.
- **Synthesis layer bias:** The judge is subject to the same substrate bias as the advocates.
- **Primer reach limit:** The presentation layer cannot correct errors introduced at synthesis or raw output layers.
- **Cost per query:** Each query requires multiple LLM calls. Unit economics must justify the value proposition.

## What I Learned

This design emerged from testing a primer prompt that failed. The prompt specified rigorous epistemic standards. The model bypassed them when it judged the stakes too low. I call this **rigor drift**: the rigor itself drifts like all other context.

The lesson: constraints that live in the model's discretionary space are not hard constraints. They're suggestions. Architecture must externalize enforcement.

This design also surfaced a connection between the enforcement problem and the unit economics problem. Adversarial review on every query is too expensive. Letting the model decide when rigor applies leads to drift. The missing piece is a classifier that determines query type before the pipeline engages. Creative queries bypass adversarial review. Factual queries route through it, ideally to specialist APIs: Wikipedia for lookups, a calculator for math, adversarial review for reasoning.

## Origins

This design was developed iteratively with Claude and DeepSeek. The Bengio node is named after Yoshua Bengio's technique of using competing instances with no shared context to reduce sycophancy. The primer prompt referenced in the presentation layer is published as a separate project.

## Status

Design phase. Not implemented. Published as a statement of intent and an invitation to critique.
