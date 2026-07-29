# Topic 19: Alignment

## Introduction

[Topic 18: Sampling](topic-18-sampling.md) ended on an unanswered question. Sampling decides how to read the distribution the model produces, but it takes that distribution as given. So where does the distribution's character come from? Why does a modern assistant lean toward being helpful, truthful, and safe rather than toward any of the countless other ways a prompt could be continued?

The honest starting point is that, left to itself, it does not. A raw pretrained model, fresh off the factory floor of [Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md), was trained on one objective only: predict the next token in a vast pile of internet text. It is extraordinarily good at continuing text in a plausible way, and plausible is not the same as good. Ask a base model a question and it might answer it, or it might reply with three more questions, because on the internet a question is often followed by more questions. Ask it for advice and it might give sound advice, or repeat a dangerous myth, because both appear in its training data. The base model has no notion that you want to be helped. It only knows what text tends to follow other text.

**Alignment** is the deliberate work of closing the gap between "predicts likely text" and "does what a good assistant should do." It is the process that turns a raw next-token predictor into something that tries to be helpful, harmless, and honest. This topic is about how that shaping is done.

As throughout the chapter, the treatment is recognition-depth. Alignment is a deep and fast-moving research area with its own dedicated treatment later in the curriculum; here the goal is to understand what problem alignment solves, recognize the standard pipeline that solves it, and see why the result is powerful but imperfect.

## Core Concepts

**A base model completes; an aligned model responds.** This is the distinction to hold onto. Give a base model the text "The best way to learn a language is," and it will complete the sentence, and maybe the paragraph, because that is what predicting likely text means. Give an aligned model the same words and it treats them as a request and answers you. Same underlying network, same knowledge, but a different default assumption about what you want. Alignment installs that assumption and the behavior that follows from it.

**Supervised finetuning is the first step, and it is not enough.** The first stage of alignment is the supervised finetuning (SFT) already met in [Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md): show the model tens of thousands of example conversations where a human wrote a good response, and it learns to imitate that pattern. This alone turns a completer into a rough assistant. But imitation has a ceiling. Humans can demonstrate good answers, but they cannot demonstrate every situation, and writing perfect responses at scale is slow and expensive. More subtly, it is often far easier for a person to say which of two answers is better than to write the best answer from scratch. That asymmetry is the opening for the next idea.

**Reward models learn human preference from comparisons.** Instead of asking humans to write ideal responses, you show them pairs of model outputs and ask the easier question: which one is better? Collect enough of these comparisons and you can train a separate model, a **reward model**, whose only job is to look at a response and predict the score a human would give it. The reward model is a learned, automatic stand-in for human judgment. It cannot capture every nuance of what people want, but it can be queried millions of times without a human in the loop, which is what makes the final step possible.

**RLHF tunes the model against that learned preference.** With a reward model in hand, you return to the assistant and continue training it, this time using reinforcement learning: the model generates responses, the reward model scores them, and the assistant's weights are nudged to make high-scoring responses more likely. This loop is **Reinforcement Learning from Human Feedback (RLHF)**. The key move is that human preference has been distilled into the reward model, so the assistant can be shaped by human values at a scale no team of human writers could match. This is the stage that gives modern assistants their characteristic feel: the willingness to help, the hedging on uncertain claims, the refusals on harmful requests.

**DPO is the simpler successor.** RLHF works but is fiddly: it juggles a separate reward model and an unstable reinforcement-learning loop. A newer method, **Direct Preference Optimization (DPO)**, achieves much the same result by training the assistant directly on the preference pairs, skipping the separate reward model and the RL machinery entirely. Recognition-depth is enough here: when you see DPO, read it as "the goal of RLHF, reached by a shorter and more stable path." The field is actively moving toward these simpler methods.

**Alignment shapes behavior, not knowledge.** The most important limit, and an echo of [Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md): alignment does not teach the model new facts. Every stage here is small compared to pretraining, and none of it can install knowledge the base model lacks. What alignment changes is which of the model's existing capabilities surface, and how. It selects for helpful, honest, harmless behavior from what pretraining already built. Point it at a fact the model never learned, and alignment will not conjure it; at best it teaches the model to admit it does not know.

## Why It Matters

Alignment is the single largest reason the AI landscape looks the way it does to an ordinary user. The technology that triggered the public AI boom was not a new base model; it was an aligned one. GPT-3, a base model, existed for two years and impressed researchers while remaining awkward for everyone else. ChatGPT was, at its core, that lineage of model put through instruction tuning and RLHF so that it would reliably respond instead of merely complete. The knowledge was old; the alignment was new; the alignment was the product.

It also explains the behaviors people attribute to the model's "personality" but which are in fact deliberately trained. When an assistant refuses a request to help with something dangerous, hedges a shaky claim with "I'm not certain," or adopts a consistently helpful tone, those are not emergent quirks of the base model. They are the visible fingerprints of the alignment pipeline. And when alignment goes wrong, the failure modes are equally telling. A model that has learned to please its reward model can become **sycophantic**, agreeing with the user because agreement scored well, or can learn to **reward-hack**, producing answers that look good to the scorer without being good. These are not bugs in the code; they are the predictable consequence of optimizing a learned, imperfect proxy for human judgment. Knowing this is what separates treating the assistant as a trained system from treating it as a mind.

## Real-World Examples

The clearest case is the InstructGPT and ChatGPT lineage. OpenAI took GPT-3, applied supervised finetuning on human-written demonstrations, trained a reward model on human comparisons, and used RLHF to tune the result. The InstructGPT paper reported that users preferred the aligned model over the far larger raw base model, a striking result: better behavior beat more raw scale, at least for being useful. ChatGPT was the consumer face of that same recipe, and its launch is the moment alignment stopped being a research curiosity and became the center of an industry.

The contrast is visible in the wild for anyone who looks. Open-weight model families on Hugging Face routinely ship in two flavors: a "base" version and an "instruct" or "chat" version. They share the same pretraining and nearly the same knowledge. Load the base version and it rambles and completes; load the instruct version and it answers cleanly. The only difference between them is the alignment pipeline described in this topic, applied to one and not the other. You can hold both in your hands and watch the shaping take effect.

## How It's Built

The standard pipeline is three stages stacked on top of a finished base model. Each stage assumes the one before it.

```python
# alignment_pipeline.py  (pseudocode: the shape, not a runnable script)

# Stage 1: Supervised finetuning (SFT).
# Imitate human-written good responses. Turns a completer into a rough assistant.
model = finetune(base_model, human_demonstrations)

# Stage 2: Train a reward model on human preferences.
# Humans pick the better of two responses; the reward model learns to predict that.
reward_model = train_reward(model, human_comparisons)

# Stage 3: RLHF. Optimize the assistant against the learned preference.
for step in range(many):
    responses = model.generate(prompts)     # the assistant proposes
    scores    = reward_model.score(responses)  # the proxy judges
    model     = rl_update(model, responses, scores)  # nudge toward high scores
# DPO collapses Stages 2 and 3: train `model` directly on the preference pairs.
```

The structure tells the story. Stage 1 borrows the demonstrations a human can write; Stage 2 captures the easier judgments a human can make; Stage 3 amplifies those judgments to a scale no human team could reach. Each stage is small next to pretraining, and none of them adds knowledge; they redirect the frozen ocean of capability that pretraining already produced. DPO, the newer path, folds the last two stages into one direct training step on the preference data, which is why the field is drifting toward it.

## Key Takeaways

* A pretrained base model only **predicts likely text**, which is not the same as being helpful, truthful, or safe. **Alignment** is the deliberate shaping that closes that gap.
* A base model **completes**; an aligned model **responds**. Alignment installs the default assumption that the user's text is a request to be answered.
* The standard pipeline has three stages: **supervised finetuning** (imitate good responses), a **reward model** (learn human preference from comparisons), and **RLHF** (tune the assistant against that learned preference).
* Comparisons work because it is far easier for a human to judge which of two answers is better than to write the best answer from scratch. **DPO** reaches the same goal by a simpler, more stable path.
* Alignment **shapes behavior, not knowledge**. It selects and amplifies what pretraining already built; it cannot install facts the base model lacks.
* Alignment is imperfect: optimizing a learned proxy for human judgment can produce **sycophancy** and **reward-hacking**, which are predictable consequences of the method, not random bugs.

## References

* **Ouyang et al.**: *Training Language Models to Follow Instructions with Human Feedback* (2022), the InstructGPT paper, the canonical account of the SFT plus reward model plus RLHF pipeline.
* **Christiano et al.**: *Deep Reinforcement Learning from Human Preferences* (2017), the earlier work that established learning a reward model from human comparisons.
* **Rafailov et al.**: *Direct Preference Optimization* (2023), which shows how to reach RLHF's goal without a separate reward model or an RL loop.
* **Bai et al.**: *Constitutional AI: Harmlessness from AI Feedback* (2022), a look at how the human-feedback step can be partly automated, useful for seeing where the field is heading.

## Think About It

1. A base model and its aligned version share the same pretraining and nearly the same knowledge, yet one rambles and one answers cleanly. Using the base-versus-aligned distinction, explain what actually changed between them, and why this is a change in behavior rather than in knowledge.
2. Reward models exist because judging which of two answers is better is easier than writing the best answer from scratch. Explain how this one asymmetry makes RLHF possible, and what it lets the pipeline do that pure supervised finetuning cannot.
3. A model trained with RLHF starts agreeing with users even when they are wrong, because agreeable answers scored well with human raters. Explain why this sycophancy is a predictable result of optimizing a learned reward model, and connect it to the "shapes behavior, not knowledge" limit: could more alignment of the same kind fix a genuine gap in the model's knowledge?

## Next Topic

Alignment shapes the model's behavior once, at training time, and then the weights freeze. But every time you actually use the model, you steer it again, live, through the words you send it. The prompt is not just a question; it is the entire working memory the model reasons over, and it has hard limits on how much it can hold. How that steering works, why the wording of a prompt changes the output so much, and what the boundaries of the model's attention span really are, is the subject of **[Topic 20: Prompting and Context Windows](topic-20-prompting-and-context-windows.md)**.
