# Topic 22: Tool Use and Function Calling

## Introduction

[Topic 21: RAG](topic-21-rag.md) ended on a capability that felt almost like giving the model a memory it never had. Retrieval reaches into an external store, pulls out the passages that bear on a question, and slips them into the prompt, so the model can answer using facts it was never trained on. It is a powerful move, and it dissolves the problem of frozen, finite knowledge. But look closely at what it actually does, and a boundary comes into view. Retrieval fetches *information*. It hands the model text to read. And text to read is the only kind of help it delivers.

That boundary matters because knowledge is not the only thing a language model lacks. Ask it what 847293 times 12844 equals, and it will produce a confident number that is very likely wrong, because it is predicting digits, not multiplying. Ask it for today's weather in a city, and it cannot know, because the weather is not in its weights and no amount of retrieval over a static document store will contain a fact that did not exist when the store was built. Ask it to send an email, book a flight, or write a row to a database, and it can describe doing so in fluent prose while changing exactly nothing in the world. The model generates text. That is the whole of what it does, and no clever prompt changes the fact.

Tool use is the move that reaches past this boundary. The idea is disarmingly simple: instead of forcing the model to answer everything from its own generated text, you let it *ask an external system to do the work* and hand back the result. The calculator multiplies. The weather service reports. The database returns its rows. The model's job shifts from knowing everything to knowing *when to ask* and *what to do with the answer*. Function calling is the disciplined, structured form of this idea, the contract that makes it reliable enough to build on.

As throughout the chapter, the treatment here is recognition-depth. The full engineering of agentic systems, tool orchestration, error handling, and the protocols that standardize all of it lives in the dedicated chapters later in the curriculum. The goal now is to understand clearly what tool use *is*, and why it does not contradict the one fact this chapter has repeated from the start: the model only ever predicts tokens.

## Core Concepts

### The Model Does Not Run Anything

The first and most important thing to understand about tool use is what the model does *not* do. It does not execute code. It does not query the database. It does not call the weather API. It cannot, because a language model is a function from text to text, and running a program is not a text operation.

What the model does instead is emit a *request*. When it decides a tool would help, it produces a structured piece of text that names the function it wants and specifies the arguments to pass. Something to the effect of: call `get_weather` with the argument `city: "Karachi"`. That request is just tokens, generated the same way every other token is generated, by predicting what comes next. The model has not done anything yet. It has only written down what it would like done.

Everything after that happens *outside* the model, in a surrounding program usually called the **harness** or the orchestrator. The harness reads the model's request, recognizes it as a tool call, and runs the real function: it actually hits the weather service, actually executes the query, actually performs the multiplication. Then it takes the result and feeds it back to the model as new text in the context, as if to say, here is what you asked for. The model reads that result and continues generating, now able to answer the user with a real fact it could never have produced on its own.

This division is the entire mechanism, and it is worth stating plainly because it dissolves the apparent magic. The model supplies the *judgment*, deciding a tool is needed and choosing the arguments. The harness supplies the *agency*, actually reaching out and doing the thing. Text in, text out, unchanged from the start of the chapter. What changed is that some of the text coming back is now written by a calculator or an API instead of by the model itself.

### The Loop

A single tool call is rarely the whole story, because the result of one call often shapes what to do next. So tool use runs as a **loop**, and understanding its shape is understanding the whole pattern.

The user asks a question. The model reads it and reasons about how to answer. If it can answer directly, it does. If it recognizes that it needs something it does not have, a live fact, a reliable calculation, an action in the world, it emits a tool request instead of a final answer. The harness catches that request, runs the corresponding function, and returns the result into the context. The model reads the result and reasons again. Maybe that result is enough, and now it writes the final answer. Or maybe the result reveals that another tool is needed, and it emits a second request, and the loop turns again.

Consider a question like "What is the current population of France divided by the number of days since the start of the year?" No single tool answers this. The model might first call a tool to look up the current population, read the returned number, then call a calculator with that number and the day count, read the computed result, and only then compose its answer in plain language. Three turns of the loop, two tools, one answer. Each turn is the same simple cycle: reason, maybe request, receive, repeat.

The loop is what makes tool use feel less like a lookup and more like a small process unfolding. And it is precisely this loop, run under a larger goal, that the next topic will build into something more.

### Function Calling as a Structured Contract

If the model's tool request were free-form prose, the harness would face an impossible parsing problem: how do you reliably extract a function name and its arguments from a sentence that could be phrased ten thousand ways? **Function calling** is the answer, and it is really an application of structured output.

The developer defines, ahead of time, a set of available tools. Each tool comes with a **schema**: its name, a description of what it does and when to use it, and a precise specification of the arguments it accepts, their names, their types, which are required. This schema is given to the model as part of its context, so the model knows exactly what tools exist and how to call each one.

When the model wants a tool, it does not write a sentence. It emits its request in the exact structured format the schema demands, typically a small block of JSON naming the function and supplying the arguments as typed fields. Because the format is fixed and machine-readable, the harness can parse it without guesswork, validate that the arguments match the schema, and dispatch the call with confidence.

This is where tool use connects back to earlier ideas. The model's ability to reliably emit valid, well-formed structured output is not automatic; it is a behavior shaped during [Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md), where models are trained specifically to produce clean function-call formats when appropriate. And the mechanics of *how* that structured text gets generated token by token, with the sampling controlled tightly enough that the JSON is always valid, reach back to [Topic 18: Sampling](topic-18-sampling.md). Function calling is not a new capability bolted onto the model. It is the same next-token prediction, disciplined by a schema and a training regime into a format a program can trust.

### The Description Is the Interface

One subtlety deserves its own note, because it surprises people coming from ordinary programming. In normal code, a function is chosen by the programmer who wrote the call. In tool use, the function is chosen by the *model*, based on the tool's natural-language description. This means the description is not documentation sitting off to the side. It is the actual interface the model reasons over.

A tool described vaguely as "gets data" will be called at the wrong times or not at all, because the model cannot tell from that phrase when it applies. A tool described precisely, "returns the current temperature and conditions for a given city; use when the user asks about present or near-future weather", gives the model exactly what it needs to decide. The quality of a tool-using system depends heavily on how well its tools are described, which is a genuinely new discipline: writing not for a human reader but for a model that will decide, on its own, whether and how to invoke what you have built.

## Why It Matters

Everything up to this topic described a system that was, in the end, sealed off from the world. A language model with a context window is a remarkable text engine, and RAG lets that engine read from an external library, but it remained a thing that could only ever *speak*. Tool use is the point where that seal breaks.

The consequence is a change in kind, not degree. A model that can call functions is no longer bounded by what it was trained on or what sits in its prompt. It can reach live systems, so its knowledge is as current as the services it can query. It can offload the operations it is bad at, arithmetic, precise lookup, anything requiring exactness, to systems built to do them correctly, which directly addresses the reliability problem that has shadowed every capability in this chapter. And it can *act*: send, write, book, trigger, moving from a system that describes the world to one that changes it.

This is also the architectural foundation for essentially all serious applied AI being built today. The chatbot that checks your order status, the coding assistant that runs your tests, the research tool that searches the live web, none of these are pure language models. They are language models wrapped in a harness, equipped with tools, running the loop. Understanding tool use is understanding the shape of the field as it actually exists, rather than the shape of a model sitting alone.

## Real-World Examples

**The calculator.** The cleanest illustration, because it targets a known weakness head-on. A model asked to multiply large numbers will confidently generate a wrong answer, since it is predicting plausible digits rather than computing. Give it a calculator tool, and it stops guessing: it emits a call with the two numbers, the harness computes the exact product, and the model reports it. The unreliable operation is handed to a system that cannot get it wrong.

**Code execution.** Many assistants can write code, but writing code and knowing whether it *works* are different things. A code-execution tool lets the model actually run what it wrote, see the real output or the real error, and correct itself based on what happened rather than on what it predicted would happen. This turns coding from a one-shot guess into a feedback loop.

**Web and API access.** A weather tool, a search tool, a stock-price tool: each connects the model to a live source of information that no static store could hold, because the information did not exist when any store was built. This is the direct answer to the frozen-knowledge limit that [Topic 21: RAG](topic-21-rag.md) could only partly address, since RAG retrieves from a fixed collection while a live tool queries the present moment.

**Database queries and actions.** A tool that runs a query against a company's database lets the model answer questions about private, current data. A tool that writes a record, sends a message, or updates a status lets it *do* things, crossing fully from describing the world into changing it. This is also where the stakes rise sharply, since a model that can act can act wrongly, a concern that connects directly to [Topic 19: Alignment](topic-19-alignment.md).

## How It's Built

The pattern underneath all of this is small enough to hold in your head, which is part of why it has become so pervasive.

The developer writes real functions in ordinary code, a `get_weather` that calls a weather service, a `run_query` that hits a database, whatever the application needs. Each function is paired with a schema describing it in the structured form the model expects. These schemas are supplied to the model as part of its context, so it knows the full menu of what it can invoke.

At run time, the harness manages the loop. It sends the user's message and the tool schemas to the model. It watches the model's output for a tool call. When one appears, it does not pass it along as an answer; instead it parses the structured request, looks up the matching real function, calls that function with the model's supplied arguments, and captures the result. It then appends the result to the conversation and sends everything back to the model for the next turn. When the model finally produces ordinary text instead of a tool call, the harness recognizes that as the final answer and delivers it to the user.

Two responsibilities are worth separating clearly, because conflating them is the source of most confusion about tool use. The **model** decides *what* to call and *with what arguments*; that is judgment, and it lives inside the language model. The **harness** decides *how* to actually run the call and *what to do with the result*; that is execution, and it lives entirely in ordinary code outside the model. The model never touches a real system. The harness never reasons about the user's intent. Each does the one thing it is suited for, and the loop stitches them together.

## Key Takeaways

* **Tool use lets a model act, not just speak.** [Topic 21: RAG](topic-21-rag.md) fetches information into the prompt; tool use lets the model ask an external system to *do* something, a calculation, a live query, an action, and use the result. It reaches past the boundary of text-in, text-out.
* **The model never runs anything.** It only emits a structured *request* naming a function and its arguments. That request is ordinary generated text. A surrounding **harness** reads it, runs the real function, and feeds the result back. The model supplies judgment; the harness supplies agency.
* **Tool use runs as a loop.** Reason, maybe request a tool, receive the result, reason again, repeat until the model produces a final answer. One question can turn the loop several times across several tools.
* **Function calling is a structured contract.** Each tool has a schema, name, description, typed arguments, given to the model in context. The model emits its call in that exact machine-readable format, so the harness can parse and dispatch it reliably. This builds directly on structured output from [Topic 18: Sampling](topic-18-sampling.md) and behaviors shaped in [Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md).
* **The tool description is the interface.** Because the *model* chooses which tool to call from its natural-language description, writing precise, well-scoped descriptions is a real and new discipline: you are writing for a model that will decide on its own whether to invoke what you built.
* **This is the shape of applied AI.** Nearly every serious AI product today is a language model wrapped in a harness, equipped with tools, running the loop. Tool use is the architectural foundation, not an add-on.

## References

* **Schick et al.**: *Toolformer: Language Models Can Teach Themselves to Use Tools* (2023), an influential demonstration that models can learn when and how to call external tools.
* **Yao et al.**: *ReAct: Synergizing Reasoning and Acting in Language Models* (2023), which framed the interleaving of reasoning and tool actions that underlies the tool-use loop.
* **Anthropic**: *Tool Use* (documentation), a practical reference for how function calling is defined, invoked, and handled in production.
* **Alammar and Grootendorst**: *Hands-On Large Language Models* (2024), for an accessible, implementation-oriented treatment of tool use and function calling.

## Think About It

1. A user asks a model, "Is it going to rain in my city this afternoon?" and the model answers confidently that it will not, without ever calling a weather tool that was available to it. Explain, using the split between the model's judgment and the harness's agency, where this failure actually occurred, and why giving the model access to a tool is not the same as guaranteeing it will use the tool.

2. Function calling depends on the model emitting a structured request that a program can parse without guesswork. Using what you know from [Topic 18: Sampling](topic-18-sampling.md), explain why an unconstrained, high-randomness generation process would be dangerous for tool calls specifically, and what has to be true about the generated text for the harness to dispatch the call safely.

3. Tool use lets a model not only read the world but change it: send messages, write records, trigger actions. Connect this to [Topic 19: Alignment](topic-19-alignment.md), and explain why the shift from a model that describes to a model that acts raises the stakes of alignment sharply, and what new category of failure becomes possible once a model can act that was impossible when it could only generate text.

## Next Topic

Tool use gives a model the ability to reach outside itself, and the loop lets it chain a few calls together to answer a single question. But notice that in everything above, the loop was still driven turn by turn toward one immediate request. What happens when you hand the model not a single question but a *goal*, and let it run the loop on its own, deciding which tools to call, in what order, for as many steps as the goal requires, without a human steering each turn? A model that plans and acts across many steps in pursuit of an objective is no longer just answering; it is doing. That shift, from tool-using assistant to goal-driven system, is the subject of **[Topic 23: AI Agents](topic-23-ai-agents.md)**.
