# Characteristics of AI Systems

[AI_BoK_ID: `COAI-01-KU01-T003` | Prerequisites: None | [BoK reference](../../../ai-bok.md#coai-01-ku01-t003)]

## Objectives

By the end of this topic, you can:

- Name the core characteristics that recur across AI systems and describe what each one contributes.
- Explain how these characteristics connect into a loop, from taking in the world to acting on it.
- Distinguish autonomy as a matter of degree rather than a yes-or-no property.
- Look at a real system and identify which characteristics it has, and which it lacks.

## From "what" and "why" to "what they are made of"

Topic 01: What is Artificial Intelligence? gave a working definition and listed, in passing, some qualities that make a system intelligent. Topic 02: Why Artificial Intelligence? explained the motivations for building such systems. This topic opens the machine up and looks at the recurring parts. Almost every AI system, from a spam filter to a self-driving car, can be described in terms of a small set of characteristics. Learning to see them is a skill you will use for the rest of the course, because it lets you compare very different systems on the same terms.

These characteristics are not a rigid checklist where a real system must have all of them. They are more like a vocabulary. Some systems have every one; many have only a few. The value is in being able to say precisely what a given system does and does not do.

## Perception

Perception is how a system takes in information about its environment. Without it, a system has nothing to act on.

The form of the input varies widely. A vision system perceives images as grids of pixels. A language model perceives text as sequences of tokens. A trading system perceives streams of prices. A robot perceives its surroundings through cameras, microphones, and touch or distance sensors. In each case, raw signals from the world are converted into a form the system can process.

Perception is rarely trivial. The world arrives noisy, incomplete, and ambiguous. A photograph of a street contains far more than the system needs, and part of perceiving well is extracting what matters and discarding the rest. Much of the difficulty in AI lives right here, in turning messy input into a usable internal representation.

## Reasoning and decision-making

Once a system has taken in information, it must do something with it: draw conclusions, weigh options, and choose. This is reasoning and decision-making.

Reasoning can take many forms. A logic-based system may chain rules together to reach a conclusion. A probabilistic system may weigh evidence to estimate how likely something is. A learned model may map inputs to outputs through patterns it absorbed from data. What unites them is the move from what the system knows to what it should conclude or do.

A defining feature of real-world reasoning is that it happens under uncertainty and incomplete information. A medical system rarely has every test result; a route planner cannot know future traffic with certainty. Good AI systems do not demand perfect information before acting. They make the best decision available given what they have, which is exactly the "acting rationally" stance introduced in Topic 01: What is Artificial Intelligence?

## Learning

Learning is the capacity to improve performance from data or experience, rather than relying solely on rules a human wrote in advance.

This is the characteristic that most sharply separates modern AI from ordinary software. A traditional program does exactly what it was told and no more; to change its behavior, a human must rewrite it. A learning system adjusts itself. Shown many labeled examples, it discovers the pattern that connects input to output, and it can then handle cases it was never explicitly told about.

Not every AI system learns. Some rely entirely on hand-crafted rules and are still usefully called AI. But learning is what lets systems tackle problems too complex or too fuzzy to program directly, the very problems Topic 02: Why Artificial Intelligence? identified as AI's natural territory. Much of this course is ultimately about learning: how it works, why it works, and how to do it well.

## Action

A system that perceives, reasons, and learns but never does anything has no effect on the world. Action is the characteristic that closes the gap between thinking and consequence.

Actions differ in kind. A spam filter acts by moving a message to a folder. A recommendation system acts by choosing what to show you. A robot acts by moving a motor. A language model acts by producing text. In every case the system's internal decision becomes an outward effect.

Action is what makes a system's decisions matter, and also what makes them accountable. Because actions have consequences, the quality of a system's perception and reasoning is ultimately judged by the actions they produce.

## Adaptation

Adaptation is the ability to adjust behavior when conditions change, rather than breaking on anything unforeseen.

The world does not hold still. Spam tactics evolve, user preferences drift, road conditions shift. A rigid system that assumes a fixed world degrades as the world moves away from its assumptions. An adaptive system copes: it updates as new information arrives and keeps working under conditions its designers never explicitly anticipated.

Adaptation is closely related to learning but not identical. Learning is often how a system acquires the ability to adapt, while adaptation is the visible result, behavior that stays appropriate as the environment changes.

## Autonomy

Autonomy is the degree to which a system operates without human intervention. It is the characteristic most often misunderstood, because people treat it as a switch when it is really a dial.

At one end sits a system that only advises: a diagnostic aid that flags cases for a doctor but decides nothing on its own. In the middle sits a system that acts but under supervision, with a human able to step in. At the far end sits a fully autonomous system that senses, decides, and acts in a continuous loop with no human in the moment, such as a self-driving vehicle navigating traffic.

Higher autonomy is not automatically better. More autonomy means less human oversight, which raises the stakes of every mistake. The right level of autonomy depends on how costly errors are and how much trust the system has earned. This tension between capability and control returns throughout the course, especially when we reach agents that act over many steps.

## The characteristics form a loop

These characteristics are easiest to grasp separately, but in a working system they connect. A system perceives its environment, reasons about what it perceives, acts on that reasoning, and observes the results of its action, which become new perceptions. Learning and adaptation improve the loop over time, and autonomy sets how much of the loop runs without a human.

Picture a self-driving car. It perceives the road through sensors, reasons about the safest next move, acts by steering and braking, and immediately perceives the outcome, feeding the next cycle. Over many miles and much data, learning sharpens its judgment. Its autonomy determines how much a human driver must stay involved. Every characteristic in this topic appears in that single continuous loop.

This perceive-reason-act cycle is a template you will meet again in far more detail when the course reaches intelligent agents. For now it is enough to see that the characteristics are not a loose list but parts of one integrated process.

## Key Takeaways

- AI systems can be described through a recurring set of characteristics: perception, reasoning and decision-making, learning, action, adaptation, and autonomy.
- These characteristics form a vocabulary, not a mandatory checklist; real systems have some and lack others, and naming which is a core analytical skill.
- Perception turns messy real-world input into usable form; reasoning turns it into decisions, typically under uncertainty; action turns decisions into consequences.
- Learning lets a system improve from data instead of being rewritten by hand, and adaptation keeps its behavior appropriate as conditions change.
- Autonomy is a matter of degree, not a binary, and more autonomy raises both capability and the stakes of error.
- In a working system the characteristics connect into a perceive-reason-act loop that learning and adaptation refine over time.

## Think About It

- Take a system you know well and go through the six characteristics one by one. Which does it clearly have, which does it lack, and does anything about the absences surprise you?
- Autonomy raises the stakes of mistakes. For a system you would actually use, where would you set the dial, and what would the system have to prove before you moved it higher?

## References

- Russell, S. and Norvig, P. *Artificial Intelligence: A Modern Approach*. On agents and the properties that characterize intelligent systems.
- Poole, D. and Mackworth, A. *Artificial Intelligence: Foundations of Computational Agents*. On perception, reasoning, and action as components of an agent.
