# Intelligence in Machines

[AI_BoK_ID: `COAI-01-KU01-T004` | Prerequisites: None | [BoK reference](../../../ai-bok.md#coai-01-ku01-t004)]

## Objectives

By the end of this topic, you can:

- Give a functional account of intelligence that applies to machines, without smuggling in human assumptions.
- Explain rationality and goal-directed behavior, and why they anchor the engineering view of machine intelligence.
- Separate intelligence from consciousness, and say why a system can have one without the other.
- Compare the main perspectives on whether, and in what sense, machines can be intelligent.

## A harder question than it looks

Topic 03: Characteristics of AI Systems described what these systems do: perceive, reason, learn, act, adapt, and operate with some autonomy. A fair next question is whether doing those things amounts to being intelligent, or whether the word is just a flattering label we attach to clever machinery.

This is not idle philosophy. How you answer shapes what you try to build and how you judge success. If intelligence means "thinks like a human inside," you will measure machines against the human mind and most will fail. If intelligence means "does the right thing to achieve its goals," you will measure by behavior and results, and the question becomes tractable. This topic works through those options and settles on the one this course mostly uses, while being honest about what it leaves unresolved.

## A functional view of intelligence

The most useful working definition for our purposes is functional: intelligence is the ability to achieve goals across a range of situations, especially novel ones, by acquiring and using knowledge.

Three parts of that deserve attention. First, it is about achieving goals, not about any particular inner experience. Second, it emphasizes a range of situations, including new ones, because handling only cases you were explicitly prepared for is closer to a lookup table than to intelligence. Third, it involves acquiring and using knowledge, which connects intelligence to learning, the characteristic that Topic 03: Characteristics of AI Systems singled out as central.

This definition is deliberately silent on how the goal is achieved. It does not require the system to reason like a person, feel anything, or understand in any deep sense. It asks only whether the system reliably brings about the right outcomes across varied conditions. That silence is a feature: it lets us assess machines on their own terms.

## Rationality and goal-directed behavior

The functional view leans heavily on two linked ideas: rationality and goal-directed behavior.

A system exhibits goal-directed behavior when its actions are organized around achieving an objective, so that we can meaningfully call some actions better and others worse relative to that goal. A thermostat has a trivial goal; a chess engine has a demanding one. What matters is that behavior is answerable to a goal at all, because that is what lets us evaluate it.

Rationality, in the AI sense, means acting so as to best achieve one's goals given one's knowledge. A rational agent, faced with what it knows and what it wants, takes the action expected to do best. This is the "acting rationally" stance first introduced in Topic 01: What is Artificial Intelligence?, and it is the backbone of the engineering view of machine intelligence. It sets a clear, checkable standard: given what the system knew, did it choose well?

Note that rationality is relative to knowledge, not to omniscience. A rational agent can make a choice that turns out badly because the world held information it could not have had. It is still rational if it acted best on what it knew. This distinction protects us from judging systems, or people, purely by outcomes they could not foresee.

## Intelligence is not consciousness

Here lies the single most common confusion for newcomers, so it is worth stating plainly: intelligence and consciousness are different things, and a system can have the first without the second.

Intelligence, on the functional view, is about capability: solving problems, achieving goals, handling novelty. Consciousness is about subjective experience, there being something it is like to be the system, an inner life of sensations and awareness. These can come apart. A calculator has a sliver of capability and, as far as anyone can tell, no experience whatsoever. A modern language model can produce strikingly capable behavior while there is no evidence it experiences anything at all.

Confusing the two leads to bad reasoning in both directions. People overclaim, treating a fluent chatbot as if it must have feelings because it talks like something that does. People also underclaim, refusing to grant that a system is doing anything intelligent because it obviously lacks an inner life. The functional view sidesteps this: it judges intelligence by what a system accomplishes and leaves the separate, genuinely hard question of machine consciousness open. This course is about building capable systems. It does not need to resolve whether machines can be conscious, and it will not pretend to.

## Different perspectives on machine intelligence

Beyond the functional view, several perspectives have shaped the debate, and knowing them helps you read the field.

The **behavioral** perspective says that if a system behaves intelligently, that is all intelligence could mean; there is nothing further to demand. The Turing Test embodies this: judge by conversation, not by inner workings. Its strength is testability. Its weakness, critics say, is that convincing behavior might be produced by something we would hesitate to call genuine understanding.

The **strong versus weak AI** distinction, associated with the philosopher John Searle, separates two claims. Weak AI holds that machines can act as if intelligent and be powerful tools for studying the mind. Strong AI holds that a suitably programmed machine would actually have a mind and understand, not merely simulate understanding. Searle's Chinese Room argument targets strong AI: it tries to show that following rules to produce correct outputs is not the same as understanding them. One need not accept the argument to find the distinction useful, because it names exactly what is contested.

The **narrow versus general** perspective concerns scope rather than depth. Almost all systems today are narrow: highly capable within a specific domain and helpless outside it. General intelligence, the flexible competence humans show across wildly different tasks, remains an open goal rather than an achievement. This distinction, which the course returns to in the topics on types of AI, matters because "intelligent at one thing" and "intelligent in general" are very different bars.

These perspectives are not mutually exclusive, and you do not have to pick one. The point is to recognize which question is on the table in any given discussion, capability, understanding, or generality, since much confusion comes from arguing past one another about different things.

## Where this course stands

For building systems, this course adopts the functional, behavior-and-goals view: a machine is intelligent to the extent that it achieves its goals well across a range of situations, including new ones. This is not a claim that the deeper questions are settled or unimportant. It is a working commitment that lets us design, measure, and improve real systems without first solving the philosophy of mind.

The harder questions, whether machines understand, whether they could be conscious, what general intelligence would truly require, are real, and Topic 09: Philosophy of Artificial Intelligence takes them up directly. Holding them open while building capable systems is not evasion. It is how the field has made progress.

## Key Takeaways

- A functional definition treats intelligence as achieving goals across varied, including novel, situations by acquiring and using knowledge, without requiring human-like inner processes.
- Goal-directed behavior lets us judge actions as better or worse; rationality means acting best given one's knowledge, and is relative to knowledge rather than to omniscience.
- Intelligence and consciousness are distinct: a system can be capable without any subjective experience, and conflating the two causes both overclaiming and underclaiming.
- Major perspectives include the behavioral view, the strong-versus-weak AI distinction, and the narrow-versus-general distinction; each foregrounds a different question.
- This course adopts the functional view for engineering purposes while leaving the deeper questions open for the philosophy topic.

## Think About It

- A system passes every behavioral test you can devise yet, you are told, has no inner experience at all. Is it intelligent? Does your answer depend on what you wanted the word to do?
- Rationality is judged relative to what a system knew, not what turned out to be true. Can you think of a decision that was rational but ended badly, or irrational but ended well? What does that separation buy us?

## References

- Russell, S. and Norvig, P. *Artificial Intelligence: A Modern Approach*. On rationality and the rational-agent view of intelligence.
- Turing, A. "Computing Machinery and Intelligence." The behavioral test for machine intelligence.
- Searle, J. "Minds, Brains, and Programs." The Chinese Room and the strong-versus-weak AI distinction.
- Legg, S. and Hutter, M. "A Collection of Definitions of Intelligence." On functional definitions of intelligence relevant to machines.
