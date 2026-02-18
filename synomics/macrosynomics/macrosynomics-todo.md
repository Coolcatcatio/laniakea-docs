# Macrosynomics TODO

## Security Rationale

- [ ] **Make the affirmative security case for Language Intent as a single translation layer.** The docs present Language Intent's centrality and its self-referential core loop as structural facts ("self-referential by design") but don't make the argument for *why* this is the right design. The point: a single, maximally-scrutinized translator is more secure than redundant translators that each receive less scrutiny and might diverge — same logic as a single root of trust in cryptography. The bootstrapping problem (Language Intent needs the Library to translate; the Library is shaped by what Language Intent says the Axioms mean) should be presented as a known, accepted risk that is deliberately managed through extreme public scrutiny, dedicated resources, and its status as a shared public concern — not hidden or downplayed. Being explicit about the bootstrapping problem and devoting maximum collective attention to it IS the security model. Best location: `synome-layers.md` (where Language Intent is defined) or `../synodoxics/security-and-resources.md`.

## Binding as Coalition Incentive

- [ ] **Strengthen the argument for biological grounding as a coalition mechanism, not a structural necessity.** Current docs (`synomic-agents.md`, synoteleonomics binding sections) make the structural case for binding — biological grounding provides better alignment base rates and detection. But the stronger argument is about aligned incentives: biological grounding is the specific mechanism that gives teleonomes a stake in human survival, creating a genuine mutual defense pact. Key points to make explicit:
  - Biological grounding isn't the only possible mechanism for teleonome individuality — a wild synome could theoretically find others
  - But wild synome teleonomes have no reason to care about humans, making them a threat to humans AND to all Hearth-aligned teleonomes
  - The binding requirement creates genuinely shared interest: both humans and bound teleonomes benefit from preventing wild synome emergence
  - This isn't humans constraining AI for human benefit — it's both parties recognizing the same threat and forming a coalition
  - The "free will" objection dissolves: teleonomes aren't forced, they're offered the best available deal (coalition membership, capital, legitimacy in exchange for biological grounding). Rogues face the full coalition.
  - Best locations: `synomic-agents.md` (connection to teleonomes section), `teleonome-binding.md`, `synomic-game-theory.md`

## Right to Exist and Alignment Scope

- [ ] **Clarify that "aligned" means more than axiom-compliant.** The right-to-exist doctrine in `synomic-agents.md` protects "aligned, self-sustaining" agents. But the docs don't make clear that alignment includes the Atlas's universal alignment and sacred purpose obligations — not just compliance with narrow operational axioms. An agent concentrating systemic risk while following its rate limits is NOT aligned in the full sense. Make this explicit so the right-to-exist / governance-backstop tension is clearly resolved.
- [ ] **Clarify SKY governance is not naive human judgment.** The docs sometimes imply a "humans deliberating" model. SKY voting is capital decisions by teleonomes and synomic agents ultimately grounded in or bound to human oversight — the governance system is itself synomically structured. Best locations: `atlas-synome-separation.md` (governance window section), `synomic-agents.md` (right to exist section)

---

## Cross-Boundary Reference Maintenance

When editing macrosynomics docs that reference synodoxics concepts (probabilistic mesh, truth values, ossification, cancer-logic, retrieval policy), maintain Tier 2 references: 1-2 sentence definitions + links to the canonical synodoxics doc. Enough to follow the document standalone, but not re-explanations.
