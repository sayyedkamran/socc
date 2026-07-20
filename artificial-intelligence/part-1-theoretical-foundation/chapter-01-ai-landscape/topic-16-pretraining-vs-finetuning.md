# Topic 16: Pretraining vs Finetuning

## Introduction

[Topic 15: Large Language Models](topic-15-large-language-models.md) ended with a strange product. After months of training and millions of dollars of compute, the pipeline produces a model that knows an astonishing amount and helps with almost nothing. Ask the raw model "What is the capital of France?" and it might answer "Paris." It might just as well continue with "What is the capital of Germany? What is the capital of Italy?", because it has seen thousands of quiz sheets, and a list of questions is a perfectly plausible continuation of a question. The model is not being difficult. It is doing exactly what it was trained to do: predict the next token of a document. Nobody told it that the document is a conversation and that its role is to be the helpful participant.

Closing the gap between "knows everything" and "helps you" turns out not to require another mountain of data or another training run of the same scale. It requires a second, much smaller phase of training with a completely different character. The industry settled on a two-phase recipe, and the names of the phases are the title of this topic: **pretraining** builds the knowledge, **finetuning** shapes the behavior. Every assistant you have ever used, including the one you may be reading this alongside, is a pretrained model that went through finetuning. Understanding the split explains a remarkable amount about the modern AI landscape: why model families ship in "base" and "instruct" versions, why startups can build specialized models without billion-dollar budgets, and why an LLM can sound confident about medicine it half-knows.

As throughout the chapter, the treatment is recognition-depth. The full machinery of both phases returns in later parts of the curriculum; here the goal is to recognize the two phases, know what each one contributes, and never again confuse them.

## Core Concepts

### Pretraining: the education

Pretraining is the phase [Topic 15: Large Language Models](topic-15-large-language-models.md) described. The model starts with random weights and learns next-token prediction over a huge slice of the written internet: web pages, books, code, papers, forums. The process is **self-supervised**: no human labels anything, because the text itself provides the answer key. Every position in every document is a tiny exam question ("given everything so far, what comes next?") with the correct answer sitting right there in the data.

Three properties define pretraining:

* **Scale**: trillions of tokens, thousands of GPUs, weeks to months of wall-clock time, costs in the tens or hundreds of millions of dollars.
* **Generality**: the data is not curated toward any task. The model absorbs grammar, facts, reasoning patterns, coding conventions, and the statistical texture of a thousand genres, all at once.
* **Output**: the result is called a **base model** (or foundation model). It is a pure text simulator. Given a beginning, it produces a statistically plausible continuation, with no notion of "user," "assistant," or "task."

A useful analogy: pretraining is a general education. Twenty years of reading everything, with no job in mind.

### Finetuning: the job training

Finetuning takes the finished base model and continues training it, using the same gradient descent of [Topic 07: Gradient Descent](topic-07-gradient-descent.md), on a small, carefully curated dataset that demonstrates the behavior you want. The weights are not rebuilt from scratch; they are nudged. The knowledge is already in there. Finetuning teaches the model what to do with it.

The most important variant is **supervised finetuning (SFT)**, also called instruction tuning. The dataset is tens of thousands to a few million examples, each a pair: an instruction ("Summarize this paragraph in one sentence") and a high-quality response written or vetted by humans. Training on these pairs teaches the model a new default assumption about every document it sees: this is a conversation, a request is being made, and your job is to produce the response. The same next-token machinery, pointed at a new target.

Everything about finetuning inverts the pretraining profile:

* **Scale**: thousands to millions of examples instead of trillions of tokens; hours to days instead of months; costs from a few dollars to a few thousand.
* **Specificity**: the data is deliberately narrow. It encodes a format, a tone, a role, or a domain.
* **Output**: an **instruct model** (or chat model), the kind you actually converse with.

The analogy completes: finetuning is job training. A few weeks of onboarding that turn twenty years of education into a useful employee.

### What finetuning changes, and what it cannot

The single most important mental model in this topic: **pretraining installs knowledge, finetuning shapes behavior.** Finetuning is far too small to teach the model substantial new knowledge. A few hundred thousand examples cannot compete with trillions of pretraining tokens. What those examples can do is select and amplify capabilities that pretraining already built. The base model already knows how to summarize, because it has seen countless summaries; SFT teaches it that when asked for a summary, it should actually produce one instead of continuing the essay.

The limit cuts the other way too. If knowledge is missing or wrong in the base model, finetuning on polite question-answer pairs will not fix it; it will produce a model that delivers the same wrong answer more confidently and in better formatting. This is one root of the fluent-nonsense problem flagged in [Topic 06: Probability as Output](topic-06-probability-as-output.md).

### Domain finetuning

Instruction tuning is not the only use of the second phase. The same technique specializes a model into a domain: continue training on medical literature and clinical Q&A, and you get a medical model; on legal contracts, a legal assistant; on a company's codebase and style guide, an internal coding helper. This is the economic magic of the two-phase recipe: the expensive phase is done once by a large lab, and thousands of teams pay only for the cheap phase. The open-weight base models on Hugging Face exist precisely so that others can finetune them.

### Parameter-efficient finetuning: LoRA

Full finetuning updates every weight in the model, which for a large model still demands serious hardware. A family of shortcuts called **parameter-efficient finetuning (PEFT)** updates only a small fraction of the weights. The most famous is **LoRA** (Low-Rank Adaptation), which freezes the base model entirely and trains small add-on matrices alongside it, often less than one percent of the original parameter count. The result trains on a single consumer GPU and ships as a tiny file that plugs into the shared base model. Recognition-depth is enough here: when you see "LoRA" attached to a model on Hugging Face, you are looking at a lightweight behavioral patch on a frozen foundation.

## Why It Matters

The two-phase split is the economic and practical structure of the entire industry.

First, it explains the model listings you now encounter everywhere. Search Hugging Face for Llama and you find `Llama-3.1-8B` and `Llama-3.1-8B-Instruct`. Same architecture, same pretraining, same knowledge. The first is the raw text simulator, the material for builders; the second has been through SFT and will actually answer you. Downloading a base model and expecting a chatbot is one of the most common beginner surprises in the field.

Second, it explains who can afford to do what. Pretraining a frontier model is restricted to a handful of organizations on the planet. Finetuning one is within reach of a student with a weekend and a rented GPU, and with LoRA, sometimes a laptop. The entire open-model ecosystem of specialized coding models, medical models, and roleplay models is a finetuning economy built on a few expensive pretrained foundations.

Third, it sets up the question of quality. SFT can teach a model to answer in the right format, but "a well-formatted answer" and "a good answer" are not the same thing, and deciding which of two fluent answers is better turns out to need machinery beyond anything in this topic. That gap is where [Topic 19: Alignment](topic-19-alignment.md) will pick up.

## Real-World Examples

**ChatGPT's origin story.** GPT-3, released in 2020, was a base model. Impressive in demos, awkward in practice: it needed carefully crafted prompts and often rambled. In 2022 OpenAI applied instruction tuning (plus alignment techniques covered in [Topic 19: Alignment](topic-19-alignment.md)) and released the result as ChatGPT. The knowledge was two years old; the behavior was new. The product that triggered the AI boom was, at its core, a finetuning success story.

**Base vs instruct on your own machine.** Pull both `llama3.1:8b-text` (base) and `llama3.1:8b` (instruct) with a tool like Ollama and prompt each with "What is the capital of France?" The instruct model answers. The base model may answer, continue the quiz, or drift into an essay about European geography. Same weights lineage, different second phase; no demonstration makes the distinction more visceral.

**Med-PaLM.** Google took its general PaLM model and finetuned it with medical instruction data, producing Med-PaLM, one of the first models to pass the threshold on US medical licensing exam questions. The general model already held most of the medical knowledge from pretraining; the finetuning taught it to deploy that knowledge in the format and register of clinical Q&A.

**Code assistants.** Models like Code Llama and the coding variants of Qwen start from a general base model and receive continued training weighted heavily toward source code, then instruction tuning on programming tasks. The pattern repeats across every vertical: one foundation, many trades.

## How It's Built

The two phases as a pipeline, with the contrast in data made explicit:

```
PHASE 1: PRETRAINING (once, by a large lab)
  data:    ~15 trillion tokens of raw internet text, unlabeled
  method:  self-supervised next-token prediction
  compute: thousands of GPUs, weeks to months
  output:  BASE MODEL  (a text simulator)

PHASE 2: FINETUNING (many times, by anyone)
  data:    ~10 thousand to ~1 million instruction-response pairs, curated
  method:  supervised next-token prediction on demonstrations
  compute: one to a few GPUs, hours to days
  output:  INSTRUCT MODEL  (an assistant)
```

The asymmetry deserves a pause: the data ratio between the phases can exceed a million to one, and yet the second phase determines almost everything about the experience of using the model. A conceptual sketch of an SFT training example shows why the same machinery serves both phases:

```
# One SFT training example (conceptual)
prompt:   "### Instruction:\nSummarize in one sentence: <paragraph>\n### Response:\n"
target:   "The paragraph argues that ..."

# Training still minimizes next-token prediction error,
# but only over the response tokens. The model learns:
# after an instruction, produce the response.
```

Nothing about the loss function or the gradient descent of [Topic 09: Backpropagation](topic-09-backpropagation.md) changed. Only the data changed, and the data is the curriculum.

## Key Takeaways

* Modern assistants are built in two phases: **pretraining** (huge, general, self-supervised, produces a base model) and **finetuning** (small, curated, supervised, produces an instruct model).
* Pretraining installs knowledge; finetuning shapes behavior. Finetuning cannot add substantial knowledge, and it cannot fix knowledge the base model lacks.
* **Supervised finetuning (SFT)** on instruction-response pairs is what turns a text simulator into an instruction-follower.
* The same technique specializes models into domains (medicine, law, code), which is why one expensive foundation supports thousands of cheap derivatives.
* **LoRA** and other parameter-efficient methods finetune by training a tiny add-on to a frozen base, putting customization within reach of individuals.
* "Base" and "instruct" variants of a model share knowledge and differ only in the second phase.

## References

* **Ouyang et al.**: *Training language models to follow instructions with human feedback* (2022), the InstructGPT paper, the canonical account of turning GPT-3 into an instruction-follower.
* **Hu et al.**: *LoRA: Low-Rank Adaptation of Large Language Models* (2021), the paper behind the most widely used parameter-efficient finetuning method.
* **Raschka**: *Build a Large Language Model (From Scratch)* (2024), whose later chapters walk through implementing both pretraining and finetuning by hand.
* **Hugging Face**: *LLM Course*, the finetuning chapters, a practical companion showing SFT on open models.

## Think About It

1. A company finetunes an open base model on its internal support tickets and finds the model now answers in perfect company style but still invents product features that do not exist. Using the knowledge-vs-behavior split, explain why the finetuning produced exactly this result and what it would actually take to fix it.
2. The base model has seen far more question-answer text during pretraining than it ever sees during SFT. So why does it not already behave like an assistant? What, precisely, is SFT teaching that pretraining data left ambiguous?
3. LoRA freezes every original weight yet visibly changes the model's behavior. Reconcile this with the picture from [Topic 08: Deep Learning](topic-08-deep-learning.md) of a network's function living in its weights.

## Next Topic

Both phases of the recipe belong to the factory. Whether the run costs a hundred million dollars or a weekend, it happens once, and it ends: the weights are frozen, the learning stops, and the model ships. What happens after that, every single time you type a message and watch an answer stream back, is a different process with different costs, different hardware, and different bottlenecks. The distinction between the factory and the product in your hands is the subject of **[Topic 17: Training vs Inference](topic-17-training-vs-inference.md)**.
