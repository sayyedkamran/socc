# Topic 18: Sampling

## Introduction

[Topic 17: Training vs Inference](topic-17-training-vs-inference.md) ended by pointing at a quiet step buried inside the generation loop. At every position, the model runs a forward pass and hands back not a word but a score for every token in its vocabulary, tens of thousands of numbers at once. Passed through the softmax from [Topic 06: Probability as Output](topic-06-probability-as-output.md), those scores become a probability distribution: this token 12 percent, that one 4 percent, thousands of others a sliver each. That distribution is the model's honest answer to "what comes next." But a distribution is not a word. Something has to reach into that spread of possibilities and pull out the single token that actually gets written to the screen. That step is **sampling**, and this topic is about how it works.

What makes sampling worth its own topic is that it lives entirely outside the weights. Training set the distribution; sampling only decides how to read it. The very same model, with not a single parameter changed, can be turned into a careful and repetitive machine or a loose and inventive one purely by changing how that final choice is made. Two people sending the identical prompt to the identical model can get different answers, and the reason is here, not in the network.

As throughout the chapter, the treatment is recognition-depth. The goal is to understand the handful of knobs that turn a distribution into a decision, see what each one trades away, and recognize why the same prompt behaves so differently depending on how they are set.

## Core Concepts

**Greedy decoding always takes the top token.** The simplest rule is to ignore the distribution's shape and just pick the single most probable token every time, the argmax. This is fast and fully deterministic: the same prompt yields the same output, every time. But it is also brittle. Always grabbing the likeliest next word tends to collapse into flat, repetitive text, looping on stock phrases and dull continuations, because the locally safest choice at every step rarely adds up to interesting writing. Greedy decoding never takes the small risk that leads somewhere fresh.

**Sampling introduces controlled chance.** Instead of always taking the top token, the model can draw from the distribution at random, weighted by probability: a token with 12 percent probability gets picked roughly 12 percent of the time. Now the output can vary, and the model can surface the less obvious but still plausible continuations that make text feel alive. The catch is that the raw distribution has a long tail of thousands of low-probability tokens, and letting all of them stay in play means occasionally drawing something incoherent. The rest of the knobs exist to shape that trade between variety and coherence.

**Temperature sharpens or flattens the distribution.** Temperature is a single number applied to the scores before the softmax. Below 1, it sharpens the distribution: probable tokens grow more probable, the tail shrinks toward nothing, and the output moves toward the deterministic, greedy end. At exactly 0, it collapses to greedy decoding. Above 1, it flattens the distribution: the gap between likely and unlikely tokens narrows, the tail fattens, and the output grows more varied and eventually more chaotic. Temperature does not add information; it only decides how much the model's confidence gaps are respected or ignored.

**Top-k keeps only the k most likely tokens.** Rather than tune the whole curve, top-k truncation simply discards everything except the k highest-probability tokens, renormalizes those, and samples from the survivors. With k of 40, the thousands of tokens in the long tail are cut off entirely, so the incoherent draws become impossible while real variety among the plausible options remains. The weakness is that k is a fixed count: when the model is very confident, 40 candidates may drag in junk, and when it is genuinely uncertain, 40 may be too few.

**Top-p keeps the smallest set that covers probability p.** Nucleus sampling fixes the rigidity of a fixed count. Instead of keeping a set number of tokens, top-p keeps the smallest group whose probabilities add up to p, say 0.9, then samples from that group. The size of the pool now flexes with the model's confidence: when one token dominates, the pool might hold just a few candidates, and when the model is unsure, the pool widens to include many. This adaptive cutoff is why top-p is one of the most common defaults in practice.

**Beam search, and why generation mostly left it behind.** An older idea, beam search, keeps several candidate sequences alive at once and expands the most probable overall, trying to optimize the whole sequence's likelihood rather than each token in isolation. It earns its keep in tasks with one correct answer, like translation, but for open-ended generation it tends to produce bland, oddly repetitive text, because the highest-probability sequence is usually a safe and lifeless one. Modern open-ended generation leans on temperature, top-k, and top-p instead.

## Why It Matters

These knobs explain a behavior that otherwise looks like a bug: ask the same model the same question twice and you may get two different answers. Nothing is broken and nothing changed in the weights. The model produced the same distribution both times, and the sampler, drawing with controlled randomness, simply landed on a different token early on and followed that thread. Once you know sampling sits between the distribution and the text, this stops being mysterious and becomes a setting you control.

It also explains why serious use of these models is task-dependent at the level of the sampler. For anything where correctness and reproducibility matter, extracting a fact, generating code that must run, following a strict format, you push temperature toward 0 and accept the repetitive, deterministic end of the spectrum, because you want the model's single best guess and you want it to be the same every time. For anything where surprise is the point, brainstorming, fiction, generating many distinct options, you raise temperature and widen the pool, trading reproducibility for range. The same weights serve both jobs; only the sampling settings differ.

## Real-World Examples

Every major model API exposes these knobs directly. The `temperature` and `top_p` parameters you set on a request are exactly the controls described here, handed to you rather than hidden. Setting `temperature=0` is the standard move for tasks that must be deterministic, and raising it is the standard move for creative range. The parameters are not decoration; they reach straight into the final step of generation.

The contrast shows up cleanly across tasks. A developer using a model to generate code sets temperature to 0 or near it: they want the most probable, most conventional completion, and they want the same prompt to yield the same code so results are reproducible and debuggable. A writer using the same model to brainstorm story openings does the opposite, raising temperature so that regenerating the prompt keeps producing genuinely different openings instead of the same safe sentence. One model, one prompt each, opposite settings, because the two jobs want opposite things from that final draw.

## How It's Built

The sampler is the `pick_from` step left abstract in the previous topic's generation loop. It takes the raw scores for the next position and turns them into one token.

```python
# sampler.py
import torch

def sample_next(logits, temperature=1.0, top_k=None, top_p=None):
    # logits: raw scores for every token in the vocabulary.
    if temperature == 0:
        return int(logits.argmax())        # greedy: just take the top token

    logits = logits / temperature          # sharpen (<1) or flatten (>1)

    if top_k is not None:                  # keep only the k highest scores
        kth = torch.topk(logits, top_k).values[-1]
        logits[logits < kth] = float("-inf")

    probs = torch.softmax(logits, dim=-1)  # scores become a distribution

    if top_p is not None:                  # keep the smallest set covering p
        ordered, idx = torch.sort(probs, descending=True)
        cutoff = torch.cumsum(ordered, dim=-1) > top_p
        cutoff[0] = False                  # always keep at least one token
        ordered[cutoff] = 0
        probs = torch.zeros_like(probs).scatter(-1, idx, ordered)
        probs = probs / probs.sum()        # renormalize the survivors

    return int(torch.multinomial(probs, 1))  # draw one, weighted by probability
```

The order is the whole story: temperature reshapes the curve, top-k and top-p prune it, the softmax turns what survives into a clean distribution, and a single weighted draw produces the token. Set temperature to 0 and the function short-circuits to greedy, deterministic every time. Leave the filters off and raise temperature and it wanders freely across the vocabulary. The weights are never touched; every lever here acts on the numbers the frozen model already produced.

## Key Takeaways

* At each step the model outputs a full **probability distribution** over the vocabulary, and **sampling** is the separate step that turns that distribution into one chosen token.
* Sampling lives **entirely outside the weights**. The same model behaves as careful or as creative purely by how this final choice is made, which is why identical prompts can give different answers.
* **Greedy decoding** takes the single most probable token every time: deterministic, but prone to flat, repetitive text.
* **Temperature** sharpens the distribution below 1 (toward deterministic, greedy at 0) and flattens it above 1 (toward varied, then chaotic). It respects or ignores the model's confidence gaps rather than adding information.
* **Top-k** keeps a fixed number of top tokens; **top-p (nucleus)** keeps the smallest set covering probability p, letting the candidate pool flex with the model's confidence.
* Sampling settings are **task-dependent**: push temperature toward 0 for facts, code, and reproducibility; raise it for brainstorming and creative range. **Beam search** suits single-answer tasks like translation but was largely left behind for open-ended generation.

## References

* **Holtzman et al.**: *The Curious Case of Neural Text Degeneration* (2019), the paper that introduced nucleus (top-p) sampling and diagnosed why high-probability decoding produces dull, repetitive text.
* **Fan et al.**: *Hierarchical Neural Story Generation* (2018), which popularized top-k sampling for open-ended generation.
* **Jurafsky and Martin**: *Speech and Language Processing*, the decoding and generation sections, for a textbook treatment of greedy, sampling, and beam search.
* **Hugging Face**: *How to generate text* blog post and generation documentation, a practical walkthrough of temperature, top-k, and top-p with runnable code.

## Think About It

1. A model is asked a factual question with `temperature=0` and gives a confidently wrong answer. Raising the temperature makes it sometimes give the right answer instead. Explain what is actually happening in terms of the distribution, and why this does not mean higher temperature makes the model "smarter."
2. Top-k with k of 40 and top-p with p of 0.9 can behave very differently on the same distribution. Describe one situation where top-k drags in bad tokens that top-p would have excluded, and one where top-k cuts off good tokens that top-p would have kept.
3. Greedy decoding maximizes the probability of each token individually, yet the text it produces is often worse than text from sampling. How can choosing the locally most probable token at every step lead to a globally worse result? Connect this to why beam search, which optimizes whole-sequence probability, still disappoints on open-ended writing.

## Next Topic

Sampling decides how to read the distribution, but it takes the distribution itself as given. That raises a deeper question the last several topics have quietly set aside: why does the distribution lean the way it does? A raw pretrained model, fresh off the factory floor of [Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md), will happily continue a prompt in ways that are unhelpful, untrue, or worse, because it was only ever trained to predict likely text, not good text. Turning a raw next-token predictor into something that tries to be helpful and harmless is a deliberate act of shaping, and it is the subject of **[Topic 19: Alignment](topic-19-alignment.md)**.
