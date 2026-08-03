# AI vs. Related Fields

[AI_BoK_ID: `COAI-01-KU01-T005` | Prerequisites: None | [BoK reference](../../../ai-bok.md#coai-01-ku01-t005)]

## Objectives

By the end of this topic, you can:

- Place machine learning and deep learning correctly inside artificial intelligence as nested subsets.
- Distinguish AI from neighboring fields that overlap with it but are not contained in it, such as data science and robotics.
- Explain what cognitive science and expert systems each contribute, and how they relate to AI.
- Use these terms precisely, avoiding the common habit of treating them as interchangeable.

## Why the terms get tangled

Artificial intelligence, machine learning, deep learning, data science, robotics: in casual use these words are thrown around as near-synonyms, and the result is confusion. Someone says "AI" and means a neural network; someone else says "machine learning" and means all of AI; a headline says "robot" and means a chatbot with no body at all.

Getting the relationships straight is worth the effort, because the terms are not interchangeable and the differences carry real consequences for how systems are built and judged. This topic sorts them into two groups: fields that sit inside AI as subsets, and fields that sit alongside AI as overlapping neighbors. Keeping that structure in mind is the key to using the vocabulary well.

## The nested core: AI, machine learning, deep learning

The cleanest relationships in the whole landscape are these three, and they nest one inside the next like rings.

**Artificial intelligence** is the broadest. As Topic 01: What is Artificial Intelligence? established, it is the study and construction of systems that perform tasks requiring intelligence. This includes approaches that do not learn at all, such as hand-crafted rule systems, search algorithms, and logic-based reasoning. AI is the whole field.

**Machine learning** is a subset of AI: the approach in which systems improve at a task by learning from data rather than following only rules a human wrote. All machine learning is AI, but not all AI is machine learning. A chess program using pure search is AI without being machine learning; a spam filter trained on examples is both.

**Deep learning** is a subset of machine learning: the family of methods built on neural networks with many layers, which learn layered representations directly from raw data. All deep learning is machine learning, and therefore all deep learning is AI, but plenty of machine learning is not deep, such as decision trees or linear models.

So the picture is three concentric rings: deep learning inside machine learning inside artificial intelligence. When you keep this nesting straight, a great deal of loose talk sorts itself out. Someone who says "AI" when they specifically mean a deep neural network is naming the outermost ring when they mean the innermost. The course treats each ring in depth later, machine learning in Phase 4 and deep learning in Phase 5, but the containment relationship is worth fixing now.

## The overlapping neighbors

Other fields are commonly mentioned in the same breath as AI but do not nest neatly inside it. They overlap AI, sharing tools and goals in places, while extending into territory that is not AI at all.

**Data science** is the discipline of extracting insight and value from data. It overlaps AI heavily, because machine learning is one of its central tools, but it is not a subset of AI, and AI is not a subset of it. Much of data science is about gathering, cleaning, exploring, and visualizing data, and about statistical analysis that involves no learning system at all. Conversely, much of AI, such as logic-based reasoning or robotics control, is not data science. They meet at machine learning and part ways elsewhere.

**Robotics** is the field of building physical machines that sense and act in the world. Its overlap with AI is real but partial. A robot needs perception and decision-making, which is where AI enters, giving the robot its "brain." But robotics also covers mechanical design, actuators, power, and control hardware, none of which is AI. And a great deal of AI has no physical body at all: a language model or a fraud detector is pure software. So robotics and AI intersect at the control-and-perception layer while each keeps a large territory of its own.

**Cognitive science** is the interdisciplinary study of the mind, drawing on psychology, neuroscience, linguistics, and philosophy. Its relationship to AI is a two-way exchange rather than a containment. Cognitive science asks how natural minds work; AI asks how to build intelligent systems. Each has informed the other: theories of human cognition have inspired AI architectures, and AI models have served as testbeds for theories of mind. This connects to the "thinking humanly" quadrant from Topic 01: What is Artificial Intelligence?, where mirroring human cognition is the explicit goal. But cognitive science is a science of existing minds, not primarily an engineering discipline for building new ones.

## Expert systems: a historical piece of AI

Expert systems deserve separate mention because they show that AI is older and broader than today's learning-driven picture suggests.

An expert system captures the knowledge of a human specialist as a large collection of rules, typically of the form "if these conditions hold, then conclude or do this," and applies them to reach conclusions in a narrow domain such as medical diagnosis or equipment configuration. Crucially, expert systems are AI but not machine learning: their knowledge is hand-encoded by human experts, not learned from data. They were the dominant commercial form of AI in the 1980s.

Their inclusion under AI reinforces the central point of the nested rings. AI is not synonymous with machine learning. It is the larger field, and rule-based approaches like expert systems are a genuine part of it, even though the modern center of gravity has shifted toward learning. The course returns to this history in Topic 02 of the next knowledge unit, on the history and evolution of AI, where the rise and fall of expert systems is part of a longer story.

## Putting it together

A single mental map holds all of this. At the center are three nested rings: deep learning within machine learning within artificial intelligence. Around them sit overlapping neighbors, data science, robotics, and cognitive science, each sharing a region with AI while keeping territory of its own. And within AI, alongside machine learning, sit older approaches such as expert systems that learn nothing yet are unmistakably AI.

Holding this map lets you hear a claim like "our product uses AI" and ask the sharper questions: Is it learning from data, or following rules? Is it a subset method like deep learning, or a neighbor like data analytics wearing an AI label? Precision here is not pedantry. It is the difference between understanding a system and being sold one.

## Key Takeaways

- Artificial intelligence, machine learning, and deep learning nest as concentric subsets: deep learning within machine learning within AI. All deep learning is machine learning is AI, but not the reverse.
- AI includes non-learning approaches such as search, logic, and rule-based systems, so it is strictly broader than machine learning.
- Data science, robotics, and cognitive science overlap AI without being contained in it; each shares tools or goals with AI while keeping a large territory of its own.
- Expert systems are AI but not machine learning, since their knowledge is hand-encoded rather than learned, a reminder that AI predates and exceeds the current learning-driven picture.
- Using these terms precisely lets you ask sharper questions about any system claimed to "use AI."

## Think About It

- Someone tells you their app is "powered by AI." What questions would you ask to place it on the map from this topic, and why would the answers change how much you trust the claim?
- Data science and AI overlap at machine learning but diverge elsewhere. Can you name a task that is clearly data science but not AI, and one that is clearly AI but not data science?

## References

- Russell, S. and Norvig, P. *Artificial Intelligence: A Modern Approach*. On the scope of AI and its relation to subfields.
- Goodfellow, I., Bengio, Y., and Courville, A. *Deep Learning*. On the nesting of deep learning within machine learning within AI, presented in the introduction.
- Jackson, P. *Introduction to Expert Systems*. On rule-based expert systems as a form of AI distinct from machine learning.
