# Topic 17: Training vs Inference

## Introduction

[Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md) described a factory. Pretraining and finetuning are both runs on that factory floor: one enormous and one modest, but both the same kind of thing. Data goes in, gradients flow, weights change, and at some point the run ends. The weights are frozen, the learning stops, and the model is packaged and shipped. Whether the bill was a hundred million dollars or a quiet weekend on a single rented GPU, the factory did its job once and fell silent.

What happens next is a different process entirely, and you trigger it every time you send a message. When you type a question and watch the answer appear word by word, no learning is happening. The weights do not move. The model is not studying your question the way it studied its training data; it is running a fixed function over your input to produce an output, the same way a compiled program runs. This is **inference**, and it is not a smaller version of training. It has different math, different hardware pressures, different costs, and a different bottleneck. The two words in this topic's title name the two halves of a model's life: **training** is how the model is made, **inference** is how the model is used.

As throughout the chapter, the treatment is recognition-depth. The engineering of efficient inference is a deep field that returns later in the curriculum; here the goal is to cleanly separate the two processes, understand why inference dominates a model's real-world cost, and see why a laptop can run a model it could never have trained.

## Core Concepts

**Training runs the full loop.** Every training step from [Topic 09: Backpropagation](topic-09-backpropagation.md) has three parts: a forward pass that computes a prediction, a loss that measures how wrong it was, and a backward pass that computes gradients and nudges every weight to be a little less wrong next time. This loop repeats billions of times over the training data. It is expensive because the backward pass roughly doubles the work of the forward pass, because gradients for every weight must be held in memory, and because the whole thing must run over an ocean of data, often for months.

**Inference runs the forward pass only.** When you use the model, there is no loss and no backward pass. The input goes in, flows forward through the frozen weights, and a prediction comes out. No gradients are computed. No weights change. This is dramatically cheaper per step than training, which is exactly why a model that took a data center months to train can answer you on a phone in a second.

**The costs land in opposite places.** Training is paid once. However staggering the bill, it is a one-time capital expense: run the factory, ship the model, done. Inference is paid every single time anyone uses the model, forever. A popular model answers billions of queries a day, and each one costs a little compute. Over a model's deployed lifetime, the total spent on inference dwarfs what was spent on training. This is the fact that shapes the entire economics of the industry: training makes the headlines, inference pays the bills.

**Generation is autoregressive, one token at a time.** A language model does not produce a whole answer at once. It predicts one token, appends that token to the input, and runs the forward pass again to predict the next, over and over until it emits a stop signal. This loop is why answers stream onto your screen rather than appearing all at once, and it means generating a hundred-token reply is a hundred forward passes, not one. The length of the output, not just the input, drives the cost.

**The KV cache keeps that loop affordable.** Naively, each new token would re-process the entire growing sequence from scratch, doing more and more redundant work as the text lengthens. Instead, models store the intermediate attention results (the "keys" and "values" from [Topic 13: Attention](topic-13-attention.md)) for tokens already seen, so each new token only computes what is genuinely new. This **KV cache** is the single most important trick that makes real-time generation practical, and it is why inference is often limited by memory bandwidth rather than raw arithmetic.

## Why It Matters

The training-inference split explains the shape of the products you actually touch. API pricing is quoted per token, split into input and output, because that is literally what inference costs the provider: every token in your prompt is processed, and every token generated is a separate forward pass. Latency, the pause before and during a response, exists because generation is a sequential loop that cannot be fully parallelized away. When a provider is slow or rate-limits you at peak hours, that is an inference-serving constraint, not a training one.

The split also explains why so much deployment effort goes into shrinking models after they are trained. Techniques like quantization, which stores weights in fewer bits, exist to make inference cheaper and to fit models onto smaller hardware. They trade a little quality for a large drop in the memory and compute each forward pass demands. None of this touches training; it is all about making the finished product cheaper to run at scale. Recognizing that inference is the recurring cost center, not training, is the key to understanding why the field pours so much ingenuity into it.

## Real-World Examples

A frontier model like GPT-4 is trained once, in a run measured in months and across thousands of specialized accelerators, at a cost widely estimated in the tens or hundreds of millions of dollars. That run happens a single time. After it ships, that same model serves an enormous, continuous stream of queries: coding help, homework, translation, customer support, all day, every day, from users around the world. The training run is a historical event; the inference is a river that never stops.

The contrast is sharpest on your own hardware. Take an open model like Llama, quantize it, and it will run inference comfortably on a consumer laptop or even a phone, generating text with no data center in sight. That same laptop could not perform even a tiny fraction of the training that produced the model: it lacks the memory to hold the gradients, the compute to run the backward pass, and the data to learn from. You are running the product, and running the product is cheap. Building it was the expensive part, and that part is already over.

## How It's Built

The two processes are two different pipelines over the same weights.

```python
# Training step: forward, loss, backward, update.
logits = model(batch_of_text)              # forward pass
loss   = cross_entropy(logits, targets)    # how wrong were we?
loss.backward()                            # backward pass: gradients for every weight
optimizer.step()                           # nudge the weights
optimizer.zero_grad()                      # reset for the next batch
# Repeated billions of times, over months. Weights change every step.
```

Inference deletes the bottom half and wraps the top in a loop that grows the sequence one token at a time:

```python
# Inference: forward pass only, weights frozen, one token at a time.
tokens = tokenize(prompt)
while not done:
    logits    = model(tokens)         # forward pass, no gradients
    next_tok  = pick_from(logits[-1]) # choose the next token
    tokens.append(next_tok)           # feed it back in
    done = (next_tok == STOP)
# No loss, no backward, no update. The KV cache means each pass
# only processes the new token, not the whole sequence again.
```

The asymmetry is visible in the code: training is compute-bound, dominated by the heavy backward pass over huge batches, and it runs on clusters built for raw arithmetic throughput. Inference, especially the token-by-token generation loop, is often memory-bound, limited by how fast the growing KV cache can be moved in and out of memory rather than by arithmetic. Same model, two profiles, two kinds of hardware pressure.

## Key Takeaways

* A model's life has two phases: **training**, which makes the model, and **inference**, which uses it. They are different processes, not big and small versions of the same one.
* **Training** runs the full loop: forward pass, loss, and backward pass with weight updates. **Inference** runs the forward pass only, with frozen weights and no gradients.
* **Training is paid once; inference is paid per request, forever.** Over a deployed model's lifetime, total inference cost dwarfs training cost, which is why inference is the industry's real cost center.
* Generation is **autoregressive**: one token at a time, each fed back in, which is why answers stream and why output length drives cost.
* The **KV cache** stores past attention results so each new token only does new work, making real-time generation practical and making inference frequently memory-bound.
* Post-training tricks like quantization exist to make **inference** cheaper and to fit models onto smaller hardware, which is how a laptop runs a model it could never have trained.

## References

* **Kaplan et al.**: *Scaling Laws for Neural Language Models* (2020), useful for the training-cost side of the picture and how compute is budgeted for a run.
* **Pope et al.**: *Efficiently Scaling Transformer Inference* (2022), a clear account of what actually makes inference expensive and how serving is optimized.
* **Dettmers et al.**: *LLM.int8()* (2022), a foundational quantization paper showing how models are shrunk for cheaper inference without wrecking quality.
* **Hugging Face**: *Text Generation Inference* documentation, a practical look at how the generation loop and KV cache are served in production.

## Think About It

1. Two providers host the exact same open model. One charges more per output token than per input token; the other charges the same for both. Using the autoregressive generation loop, explain why charging more for output tokens is the more principled choice.
2. A user complains that a model "gets slower the longer the conversation goes." Nothing about the weights has changed between messages. Using the KV cache and the growing sequence, explain what is actually getting more expensive and why.
3. Quantization makes inference cheaper by storing weights in fewer bits, and it is applied after training is finished. Why can the same trick not simply be used to make training cheap in the first place? Think about what the backward pass of [Topic 09: Backpropagation](topic-09-backpropagation.md) needs that a forward pass does not.

## Next Topic

The inference loop has a quiet step hidden inside it. At each token, the model does not hand back a single word; it produces a score for every possible next token, a full distribution over the vocabulary, and something has to turn that distribution into the one token that actually gets chosen. That choosing step is where a model becomes creative or precise, repetitive or surprising, and it is entirely separate from the weights. How a distribution becomes a decision is the subject of **[Topic 18: Sampling](topic-18-sampling.md)**.
