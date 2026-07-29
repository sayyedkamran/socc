# Topic 15: Large Language Models

## Introduction

Walk the chain one more time, because every link in it was forged for this moment.

It starts with raw text, which a neural network cannot touch, because networks compute with numbers. So [Topic 10: Tokenization](topic-10-tokenization.md) chops the text into tokens and hands each one an integer ID. But an ID is just a label; token 5,072 is not "more similar" to token 5,073 in any meaningful way, and a model that wants to generalize needs meaning it can measure. So [Topic 11: Embeddings](topic-11-embeddings.md) turns each ID into a vector, placing tokens in a geometric space where similar meanings sit close together. But a word's meaning is not fixed; "bank" near "river" is not "bank" near "loan," so each token's vector must be updated by its context. The recurrent networks of [Topic 12: Sequence Models](topic-12-sequence-models.md) tried to carry context through a single squeezed summary and hit two walls: the bottleneck and the inability to parallelize. So [Topic 13: Attention](topic-13-attention.md) let every token look directly at every other token and pull in exactly the context it needs, all at once. And because one round of looking is not enough to build deep understanding, [Topic 14: Transformers](topic-14-transformers.md) wrapped attention in a repeatable block, attention then feed-forward, and stacked it dozens of layers high, with residual connections keeping the whole tower trainable.

Every step in that chain was an answer to a problem the previous step created. What remains is the question the finished machine poses: the transformer is an architecture that gets better the bigger you build it, so what happens if you build it very, very big? Around 2019 a handful of labs decided to find out. They took the decoder-only branch, grew it by factors of ten, and trained it on one deceptively humble task, predicting the next token, over a meaningful fraction of everything humans have written and put on the internet.

What came out was not a better autocomplete. It was a single model that translates between languages, writes working code, answers questions, summarizes documents, and holds conversations, none of which appeared as explicit training goals. That artifact is the **large language model**, or **LLM**, and this topic is where the whole chapter converges. As throughout Chapter 1, the treatment is recognition-depth; the full training story lives in later chapters.

## Core Concepts

### One objective: predict the next token

Strip away the mystique and an LLM is trained on a single task. Show the model a sequence of tokens, ask it to output a probability distribution over the vocabulary for what comes next, and score it with the loss machinery from [Topic 07: Gradient Descent](topic-07-gradient-descent.md). The correct next token is right there in the text, so the training data labels itself. This is the self-supervised trick from [Topic 03: Learning Paradigms](topic-03-learning-paradigms.md) deployed at planetary scale: no human annotators, just text and the demand to continue it.

The humble surface hides a brutal demand. To predict the next token well, everywhere, across all the text humanity produces, shallow statistics are not enough. Predicting the last word of "the capital of Australia is" requires a fact. Predicting the next line of a Python function requires the logic of the code above it. Predicting who "she" refers to in paragraph four requires tracking characters across a story. Next-token prediction is not one task; it is every task that has ever been written down, disguised as one task. Getting good at it forces the model to absorb grammar, facts, style, and fragments of reasoning, because those are the cheapest ways to drive the loss down.

### Scale: the three dials

"Large" is not decoration. Three quantities define the regime: **parameters** (the weight count, from hundreds of millions to hundreds of billions and beyond), **data** (trillions of tokens of text), and **compute** (the total arithmetic spent in training, measured in FLOPs and, in practice, in months of GPU-cluster time). The transformer made all three dials turnable at once: its parallelism, from [Topic 14: Transformers](topic-14-transformers.md), means more hardware directly buys more training.

### Scaling laws: loss you can forecast

The surprise that launched the LLM era is that turning those dials produces *predictable* returns. Plot loss against parameters, data, or compute on log-log axes and you get strikingly straight lines: **scaling laws**. Kaplan and colleagues at OpenAI documented this in 2020, and the Chinchilla work at DeepMind in 2022 refined the recipe, showing that parameters and data should grow together (roughly twenty tokens of training data per parameter) rather than parameters alone. The consequence is strange and powerful: labs could forecast the loss of a model costing tens of millions of dollars before spending the money. Scaling turned from a gamble into an engineering plan.

### Emergent abilities

Loss falls smoothly, but *capabilities* do not always appear smoothly. Some skills, multi-step arithmetic, translating a language pair never seen paired in training, following written instructions, seem to be nearly absent in smaller models and then show up as models cross scale thresholds. These are called **emergent abilities**. Whether they are truly sudden or an artifact of how we measure (a model can be "partially right" in ways a pass/fail benchmark scores as zero) is an active debate, and honest researchers disagree. What is not debated: models at the top of the scale do things their smaller siblings simply cannot, and nobody wrote those skills in.

### In-context learning

The strangest emergent skill deserves its own name. Give a large model a few examples of a task inside its input, English word then French word, three times over, and it continues the pattern for a new word, *with no weight updates at all*. This is **in-context learning**: the model learns the task from the prompt, at inference time, using only the attention machinery of [Topic 13: Attention](topic-13-attention.md) over the examples in front of it. Training, in the gradient-descent sense from [Topic 07: Gradient Descent](topic-07-gradient-descent.md), happened months earlier. Yet something that looks like learning happens in the forward pass. This single property is why one frozen model can be steered to thousands of tasks by changing nothing but its input, and it is the foundation for everything in [Topic 20: Prompting and Context Windows](topic-20-prompting-and-context-windows.md).

### The base model

The direct product of all this is the **base model** (or pretrained model): a next-token predictor of enormous power and no manners. Ask it a question and it may answer, or continue with three more questions, because pages of questions exist on the internet and continuation is its only goal. It is a simulator of text, not an assistant. Keep this distinction in a firm grip; it is the cliffhanger this topic ends on.

## Why It Matters

The LLM ended the one-model-per-task era. Before, sentiment analysis, translation, summarization, and question answering each meant a dedicated dataset, a dedicated architecture, and a dedicated team. After, they are all the same act: put text in front of a single pretrained model. The economics of building with language flipped from "collect ten thousand labeled examples" to "write a good prompt," which is why AI capability spread through the software industry in a few years rather than a few decades.

Scaling laws also changed who could play and how they planned. When capability is a forecastable function of compute, frontier AI becomes a capital expenditure race, which explains the multi-billion-dollar clusters, the GPU shortages, and the strategic weight nations now place on chips. And emergent abilities cut the other way: they mean capability forecasts have error bars. If skills can appear unannounced at the next scale-up, so can risks, which is a core argument behind the safety and alignment work waiting in [Topic 19: Alignment](topic-19-alignment.md).

For the working developer, the practical shift is this: the model is no longer something you train; it is something you *use*, and using it well is a skill of its own. The rest of this chapter, from [Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md) through [Topic 23: AI Agents](topic-23-ai-agents.md), is essentially a tour of the ways people harness this one artifact.

## The Story So Far: From GPT-1 to Today

The chapter so far has treated the LLM as an idea. It is also a story, one of the fastest technology stories ever told, and knowing it turns a pile of model names into a plot. This section is a timeline with the reasoning attached: what each era got right, what it got wrong, and how the next era answered. A note before starting: this section is written as of mid-2026, and this field moves fast enough that the last few paragraphs will age. The arc will not.

### 2017 to 2018: the recipe is written

The transformer paper arrives in 2017 under the most confident title in machine learning, *Attention Is All You Need*. A year later OpenAI tests the claim with **GPT-1** (June 2018, 117 million parameters): pretrain a decoder-only transformer on unlabeled books, then finetune it briefly per task, and it beats carefully engineered task-specific systems. Months later Google's **BERT** does the same with the encoder branch and breaks every benchmark it touches. The recipe, generative pretraining, is now public. Nobody outside a few labs notices. The guiding philosophy had been articulated years earlier by reinforcement learning pioneer Rich Sutton in his essay *The Bitter Lesson*: across seventy years of AI, "general methods that leverage computation are ultimately the most effective." The transformer was about to become the ultimate test of that claim.

### 2019: GPT-2 and the first controversy

**GPT-2** (1.5 billion parameters) writes coherent multi-paragraph text, and OpenAI makes an unprecedented move: it withholds the full model, citing fears of misuse for fake news and spam, releasing it in stages over the year. Critics call it a publicity stunt; defenders call it prudence. Either way, the episode plants a question that never leaves the field: what do you do when a text predictor gets *good*? The staged release also marks the moment "AI safety" moves from philosophy seminars into release schedules.

### 2020: GPT-3, scaling laws, and the first public LLM

Two things happen in 2020 that define everything after. First, Kaplan and colleagues publish the scaling laws described in Core Concepts: loss falls predictably with scale. Second, OpenAI acts on them with **GPT-3** (175 billion parameters, a hundred times GPT-2), and in June 2020 opens it to the public through an API, making it the first broadly available LLM. Developers discover in-context learning hands-on and a cottage industry of "GPT-3 demos" floods the internet.

The shortcomings are just as visible. GPT-3 is a base model with no manners: it continues text rather than following instructions, so users must trick it with elaborate prompt formats. It **hallucinates**, stating falsehoods with perfect fluency, because next-token prediction rewards plausibility, not truth. It absorbs the biases and toxicity of its training data. Its knowledge freezes at its training cutoff. And its context window, 2,048 tokens, means it forgets the start of a long document by the end.

### 2022: the shortcomings get answered, and the world finds out

Each flaw gets a targeted fix, and the fixes are the syllabus for the rest of this chapter. The instruction problem falls first: OpenAI's **InstructGPT** work shows that a second training phase, finetuning on human demonstrations plus **reinforcement learning from human feedback (RLHF)**, turns a text continuator into an instruction follower ([Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md) and [Topic 19: Alignment](topic-19-alignment.md) tell this story). DeepMind's Chinchilla result quietly fixes the training recipe itself: most giant models were data-starved.

Then, on November 30, 2022, OpenAI wraps an instruction-tuned GPT-3.5 in a chat box and ships it as "a research preview." **ChatGPT** reaches an estimated hundred million users in two months, the fastest adoption of any consumer application in history to that point. Nothing fundamental was new; packaging RLHF behind a conversation window was enough. The LLM stops being a research artifact and becomes a public utility, and every major technology company declares an emergency.

### 2023: the frenzy

The most compressed year in AI history. **GPT-4** (March 2023) jumps visibly in reasoning and accepts images. Anthropic, founded by former OpenAI researchers with a safety focus, ships **Claude**. Google merges its AI divisions and answers with what becomes **Gemini**. Meta takes the opposite bet: it releases the weights of **Llama** openly, and an open-weight ecosystem explodes around it, putting near-frontier models in the hands of anyone with a GPU.

The anxiety is as loud as the excitement. An open letter signed by thousands, Elon Musk among them, calls for a six-month pause on "giant AI experiments." Geoffrey Hinton, a godfather of the deep learning revolution from [Topic 08: Deep Learning](topic-08-deep-learning.md), resigns from Google so he can speak freely about the risks of the technology he helped create. Meanwhile the practical shortcomings keep getting engineering answers: context windows stretch from thousands of tokens toward hundreds of thousands, retrieval bolts fresh knowledge onto frozen weights ([Topic 21: RAG](topic-21-rag.md)), and models learn to call external tools ([Topic 22: Tool Use and Function Calling](topic-22-tool-use-and-function-calling.md)). Andrej Karpathy captures the developer-side shift in a line that becomes a slogan: "The hottest new programming language is English."

### 2024: the wall and the turn

By late 2024 a problem looms: the internet is finite. Frontier labs have already consumed most of the high-quality public text, and Ilya Sutskever, OpenAI's former chief scientist, tells the NeurIPS conference that "pre-training as we know it will unquestionably end." Data, not compute, is the binding constraint, and scaling-by-enlargement alone stops being a strategy.

The field turns to a new dial: **inference-time compute**. OpenAI's **o1** (September 2024) is trained with reinforcement learning to produce long private chains of reasoning before answering, and it trades seconds of extra thinking for large gains on math, science, and code. A second scaling law emerges: capability grows not only with training compute but with how long the model thinks at answer time. "Reasoning models" become a category, and every lab builds one.

### 2025: the efficiency shock and the year of agents

In January 2025 a Chinese lab few outsiders watched, **DeepSeek**, releases **R1**, an open-weight reasoning model near the frontier, trained for a small fraction of assumed frontier cost. Venture capitalist Marc Andreessen calls it "AI's Sputnik moment." Nvidia loses roughly six hundred billion dollars of market value in a single day, the largest one-day loss for any company to that point, as markets briefly panic that capability no longer requires giant budgets. The panic fades (efficiency gains historically *increase* total compute demand), but the lesson sticks: the frontier is contestable, and China's open-weight labs, DeepSeek, Alibaba's Qwen, Moonshot's Kimi, become permanent fixtures.

The other 2025 story is **agents**. Models stop just answering and start *doing*: running terminals, editing codebases, browsing, and chaining hours of multi-step work ([Topic 23: AI Agents](topic-23-ai-agents.md)). Agentic coding tools become the first LLM products with undeniable economic bite. Meanwhile the capital race goes vertical: multi-hundred-billion-dollar datacenter programs are announced, and electricity, not just chips, enters the conversation as a limiting factor.

### 2026: the state of play

As of mid-2026 the frontier is a handful of labs shipping on a cadence of months. **OpenAI** iterates its GPT-5 generation, splitting it into tiers by capability and cost, and holds the largest consumer mindshare through ChatGPT; its strengths are distribution and product velocity, its weakness a burn rate that assumes the future arrives on schedule. **Google** fields the Gemini 3 line with two advantages nobody else has, its own TPU silicon and its own ocean of data, offset by the innovator's dilemma of a search business the technology threatens. **Anthropic** pushes the Claude line with a reputation built on coding, agentic work, and a stated safety mission; its enterprise footprint is deep, its consumer footprint thinner. **Meta** keeps betting on open weights and its billions of app users, still chasing the top of the leaderboards. **xAI** converts the fastest datacenter buildout in the industry into rapid Grok releases, long on compute, shorter on product surface. And the open-weight bloc, DeepSeek, Qwen, Kimi, Mistral in Europe, keeps compressing the gap between free and frontier, with Chinese labs now demonstrating frontier training runs on domestic silicon, a direct response to US chip export controls.

Million-token context windows, multimodal input, and reasoning modes are now table stakes. The benchmark wars have moved from trivia to agentic tasks: can the model complete real software tickets, real research, real workflows.

### Constraints, near future, far future

Four constraints shape what comes next. **Chips**: advanced GPUs are export-controlled geopolitical assets, and demand outruns supply. **Power**: frontier training now needs gigawatt-scale electricity, so AI roadmaps have become energy infrastructure roadmaps. **Data**: the public internet is largely consumed; labs lean on synthetic data, licensed corpora, and RL-generated experience, each with open questions. **Money**: hundreds of billions in capital expenditure chase revenue that, while growing fast, has not yet caught up, and whether this is 1999 or 1994 is the industry's favorite argument.

Near-term, the safe bets are the visible trend lines: agents doing longer and more autonomous work, capability per dollar falling steadily, small on-device models handling more locally, and specialization for medicine, law, and science. Far-term, the field splits into camps and argues in public. Anthropic's Dario Amodei writes of AI becoming "a country of geniuses in a datacenter" possibly within this decade, compressing decades of scientific progress. Demis Hassabis and Sam Altman have offered similarly aggressive timelines for artificial general intelligence. Skeptics like Yann LeCun counter that next-token prediction alone cannot reach real understanding and that new architectures are needed. [Topic 24: AI Is Not Magic](topic-24-ai-is-not-magic.md) will give you the tools to hold that debate without taking anyone's word for it.

The honest summary: nobody knows, the people building it disagree, and the experiment is running either way.

## Real-World Examples

**Claude.** The model you may be reading alongside is a decoder-only transformer LLM from Anthropic. Every mechanism in this chapter, tokenization, embeddings, attention over your entire conversation, is running each time it replies.

**Open-weight models.** Meta's Llama family, Mistral's releases, and others publish trained weights that anyone can download and run. They turned the LLM from a service you rent into an artifact you can hold, study, and finetune, and they power a large share of research and on-premise deployments.

**Code models.** Train on a corpus rich in source code and next-token prediction becomes autocomplete for entire functions. GitHub Copilot and its successors are LLMs whose "language" happens to include Python and Rust, proof that the objective never cared that the tokens were English.

## How It's Built

The build, at recognition depth, is a pipeline with four stages.

**Data.** Crawl the web, then fight it. Raw crawls (Common Crawl is the famous public one) are full of boilerplate, spam, and duplicates, so teams filter for quality, deduplicate aggressively, and mix in curated sources: books, code, reference text. Dataset curation is quietly one of the most decisive competitive advantages in the field.

**Tokenizer.** Train the vocabulary from [Topic 10: Tokenization](topic-10-tokenization.md) on that corpus, typically byte-pair encoding with tens to hundreds of thousands of tokens, then freeze it. Every design choice here, how numbers split, how rare languages fragment, is baked into the model forever.

**Pretraining.** Shard the model and data across thousands of accelerators and run the gradient-descent loop of [Topic 07: Gradient Descent](topic-07-gradient-descent.md) and [Topic 09: Backpropagation](topic-09-backpropagation.md) for weeks or months. At this scale, engineering dominates: hardware failures are routine, checkpoints guard progress, and orchestrating the cluster is as hard as the math. A frontier run consumes power in the range of a small town and budgets in the tens to hundreds of millions of dollars.

**The checkpoint.** Training ends and the weights are frozen into a file, the base model. It is a compressed, lossy, statistical snapshot of its training data's patterns: astonishingly capable, factually fallible, and utterly indifferent to being helpful.

A note on honesty in scale: exact figures for frontier models (parameter counts, token counts, costs) are mostly unpublished. The numbers above are the reliably reported orders of magnitude, which is all recognition depth requires.

## Key Takeaways

* An LLM is a decoder-only transformer trained on one self-supervised objective, next-token prediction, over internet-scale text; the task labels itself, which is what makes internet-scale training possible.
* Next-token prediction is secretly every written task at once: driving its loss down forces the model to absorb grammar, facts, code, and fragments of reasoning.
* **Scaling laws** make loss a predictable function of parameters, data, and compute, turning frontier training from a gamble into a plannable (and enormously expensive) engineering project.
* **Emergent abilities** appear at scale without being explicitly trained, and **in-context learning** lets a frozen model pick up new tasks from examples in its prompt, with no weight updates.
* The direct output of pretraining is a **base model**: a raw text-continuation engine, not an assistant, which is the gap [Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md) exists to close.
* The history runs in a loop: each generation's shortcomings (no instructions, hallucination, frozen knowledge, short memory) became the next generation's fixes (RLHF, retrieval, tools, long context), and today's constraints (chips, power, data, capital) are the frontier being fought over now.

## References

* **Kaplan et al.**: *Scaling Laws for Neural Language Models* (2020), the paper that drew the straight lines on log-log axes and set the field's direction.
* **Hoffmann et al.**: *Training Compute-Optimal Large Language Models* (2022), the "Chinchilla" paper that rebalanced the recipe toward more data per parameter.
* **Brown et al.**: *Language Models are Few-Shot Learners* (2020), the GPT-3 paper and the definitive demonstration of in-context learning.
* **3Blue1Brown**: *Large Language Models explained briefly*, the best short visual companion to this topic.
* **Alammar and Grootendorst, *Hands-On Large Language Models***: the early chapters walk this exact terrain with diagrams and running code.
* **Huyen, *AI Engineering***: opens with what it means to build on top of foundation models, the practitioner's view of everything above.
* **Sutton**: *The Bitter Lesson* (2019), the two-page essay whose thesis the entire LLM era has been testing; read it and reread it after finishing this chapter.
* **Karpathy**: *Deep Dive into LLMs like ChatGPT*, a full video walkthrough from raw internet data to a deployed assistant, the best single companion to the history section.

## Think About It

1. Next-token prediction never rewards truth, only likelihood of continuation. Using that fact, explain why a base model can state a false "fact" with perfect fluency, and why the probability machinery of [Topic 06: Probability as Output](topic-06-probability-as-output.md) means the model was never "lying" in any meaningful sense.
2. Chinchilla says a model trained on too little data for its size is undertrained. Suppose you have a fixed compute budget and must choose: a 70-billion-parameter model on 1 trillion tokens, or a 13-billion-parameter model on 5 trillion tokens. Which does the twenty-tokens-per-parameter heuristic favor, and what practical deployment advantage does the smaller model carry regardless?
3. In-context learning happens with frozen weights, so where does the "learning" live? Trace it to the mechanisms of [Topic 13: Attention](topic-13-attention.md): what, concretely, changes between a prompt with zero examples and a prompt with three?

## Next Topic

The base model at the end of the pipeline is a magnificent engine pointed at nothing. Ask it "What is the capital of France?" and it might answer "Paris," or continue with "What is the capital of Germany?", because both are plausible continuations of text it has seen. Somewhere between that raw predictor and the helpful assistant you actually talk to lies a second phase of training: shorter, cheaper, and arguably more consequential for the user experience than the entire pretraining run. How a text simulator becomes an instruction-follower is the story of **[Topic 16: Pretraining vs Finetuning](topic-16-pretraining-vs-finetuning.md)**.
