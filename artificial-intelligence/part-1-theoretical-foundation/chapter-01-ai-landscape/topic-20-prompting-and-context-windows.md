# Topic 20: Prompting and Context Windows

## Introduction

[Topic 19: Alignment](topic-19-alignment.md) shaped the model once, at training time, and then the weights froze. But a frozen model is not a finished conversation. Every time you actually use it, you steer it again, live, and the only instrument you have is the text you send. That text is the prompt, and it is easy to mistake it for something it is not.

The natural assumption is that a prompt is a question, posed to a mind that is sitting there thinking, the way you might ask a colleague across a desk. That picture is wrong in a way that matters. The model is not sitting there. Between your messages it does nothing, remembers nothing, and is nothing but a fixed set of weights. When your prompt arrives, the entire machine wakes up, reads the text from the first token to the last, produces one continuation, and goes dark again. The prompt is not a question handed to a thinker. It is the whole of what the thinker gets to think about.

This changes how you should reason about prompting. The prompt is the model's working memory, its entire world for the duration of one response, and that world has a hard edge: a maximum number of tokens it can hold at once, called the **context window**. Understanding why wording matters so much, and where that hard edge sits, is what separates people who fight the model from people who steer it.

## Core Concepts

### The prompt is the working memory, not a question

Recall from [Topic 15: Large Language Models](topic-15-large-language-models.md) that the model does exactly one thing: it reads a sequence of tokens and outputs a probability distribution over what token comes next. It has no scratchpad, no hidden notebook, no memory of your last session. Everything it uses to compute that next token has to be present in the tokens in front of it.

So the prompt is not a message you send to a reasoning system that exists independently of it. The prompt *is* the reasoning system's input state, the complete set of conditions under which the next-token machine runs. When people say "give the model context," this is literal: context is not background information the model politely considers, it is the only thing the model has. Nothing you told it yesterday is there. Nothing implied but unwritten is there. If it is not in the tokens, it does not exist for the model.

### Why wording changes everything

Because the model outputs a probability distribution, and that distribution is conditioned on every token in the prompt, changing the wording changes the distribution. This is not the model being fussy or fragile. It is the direct, mechanical consequence of what the model is.

Consider the difference between "Tell me about dogs" and "Explain to a veterinary student the common causes of canine hip dysplasia." The second prompt does not just ask a narrower question. It floods the context with tokens that, during pretraining, tended to co-occur with technical, clinical, precise text. The model has learned, from predicting the next token across a planetary corpus, that text which begins in a clinical register continues in a clinical register. The prompt tilts the distribution toward that continuation before the model writes a single word of its answer.

This is why a prompt's framing, vocabulary, and specificity matter so much: each token you add is evidence the model uses to decide what kind of text it is in the middle of, and therefore what should come next. You are not persuading a mind. You are setting the conditions of a prediction.

### The anatomy of a prompt

Modern chat models carve the prompt into roles, most commonly a **system** message and one or more **user** messages. The system message is text placed at the very start, conventionally used to establish standing instructions and persona ("You are a careful medical editor"). The user messages carry the actual turns of conversation. To the underlying model these are all just tokens in one sequence, distinguished by special formatting tokens; the roles are a convention layered on top, made meaningful by the alignment training of [Topic 19: Alignment](topic-19-alignment.md), which taught the model to treat system text as higher-priority framing.

Within that structure, the single most reliable lever is the **example**. Asking the model to perform a task with no examples is **zero-shot** prompting; supplying a few worked examples first is **few-shot** prompting. Few-shot works for a reason we already met in [Topic 15: Large Language Models](topic-15-large-language-models.md): in-context learning. The examples do not update any weights. They sit in the context, and the model, being a next-token predictor, infers the pattern they establish and continues it. Three examples of sentences labeled "positive" or "negative" turn the model into a sentiment classifier for the length of that one response, purely by making that the most likely continuation.

### Statelessness and the illusion of chat

Here is the fact that surprises people most: the model has no memory between messages. It is **stateless**. When you send the tenth message in a long conversation, the model does not recall the previous nine. Instead, the system feeds it all ten, the entire transcript, reassembled into one long prompt, every single time. The model reads the whole history from scratch, produces one reply, and forgets it all again.

The smooth, continuous conversation you experience is an illusion, and a somewhat expensive one. There is no ongoing session inside the model. There is only a growing block of text that gets resent, in full, on every turn, with your newest message appended to the end. "Memory" in a chat interface is nothing more than the transcript being carried forward and re-fed. This is not a limitation to be engineered away with cleverness; it is the nature of the machine, and it leads directly to the hard edge.

### The context window

The prompt cannot grow forever. Every model has a **context window**: a fixed maximum number of tokens it can process in a single forward pass, counting the system message, the entire conversation history, and the response it is generating, all together. Recall from [Topic 10: Tokenization](topic-10-tokenization.md) that text is measured in tokens, not words, so a window of, say, 200,000 tokens holds roughly 150,000 words of English, everything included.

When a conversation grows past the window, something has to give. The oldest tokens fall off the front, or the system summarizes them, or the request is refused. This is why a long chat can seem to "forget" what you said at the start: those tokens have literally scrolled out of the window and no longer exist for the model. The window is not a soft preference for brevity. It is a hard architectural ceiling.

Why does the ceiling exist? Trace it back to [Topic 13: Attention](topic-13-attention.md). Attention lets every token look at every other token, which is what gives the transformer its power, but the cost of that all-pairs comparison grows with the square of the sequence length. Double the context and you roughly quadruple the attention work. The window is the engineering line drawn where that cost, in memory and computation, stops being worth it. Longer windows are an active frontier precisely because pushing that line is expensive.

## Why It Matters

Prompting is the control surface almost everyone actually touches. Most people will never finetune a model or adjust its weights, but every single interaction is an act of prompt design, whether deliberate or not. Understanding that the prompt is the model's entire working memory turns prompting from guesswork into engineering: you stop wishing the model understood you and start supplying the exact tokens that condition the output you want.

The context window matters just as concretely. It sets a hard budget on how much the model can consider at once, which shapes what tasks are even possible: you cannot ask a model to summarize a document that does not fit in its window without first splitting it up. And because every token in the window is processed on every turn, tokens are the unit of both cost and latency. A bloated prompt is a slower, more expensive prompt. Knowing where the tokens go is knowing where the money and the milliseconds go.

## Real-World Examples

**Few-shot classification.** A support team needs to route incoming tickets as "billing," "technical," or "account." Rather than train a classifier, they write a prompt with six labeled example tickets and then the new ticket. The model, conditioned on those examples, continues the pattern and emits the right label. No weights changed; the examples in the context did all the work, in-context learning from [Topic 15: Large Language Models](topic-15-large-language-models.md) put to practical use.

**Lost in the middle.** Feed a model a long context with a crucial fact buried in the exact center, and it will often answer as though the fact were not there, even though it technically fits in the window. Researchers have documented that models attend most reliably to the beginning and end of a long context and least reliably to the middle. Fitting inside the window is necessary but not sufficient; where in the window a fact sits affects whether the model actually uses it.

**Truncated documents.** A user pastes a 500-page contract and asks a question. The document is far larger than the window, so the interface silently keeps only the portion that fits, and the model answers confidently based on a fragment, unaware that most of the contract was never shown to it. The failure is invisible unless you know the window exists.

## How It's Built

Under the hood, a prompt is assembled and then measured. The system message, the conversation history, and the new user message are concatenated in order, with special tokens marking the role boundaries. That assembled string is run through the tokenizer from [Topic 10: Tokenization](topic-10-tokenization.md), producing the exact integer count that must fit under the window limit. If it fits, the sequence is fed to the model, which runs one forward pass and generates the response token by token, each new token appended and the whole thing re-read, until it emits a stop token or hits the window ceiling.

The window ceiling itself is baked into the architecture. A transformer processes positions up to some maximum for which it was trained, and the position-handling machinery, together with the quadratic attention cost from [Topic 13: Attention](topic-13-attention.md), fixes how far that maximum can reasonably go. Extending it is not a matter of changing a setting; it requires training the model to handle longer sequences and paying the compute cost that comes with them. This is why context length is a headline specification when a new model ships: it is a real, hard-won capability, not a slider.

## Key Takeaways

* A prompt is not a question sent to a thinking mind. It is the model's **entire working memory**, the complete input state the next-token machine conditions on. If something is not in the tokens, it does not exist for the model.
* Wording changes the output because the model produces a **probability distribution conditioned on every token**. Framing, vocabulary, and specificity are evidence the model uses to decide what kind of text it is continuing.
* **Few-shot prompting** supplies worked examples in the context; the model infers the pattern and continues it through in-context learning, with no change to any weights.
* The model is **stateless**. Chat has no ongoing session inside the model; the full transcript is re-fed on every turn, and "memory" is just that transcript being carried forward.
* The **context window** is a hard token ceiling covering system message, history, and response together. Exceed it and the oldest tokens fall off. The ceiling exists because attention's cost grows with the square of the sequence length.
* Tokens are the unit of cost and latency. A bloated prompt is slower and more expensive, and fitting a fact in the window does not guarantee the model will use it.

## References

* **Brown et al.**: *Language Models are Few-Shot Learners* (2020), the GPT-3 paper that established in-context and few-shot prompting as an emergent capability of scale.
* **Liu et al.**: *Lost in the Middle: How Language Models Use Long Contexts* (2023), the study documenting that models use information at the start and end of a long context more reliably than information in the middle.
* **OpenAI**: *Prompt Engineering Guide* (documentation), a practical catalog of prompting techniques and their rationale.
* **Anthropic**: *Prompt Engineering Overview* (documentation), a working reference for structuring prompts, system messages, and examples.

## Think About It

1. Two users get very different answers from the same model on the same underlying question, one vague and one precise, purely because of how they worded their prompts. Using the idea that the prompt is the model's entire working memory, explain what actually differed between the two runs, and why this is a difference in conditioning rather than the model "understanding" one user better.
2. A chat interface feels like a continuous conversation with something that remembers you, yet the model underneath is stateless and forgets everything between turns. Explain how the illusion of memory is constructed, and predict what happens to the "memory" of the earliest messages as the conversation grows longer than the context window.
3. A fact fits comfortably inside the context window but sits in the middle of a very long prompt, and the model answers as though it were not there. Connect this to the "lost in the middle" finding and to the all-pairs cost of attention from [Topic 13: Attention](topic-13-attention.md): why is fitting in the window necessary but not sufficient for the model to actually use a piece of information?

## Next Topic

The context window is finite and the model's knowledge is frozen at the moment pretraining ended. Together those two limits pose a sharp problem: how do you get a model to answer questions about a document it was never trained on, or about events that happened after its training cut off, without exceeding the window or retraining the weights? The answer is to stop trying to cram everything into the prompt and instead fetch only the few relevant pieces on demand, retrieving them from an external store and slipping them into the context exactly when needed. That technique, which turns the context window from a wall into a doorway, is the subject of **[Topic 21: RAG](topic-21-rag.md)**.
