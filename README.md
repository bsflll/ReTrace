# ReTrace
Counterfactual Reasoning for Robot Recovery

## Summary
Current robot policies (VLAs) fail silently. When a grasp slips or an action doesn't work, they have no mechanism to understand why it failed or how to recover.

ReTrace trains an LLM to do counterfactual physical reasoning on robot failures. When execution stalls or confidence drops, the system triggers slow, deliberate reasoning that:

- Explains what went wrong
- Considers counterfactuals ("what if I had grasped from a different angle?")
- Proposes actionable recovery plans the policy can condition on

## What makes this different
**Policy reasoning isn't classification, it's counterfactual.** You need to answer "what would have happened if I'd grasped 2cm to the left?" - that requires physics intuition, not just pattern matching.

**Temporal credit assignment is hard.** A task fails at step 47, but which of the previous 46 steps caused it? Maybe step 12 put the object in a bad orientation that only mattered later.

**The reasoning has to be actionable.** "The grasp failed because the mug was slippery" is useless. "Retry with a pinch grasp from the handle, approach from 15° left" is useful.

## Plan

### Phase 1: Data Collection
- Run VLA policies in simulation (LIBERO, RoboMimic)
- Collect thousands of failure trajectories
- For each failure, generate counterfactual rollouts by rewinding and perturbing in sim

### Phase 2: Reasoning Trace Generation
- Use counterfactual data to generate reasoning traces
- Format: "The grasp failed because X. Counterfactual Y would have succeeded. Next attempt should do Z."

### Phase 3: Training
- Finetune or midtrain an LLM on counterfactual reasoning traces
- Experiment with data mix and scaling

### Phase 4: Evaluation
- Does triggering the reasoning module improve task success rate on held-out failures?
- Ablations: How much counterfactual data is needed? Does reasoning transfer across robot morphologies? Sim2real transfer?

## Novel Contribution
First work to show that LLM reasoning trained on counterfactual robot data actually improves recovery rates, vs using off-the-shelf LLMs as high-level planners.
