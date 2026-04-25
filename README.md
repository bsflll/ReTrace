# ReTrace
Counterfactual Reasoning for Vision-Based Robot Recovery

## Summary
Vision-language-action (VLA) policies fail silently. When a grasp slips or an action doesn't work, they have no mechanism to understand what went wrong visually or how to recover.

ReTrace trains an LLM to do counterfactual physical reasoning on visual robot failures. When execution stalls or confidence drops, the system triggers slow, deliberate reasoning that:

- Watches the visual trajectory and explains what went wrong
- Considers counterfactuals ("what if I had approached from a different angle?")
- Proposes actionable visual recovery plans the policy can condition on

## What makes this different
**Visual reasoning isn't classification, it's counterfactual.** You need to answer "what would the scene look like if I'd grasped 2cm to the left?" - that requires visual physics intuition, not just pattern matching on frames.

**Temporal credit assignment from video is hard.** A task fails at frame 470, but which of the previous frames shows the actual mistake? Maybe frame 120 shows the object placed in a bad orientation that only mattered later.

**The reasoning has to be visually grounded and actionable.** "The grasp failed because the mug was slippery" is useless. "Retry with a pinch grasp on the handle visible at the right edge of frame, approach from 15° left" is useful.

## Plan

### Phase 1: Visual Data Collection
- Run VLA policies in simulation (LIBERO, RoboMimic)
- Collect thousands of failure trajectories as video + proprioception
- For each failure, generate counterfactual visual rollouts by rewinding and perturbing actions in sim

### Phase 2: Visual Reasoning Trace Generation
- Use counterfactual video data to generate reasoning traces
- Format: "Looking at the trajectory, the grasp failed because X. The counterfactual rollout shows Y would have worked. Next attempt should do Z based on current visual state."

### Phase 3: Training
- Finetune or midtrain a vision-language model on counterfactual visual reasoning traces
- Experiment with data mix and scaling

### Phase 4: Evaluation
- Does triggering the visual reasoning module improve task success rate on held-out failures?
- Ablations: How much counterfactual video data is needed? Does reasoning transfer across robot morphologies and camera viewpoints? Sim2real visual transfer?

## Novel Contribution
First work to show that vision-language reasoning trained on counterfactual robot video actually improves recovery rates, vs using off-the-shelf VLMs as high-level planners.
