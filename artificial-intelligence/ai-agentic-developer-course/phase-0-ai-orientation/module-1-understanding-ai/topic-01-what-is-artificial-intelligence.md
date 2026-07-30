---
BoK-ID: COAI-01-KU01-T01
Prerequisites: None
---

# What is Artificial Intelligence?

`COAI-01-KU01-T01` | Prerequisites: **None** | [BoK reference](../../../ai-bok/ai-bok.md#coai-01-ku01-t01)

## Objectives

By the end of this topic, you can:

- State a working definition of artificial intelligence and explain why no single definition is universally accepted.
- Distinguish the four classic ways AI has been defined, along the axes of thinking versus acting and human performance versus rationality.
- Separate artificial intelligence from natural intelligence, and say what the word "artificial" is really claiming.
- Identify the qualities that make a system count as intelligent, rather than merely automated.

## A question that resists a simple answer

Ask ten researchers what artificial intelligence is and you may get ten different answers. This is not a sign of a confused field. It reflects something deeper: we struggle to define "intelligence" even in humans, so defining it in machines inherits that difficulty and adds new ones.

For a working start, we can say that artificial intelligence is the study and construction of systems that perform tasks we would normally consider to require intelligence: understanding language, recognizing images, making decisions under uncertainty, learning from experience. This is deliberately loose, and by the end of this topic you will see why a sharper definition forces choices about what you actually value in a system.

One useful observation is that the meaning of AI keeps moving. Tasks once considered the height of machine intelligence, like playing chess or doing arithmetic faster than any human, are now treated as ordinary software. This pattern is common enough to have a name, the "AI effect": once a problem is solved, it stops feeling like intelligence. That moving target is part of why the definition stays unsettled.

## Four ways to define AI

A widely used framework, popularized by Stuart Russell and Peter Norvig, organizes definitions of AI along two axes. The first axis asks whether we care about **thought processes and reasoning**, or about **behavior**. The second asks whether we measure success against **human performance** or against an ideal standard of **rationality**, meaning doing the right thing given what is known. Crossing these axes gives four positions.

**Thinking humanly.** This view aims to build systems that mirror how humans actually think, drawing on cognitive science and studies of the mind. Success means the system's internal reasoning resembles a person's, not just its output.

**Thinking rationally.** This tradition descends from logic. It seeks systems that reason correctly according to formal laws of thought, arriving at valid conclusions from given premises. The emphasis is on provably sound reasoning rather than human imitation.

**Acting humanly.** Here the test is behavioral and human-centered: can a system act in a way indistinguishable from a person? The Turing Test belongs to this quadrant. Internal mechanism is irrelevant; only the observable behavior counts.

**Acting rationally.** This view, which much modern AI adopts, focuses on building agents that take actions expected to achieve their goals as well as possible given their knowledge. It does not require human-like thinking or human-like behavior, only effective, goal-directed action. This is often the most practical framing for engineering real systems, because it gives a clear standard: did the system do the right thing?

No quadrant is the single correct one. Which you emphasize depends on your purpose. A researcher modeling human cognition cares about thinking humanly; an engineer shipping a recommendation system cares about acting rationally.

## Artificial versus natural intelligence

Natural intelligence is the intelligence found in living organisms, most notably humans, produced by biological brains through evolution and development. Artificial intelligence is intelligence exhibited by human-made systems.

The word "artificial" is doing specific work here. It does not necessarily mean fake or lesser. It means constructed rather than grown, engineered rather than evolved. An artificial system may reach a goal by means entirely unlike biology: a chess engine does not think like a grandmaster, and an image classifier does not see like an eye and brain. The results can match or exceed human performance in narrow areas while the underlying method looks nothing like the natural version.

This distinction matters because it frees us from assuming machine intelligence must copy human intelligence. Airplanes fly without flapping wings. Similarly, useful AI need not replicate the brain to be genuinely capable.

## What makes a system intelligent?

If automation alone were enough, a thermostat or a calculator would qualify, and the word would lose its meaning. Several qualities tend to distinguish systems we call intelligent:

- **Perception:** taking in information from the environment, such as text, images, or sensor data.
- **Reasoning and decision-making:** drawing conclusions and choosing actions, often under uncertainty and incomplete information.
- **Learning:** improving performance from data or experience rather than following only fixed, hand-written rules.
- **Adaptation:** adjusting behavior when conditions change instead of breaking on anything unforeseen.
- **Goal-directed behavior:** acting in service of an objective, so that actions can be judged as better or worse relative to that goal.

A plain calculator performs none of these in a meaningful sense; it executes fixed operations. A spam filter that learns which messages you treat as junk, adapts as spammers change tactics, and acts to achieve the goal of a clean inbox sits much closer to what we mean by intelligent, even though it is far simpler than a human.

The line is not sharp, and that is expected. Intelligence is better seen as a spectrum of capability than as a switch that is either on or off. Much of this course is about moving systems along that spectrum deliberately.

## Grounding it in familiar systems

Consider three everyday systems through the lens above.

A **spam filter** perceives incoming email, reasons about the probability that a message is unwanted, learns from examples of spam and non-spam, and acts by sorting messages. It is narrow but recognizably intelligent in the "acting rationally" sense.

A **route planner** perceives your location and the road network, reasons over many possible paths, and acts by choosing one expected to minimize time. It pursues a clear goal and does so close to optimally, a clean example of rational action.

A **chatbot** built on a modern language model acts humanly in the Turing sense, producing fluent, human-like responses. Whether it "thinks" in any human way is a separate and contested question, which is exactly why the acting-versus-thinking distinction is useful: it lets us praise the behavior without overclaiming about the internal process.

## Key Takeaways

- Artificial intelligence has no single agreed definition; the ambiguity comes from the difficulty of defining intelligence itself, and from the "AI effect" that reclassifies solved problems as ordinary software.
- A useful framework defines AI along two axes, thinking versus acting and human performance versus rationality, yielding four views: thinking humanly, thinking rationally, acting humanly, and acting rationally.
- Modern engineering often favors "acting rationally": building agents that take goal-achieving actions given their knowledge, without requiring human-like thought or behavior.
- "Artificial" means constructed rather than grown; machine intelligence need not imitate biology to be capable.
- Systems we call intelligent tend to combine perception, reasoning, learning, adaptation, and goal-directed behavior, and intelligence is best understood as a spectrum rather than a binary.

## Think About It

- A calculator computes far faster than any human, yet we rarely call it intelligent, while a system that occasionally makes mistakes learning your preferences feels more so. What does that reveal about what we actually mean by intelligence?
- Pick a system you used today. Which of the four quadrants best describes what it does, and would you defend calling it intelligent?

## References

- Russell, S. and Norvig, P. *Artificial Intelligence: A Modern Approach*. The four-quadrant framework of defining AI.
- McCarthy, J. "What Is Artificial Intelligence?" An accessible essay from a founder of the field.
- Turing, A. "Computing Machinery and Intelligence." The origin of the imitation game, relevant to the "acting humanly" view.

## Next Topic

[COAI-01-KU01-T02: Why Artificial Intelligence?](topic-02-why-artificial-intelligence.md)
