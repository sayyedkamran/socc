# Topic 23: AI Agents

## Introduction

[Topic 22: Tool Use and Function Calling](topic-22-tool-use-and-function-calling.md) ended with a loop. The model reasons, emits a tool request, the harness runs the tool and hands back the result, the model reasons again, and the cycle turns until an answer is ready. It was a genuine breakthrough, the moment a sealed text engine reached past its own boundary and touched live systems. But read that loop again with a careful eye, and something quietly constrains it. Every turn was still pointed at *one immediate request*. The user asked a question, and the loop, however many times it turned, was working to answer that single question. A human sat at the front of it, posing each new query, steering each new turn.

Now imagine removing that steering. Instead of handing the model a question, you hand it a *goal*, something larger than any single tool call can satisfy: "find the three cheapest flights to Istanbul next month and put them in a table," or "figure out why this program crashes and fix it." Then you let the loop run on its own. The model looks at the goal, decides what the first useful step is, calls a tool, reads the result, decides the *next* step based on what it just learned, and keeps going, turn after turn, without a human posing each one. It plans. It acts. It reacts to what it finds. It stops when it judges the goal is met.

That is an **agent**. The ingredients are all familiar from earlier topics, a language model, tools, the loop, but the arrangement is new, and the new arrangement changes what the system *is*. A tool-using assistant answers what you ask. An agent pursues what you want. This topic is about that shift, from a system you steer turn by turn to one that steers itself toward an objective you hand it once.

As throughout the chapter, the treatment is recognition-depth. The real engineering of agents, the frameworks, the memory systems, the orchestration patterns, the hard problems of reliability and control, fills dedicated chapters later. The goal now is to see clearly what an agent is, what it is made of, and why, for all its apparent autonomy, it does not violate the single fact this chapter has held onto throughout: underneath, it is still a model predicting the next token.

## Core Concepts

### What an Agent Is

An agent is a language model running the tool-use loop *autonomously* in pursuit of a *goal*, deciding its own next step at each turn rather than being handed each step by a human.

That one sentence carries the whole shift, so it is worth taking apart. In [Topic 22: Tool Use and Function Calling](topic-22-tool-use-and-function-calling.md), the loop existed, but a person drove it: they asked a question, and the loop turned only as far as needed to answer *that* question before stopping and waiting for the next human message. In an agent, the person steps back. They supply a goal at the start, and from then on the model itself decides, at every turn, what to do next. Should it call a tool? Which one? With what arguments? Is the goal met, or is more work needed? The model answers these questions itself, over and over, until it decides it is finished.

The difference is not in the parts. It is in *who holds the steering wheel*. Strip the human out of the turn-by-turn loop, replace their guiding questions with a single standing goal, and let the model close the loop on itself. That is the entire conceptual leap. Everything else in this topic is a consequence of it.

### The Anatomy of an Agent

Four elements make up an agent, and each maps to something you have already met.

The **goal** is the objective handed in at the start, the thing the whole system is trying to accomplish. It replaces the stream of individual human questions with one durable target that persists across every turn of the loop.

The **model** is the reasoning core, the brain of the operation. At each turn it takes stock of the goal, everything that has happened so far, and the results of past actions, and it decides what to do next. This is the same next-token prediction from [Topic 15: Large Language Models](topic-15-large-language-models.md), now directed at a specific kind of question: given where I am and what I am trying to achieve, what is the next best move?

The **tools** are the hands, the functions from [Topic 22: Tool Use and Function Calling](topic-22-tool-use-and-function-calling.md) that let the model actually affect the world, search the web, run code, read a file, call an API. Without tools an agent could only think; with them it can act on its thinking.

The **loop** is the engine. It keeps the whole thing turning: model decides, tool runs, result comes back, model decides again. It runs not until one question is answered but until the *goal* is judged complete or abandoned. The loop is what converts a single act of reasoning into sustained, multi-step pursuit.

Brain, hands, objective, and the engine that drives them in a cycle. That is an agent, and none of the four is new. What is new is that they are wired together so the system runs itself.

### Planning and Decomposition

A goal like "fix this crashing program" cannot be satisfied by a single tool call. It has to be broken into steps: read the error, look at the relevant code, form a hypothesis about the cause, test it, apply a fix, verify the fix worked. This breaking-down is **decomposition**, and in an agent the model does it *itself*, rather than being handed a pre-written recipe.

Crucially, the plan is usually not fixed in advance. A rigid agent that decided all its steps up front would be brittle, because it cannot know what it will find until it looks. Real agents plan *adaptively*: they take a step, observe the result, and let that result shape the next step. The program's error message determines which file to open; what that file contains determines the next hypothesis. The agent is reasoning about a moving situation, not executing a frozen script. This interleaving of thinking and acting, decide, act, observe, decide again, is the heartbeat of agentic behavior, and it is why agents can handle tasks whose exact shape could not be known when the goal was first set.

### Memory and State

Because an agent runs across many turns, it needs to carry information forward. What has it already tried? What did those attempts reveal? What is left to do? This accumulated context is the agent's **memory** or **state**, and keeping it coherent over a long run is one of the central challenges of building agents.

The simplest form of memory is just the growing conversation: every action and every result gets appended to the context, so the model can see the whole history each turn. But this runs headlong into the limit from [Topic 20: Prompting and Context Windows](topic-20-prompting-and-context-windows.md): the window is finite, and a long-running agent generates far more history than will fit. So real agents need strategies for managing memory, summarizing what happened, storing details externally and retrieving them when relevant, keeping only what matters in the active window. How an agent remembers, across a run longer than any single context window can hold, is a genuinely hard problem, and the depth of it belongs to the later chapters. For now the point is simply that an agent must track state across time, and that doing so well is far from trivial.

### Knowing When to Stop

A loop that never ends is not useful, so an agent has to decide, on its own, when the goal is met and it should stop. This sounds trivial and is not. The model has to judge whether its work actually satisfies the objective, and that judgment can fail in both directions: stopping too early, declaring success on an incomplete job, or never stopping at all.

The failure modes are worth naming, because they are what makes agents hard to trust. An agent can get **stuck**, unable to find a next step that makes progress. It can fall into a **loop**, repeating the same action and the same result without advancing. It can **drift**, wandering away from the original goal as the accumulated context pulls its attention elsewhere. And it can be **overconfident**, concluding it has succeeded when it has not. Every one of these traces back to the same root: the autonomy that makes an agent powerful is exactly what removes the human check that would have caught the mistake. Give a system the freedom to run itself, and you also give it the freedom to run itself off a cliff. This is why control, oversight, and reliability sit at the very center of agent engineering, and why the alignment concerns from [Topic 19: Alignment](topic-19-alignment.md) sharpen dramatically the moment a system acts on its own across many steps.

## Why It Matters

The move from tool use to agents is the move from a system that helps to a system that *does*. A tool-using assistant is still fundamentally reactive: it waits for your question and answers it. An agent is proactive within the scope of its goal: you state the objective once, and it carries out the extended, multi-step work of reaching it. That is a difference in kind, and it is the difference that most of the current excitement, and most of the current worry, in applied AI is about.

It matters because a huge fraction of valuable work is not answerable in one shot. It requires investigating, trying things, reacting to what you find, and iterating toward a result, exactly the shape of work agents are built for. A system that can be handed a real objective and left to pursue it is qualitatively more useful than one that must be walked through every step, and it opens the door to automating tasks that were previously out of reach for AI entirely.

And it matters because this is the live frontier. The systems being built and debated right now, the coding agents, the research agents, the computer-using agents, are all instances of this pattern. Understanding what an agent actually is, a model, tools, and a loop, wired for autonomy toward a goal, is understanding the thing the whole field is currently reaching for. It also sets up the chapter's final and most important lesson: that even this, the most autonomous and intelligent-seeming system in the book, is not what it appears to be.

## Real-World Examples

**Coding agents.** Handed a goal like "add this feature" or "fix this failing test," a coding agent reads the relevant files, reasons about the code, writes changes, runs the tests to see if they pass, reads the failures, and revises, looping until the tests are green or it gives up. It is the tool-use loop from the previous topic, now running autonomously across many steps toward a goal no single tool call could reach.

**Research agents.** Given a question that needs investigation, a research agent plans a series of searches, reads what it finds, notices gaps, searches again to fill them, and synthesizes the accumulated findings into an answer. It decides its own line of inquiry, adapting each search to what the previous ones turned up, rather than executing a fixed list.

**Computer-use agents.** The most striking and the most fraught: an agent given control of a computer interface, able to click, type, and navigate to accomplish a goal across applications. It sees the screen, decides an action, takes it, observes the new state, and continues. The power is obvious, and so is the risk, which is exactly why this class of agent brings the control and alignment questions of [Topic 19: Alignment](topic-19-alignment.md) into the sharpest possible focus.

## How It's Built

At its skeleton, an agent is a strikingly small program wrapped around a language model, which is part of why the pattern spread so fast.

A goal and an initial context are prepared and sent to the model along with the available tool schemas. The model responds, either with a tool call or with a judgment that the goal is complete. If it is a tool call, the harness, the same orchestrator from [Topic 22: Tool Use and Function Calling](topic-22-tool-use-and-function-calling.md), runs the real function, captures the result, and appends it to the running context. Then it sends everything back to the model for the next turn. This repeats: decide, act, observe, decide, until the model signals completion or some external limit, a maximum number of steps, a budget, a timeout, halts the loop as a safeguard.

The essential design question is where each responsibility lives. The **model** holds the reasoning: what to do next, whether the goal is met. The **harness** holds the machinery: running tools, managing the growing context, enforcing the limits that keep the loop from running forever. And critically, the harness is also where **human oversight** is inserted, the checkpoints where a person can approve an action before it happens, review progress, or stop the run. Because an agent's autonomy is its chief danger as well as its chief strength, these controls are not an afterthought; they are as much a part of a well-built agent as the loop itself. The deep engineering of all this, the frameworks, the memory architectures, the safety scaffolding, is the substance of the later chapters. The skeleton, though, is exactly this: a goal, a model, tools, a loop, and the guardrails around it.

## Key Takeaways

* **An agent is the tool-use loop run autonomously toward a goal.** Instead of a human posing each question and steering each turn, you hand the model a single standing goal and let it decide its own next step, over and over, until it judges the goal complete. The parts are familiar from [Topic 22: Tool Use and Function Calling](topic-22-tool-use-and-function-calling.md); the autonomy is what is new.
* **Four elements make an agent:** the **goal** (the objective), the **model** (the reasoning core deciding each next step), the **tools** (the hands that act on the world), and the **loop** (the engine that keeps turning until the goal is met). None is new; the wiring is.
* **Agents plan adaptively.** They decompose a goal into steps themselves and let each observed result shape the next step, interleaving thinking and acting rather than executing a fixed script. This is what lets them handle tasks whose exact shape is unknown at the start.
* **Agents must manage memory and state** across runs longer than any single context window can hold, which collides directly with the limit from [Topic 20: Prompting and Context Windows](topic-20-prompting-and-context-windows.md) and makes long-run memory a central, hard problem.
* **Knowing when to stop is genuinely difficult.** Agents can get stuck, loop, drift from the goal, or wrongly declare success. Every failure traces back to the same root: autonomy removes the human check that would have caught the error, which is why oversight and control sit at the center of agent design and why the concerns of [Topic 19: Alignment](topic-19-alignment.md) sharpen the moment a system acts on its own.
* **This is the frontier of applied AI.** Coding agents, research agents, and computer-use agents are all this one pattern. An agent is the most autonomous, intelligent-seeming system in the chapter, which makes it the perfect final test of the chapter's central claim.

## References

* **Yao et al.**: *ReAct: Synergizing Reasoning and Acting in Language Models* (2023), which formalized the interleaving of reasoning and action that underlies agentic loops.
* **Wang et al.**: *A Survey on Large Language Model based Autonomous Agents* (2023), a broad map of agent architectures, memory, planning, and the open problems in the space.
* **Anthropic**: *Building Effective Agents* (2024), a practical account of agent patterns and the trade-offs between simple loops and elaborate orchestration.
* **Weng, Lilian**: *LLM-Powered Autonomous Agents* (2023), an accessible and widely cited overview of the components, planning, memory, and tool use, that make up an agent.

## Think About It

1. The chapter has insisted from the start that a language model only predicts the next token. An agent plans, acts, reacts, and pursues a goal across many steps, which looks like far more than next-token prediction. Reconcile these: explain precisely where the "planning" and "goal-pursuit" actually come from, and why the appearance of autonomous intelligence does not require the model to be doing anything other than predicting tokens inside a loop.

2. An agent is handed the goal "book me the cheapest flight to Istanbul next month" and, several steps in, confidently books a flight to the wrong city on the wrong date, reporting success. Using the failure modes from this topic and the split between the model's reasoning and the harness's machinery, explain how this can happen even though every individual step seemed reasonable, and describe one place in the agent loop where a control could have caught it.

3. Tool use raised the stakes of [Topic 19: Alignment](topic-19-alignment.md) by letting a model act; agents raise them further by letting a model act *autonomously across many steps toward a goal*. Explain what specifically changes about the alignment problem when you move from a single human-steered action to an unsupervised multi-step pursuit, and why "the agent did exactly what its goal said" can still be a serious failure.

## Next Topic

An agent is the most impressive system in this chapter. It plans, it acts, it adapts, it pursues goals across dozens of steps with what looks unmistakably like intelligence and intent. And that is exactly why it makes the perfect closing case for the question the whole chapter has been circling. If even an agent, this autonomous, this capable, this convincingly intelligent, is at bottom nothing more than a next-token predictor running in a loop, then the mystique that surrounds modern AI is worth dismantling deliberately, piece by piece. The final topic does exactly that, gathering everything from tokenization to agents into one clear-eyed conclusion: **[Topic 24: AI Is Not Magic](topic-24-ai-is-not-magic.md)**.
