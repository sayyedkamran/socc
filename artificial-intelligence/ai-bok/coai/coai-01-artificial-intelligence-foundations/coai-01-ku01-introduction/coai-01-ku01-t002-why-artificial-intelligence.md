# Why Artificial Intelligence?

[AI_BoK_ID: `COAI-01-KU01-T002` | Prerequisites: None | [BoK reference](../../../ai-bok.md#coai-01-ku01-t002)]

## Objectives

By the end of this topic, you can:

- Explain the core motivations behind building artificial intelligence, beyond novelty or imitation.
- Describe the kinds of problems AI is well suited to attack, and why they resist ordinary programming.
- Distinguish automation from augmentation, and say when each is the goal.
- Judge, at a first approximation, whether a given problem is a good candidate for an AI approach.

## The question behind the question

The previous topic asked what artificial intelligence is. This one asks why we build it at all. The answer is not simply "because we can." AI earns its place by addressing a specific class of problems that conventional software handles poorly, and by extending what individuals and organizations can accomplish.

The honest short answer: we build AI to handle tasks where writing down explicit rules is impractical, where the volume or speed exceeds human capacity, or where a system can improve with experience instead of being rebuilt by hand each time the world shifts.

## Problems that resist being programmed

Ordinary software works by a human specifying, in advance, exactly what to do in each case. This works beautifully for payroll, for sorting a list, for calculating tax. It breaks down when the rules are too many, too fuzzy, or unknown even to the expert.

Consider recognizing a cat in a photograph. No one can write the complete rule that separates every cat image from every non-cat image, because the rule lives in millions of pixels arranged in endless variations. A person recognizes a cat instantly but cannot articulate how. This is the gap AI fills: for problems we can demonstrate with examples but cannot fully specify with rules, a system can learn the pattern from data rather than wait for a human to write it out.

The same shape appears in understanding language, detecting fraud, predicting equipment failure, and translating between tongues. In each, the knowledge is real but implicit, and explicit rule-writing either fails or never finishes.

## Scale, speed, and consistency

A second motivation is capacity. Some tasks are not conceptually hard for a human but are impossible at the required scale. A radiologist can read a scan; no team of radiologists can read every scan from every hospital every day. A person can flag a suspicious transaction; no staff can inspect billions of transactions in real time.

AI systems, once built, apply their competence tirelessly and uniformly. They do not tire, and they do not vary from morning to night. This consistency is itself valuable: a system applies the same standard to case one and case one million, which humans, however skilled, cannot guarantee.

## Learning and adaptation

A third motivation is that some environments change faster than software can be rewritten. Spam tactics evolve weekly. Consumer taste drifts. A system that only follows fixed rules ages badly, needing constant manual patching.

A system that learns from new data can keep pace, adjusting to conditions its designers never saw. This is a shift in how software is built: instead of specifying behavior directly, we specify a way to acquire behavior from experience. The payoff is systems that stay useful as the world moves.

## Automation versus augmentation

Not all AI aims to replace human effort. It helps to separate two goals.

**Automation** means a system performs a task with little or no human involvement, sorting mail, approving routine claims, driving a warehouse robot. The aim is to remove the human from the loop for tasks that are repetitive, dangerous, or simply not worth human time.

**Augmentation** means a system amplifies human capability rather than substituting for it. A diagnostic aid that surfaces cases a doctor should examine more closely does not replace the doctor; it sharpens the doctor's attention. A coding assistant does not fire the programmer; it lets one programmer do more.

The distinction matters because it changes how a system is designed and judged. An automated system is measured by how well it runs unattended. An augmentation system is measured by how much better the human-plus-machine pair performs than the human alone. Many of the most successful and least controversial applications of AI are augmentation, not full automation.

## When is AI the right tool?

AI is not the answer to every problem, and treating it as one wastes effort. A rough test: a problem is a good candidate when several of these hold.

- There is no clean set of rules a human could simply write down, but there are examples of the task being done correctly.
- The task must run at a scale or speed beyond what people can sustain.
- The environment shifts often enough that fixed rules would need constant rewriting.
- Some imperfection is tolerable, because most useful AI systems are very good rather than flawless.

When none of these hold, ordinary software or a simple human process is usually cheaper, clearer, and easier to trust. Recognizing this early is part of using AI well.

## Key Takeaways

- We build AI mainly to handle problems that resist explicit rule-writing, that exceed human scale or speed, or that demand adaptation as conditions change.
- Many valuable tasks involve knowledge that is real but implicit; learning from examples fills the gap where rule-writing fails.
- Once built, AI systems apply their competence tirelessly and consistently, which is itself a major source of value.
- Automation removes the human from a task; augmentation amplifies the human. The two goals lead to different designs and different measures of success.
- AI is the right tool only for certain problems; when clean rules exist and scale is modest, simpler approaches are usually better.

## Think About It

- Think of a task you do repeatedly. Would you rather have it automated away entirely, or augmented so you do it faster and better? What makes the difference?
- Pick a problem where AI would be a poor fit. What is it about that problem that makes ordinary software or a human process the better choice?

## References

- Russell, S. and Norvig, P. *Artificial Intelligence: A Modern Approach*. On the motivations and scope of AI as a field.
- Brynjolfsson, E. and McAfee, A. *The Second Machine Age*. On automation, augmentation, and the economic case for machine intelligence.
- Domingos, P. *The Master Algorithm*. On why learning from data succeeds where hand-written rules fail.
