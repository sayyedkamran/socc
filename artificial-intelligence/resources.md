# Artificial Intelligence Resources

Aggregated learning resources for the Artificial Intelligence track. Topic-specific references live at the bottom of each topic file; this page collects channels, books, courses, and other material that serve the track as a whole.

## YouTube Channels

Channels aligned with the SOCC approach: visual intuition first, then rigor, then implementation.

### Core Spine

* [3Blue1Brown](https://www.youtube.com/@3blue1brown): the backbone for linear algebra, calculus, neural networks, and transformers; the Essence and Deep Learning series map directly onto Part 1 Chapters 2, 3, 8, and 9.
* [StatQuest with Josh Starmer](https://www.youtube.com/@statquest): probability, statistics, and classical machine learning explained one small piece at a time; the spine for Part 1 Chapters 4 and 7.
* [Andrej Karpathy](https://www.youtube.com/@AndrejKarpathy): the Zero to Hero series builds neural networks, autograd, and GPT from scratch in code; the bridge from Part 1 theory into Part 2 practice.

### Intuition and Depth

* [Welch Labs](https://www.youtube.com/@WelchLabsVideo): long-form visual essays on how models actually work, with production quality matching 3Blue1Brown.
* [Artem Kirsanov](https://www.youtube.com/@ArtemKirsanov): information theory and computational neuroscience, beautifully visualized; directly serves Part 1 Chapter 5: Information Theory.
* [Mutual Information](https://www.youtube.com/@Mutual_Information): rigorous but visual treatments of classical ML and probability, one level above StatQuest.

### Implementation and Papers

* [Umar Jamil](https://www.youtube.com/@umarjamilai): paper-to-code walkthroughs of attention, LLaMA, quantization, and more; bridges theory to PyTorch implementation.
* [Sebastian Raschka](https://www.youtube.com/@SebastianRaschka): companion channel to *Build a Large Language Model (From Scratch)*, a core curriculum text.
* [AI Coffee Break with Letitia](https://www.youtube.com/@AICoffeeBreak): short, honest paper explainers for staying current with the field.

### Full University Courses

* [Agha Ali Raza](https://www.youtube.com/@aghaaliraza): complete LUMS lecture series, including a full Machine Learning course and a Speech Processing course delivered in Urdu/Hindi; real university rigor with assignments, and the only Urdu-language resource on this list. An alternate spine for the classical ML chapters.

## Books

Grouped by area. Within each category, books are ordered by priority: the one to read first (or the most valuable) sits at the top. Annotations and links for newer entries will be added in a later pass.

### Core AI and Machine Learning

1. [Burkov, *The Hundred-Page Machine Learning Book*](https://themlbook.com/): the densest useful summary of classical ML; the fastest way to get the whole map.
2. James, Witten, Hastie, and Tibshirani, *An Introduction to Statistical Learning*
3. [Russell and Norvig, *Artificial Intelligence: A Modern Approach*](https://aima.cs.berkeley.edu/): the classic survey of the whole field; the widest map of AI beyond machine learning.
4. Bishop, *Pattern Recognition and Machine Learning*
5. Murphy, *Probabilistic Machine Learning: An Introduction*
6. Hastie, Tibshirani, and Friedman, *The Elements of Statistical Learning*
7. Shalev-Shwartz and Ben-David, *Understanding Machine Learning: From Theory to Algorithms*

### Deep Learning

1. Prince, *Understanding Deep Learning*
2. [Goodfellow, Bengio, and Courville, *Deep Learning*](https://www.deeplearningbook.org/): the standard theoretical reference for deep learning; free online. The formal counterpart to Part 1 Chapters 2 through 8.

### NLP and Large Language Models

1. [Raschka, *Build a Large Language Model (From Scratch)*](https://www.manning.com/books/build-a-large-language-model-from-scratch): implements a GPT-style model end to end in PyTorch; the companion text for Part 1 Chapter 9 and Part 2.
2. [Jurafsky and Martin, *Speech and Language Processing* (3rd edition draft)](https://web.stanford.edu/~jurafsky/slp3/): the definitive NLP text; free online. Covers tokenization, embeddings, sequence models, and transformers in depth.
3. [Alammar and Grootendorst, *Hands-On Large Language Models*](https://www.oreilly.com/library/view/hands-on-large-language/9781098150952/): visual, practical guide to using and understanding modern LLMs.
4. [Burkov, *The Hundred-Page Language Models Book*](https://thelmbook.com/): the same compression applied to language models.

### Reinforcement Learning

1. Sutton and Barto, *Reinforcement Learning: An Introduction*

### Engineering and Practice

1. [Geron, *Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow*](https://www.oreilly.com/library/view/hands-on-machine-learning/9781098125967/): the standard practical ML text; theory from Part 1 turned into working code.
2. [Huyen, *AI Engineering*](https://www.oreilly.com/library/view/ai-engineering/9781098166298/): building applications on top of foundation models; evaluation, RAG, agents, and deployment.
3. Huyen, *Designing Machine Learning Systems*

### Mathematical Foundations

1. Deisenroth, Faisal, and Ong, *Mathematics for Machine Learning*
2. Strang, *Linear Algebra and Learning from Data*
3. Boyd and Vandenberghe, *Introduction to Applied Linear Algebra*
4. Axler, *Linear Algebra Done Right*
5. Trefethen and Bau, *Numerical Linear Algebra*
6. Hubbard and Hubbard, *Vector Calculus, Linear Algebra and Differential Forms*
7. Spivak, *Calculus*

### Probability and Statistics

1. Bertsekas and Tsitsiklis, *Introduction to Probability*
2. Morin, *Probability: For the Enthusiastic Beginner*
3. Wasserman, *All of Statistics*
4. Casella and Berger, *Statistical Inference*
5. Efron and Hastie, *Computer Age Statistical Inference*
6. Jaynes, *Probability Theory: The Logic of Science*
7. Hernan and Robins, *Causal Inference: What If*

### Information Theory

1. Stone, *Information Theory: A Tutorial Introduction*
2. MacKay, *Information Theory, Inference, and Learning Algorithms*
3. Cover and Thomas, *Elements of Information Theory*
4. Gray, *Entropy and Information Theory*

### Optimization

1. Boyd and Vandenberghe, *Convex Optimization*
2. Nocedal and Wright, *Numerical Optimization*

### Computer Science Foundations

1. Cormen, Leiserson, Rivest, and Stein, *Introduction to Algorithms*
2. Sipser, *Introduction to the Theory of Computation*
3. Diestel, *Graph Theory*
4. Downey, *Think DSP*

## Courses and Interactive Resources

* [Dive into Deep Learning (d2l.ai)](https://d2l.ai/): free interactive book with runnable code in PyTorch; theory and implementation side by side.
* [Karpathy, Neural Networks: Zero to Hero](https://karpathy.ai/zero-to-hero.html): the video course page with all notebooks; backpropagation to GPT, built by hand.
* [Kaggle Learn](https://www.kaggle.com/learn): short, free, hands-on courses with in-browser notebooks; no setup needed for first contact with code.
* [Hugging Face Learn](https://huggingface.co/learn): free courses on transformers, NLP, and agents, built around the ecosystem used everywhere in practice.
* [DeepLearning.AI](https://www.deeplearning.ai/): Andrew Ng's specializations and short courses; the Deep Learning Specialization fills the CNN and RNN gaps that visual channels skip.

## Blogs and Essays

* [Jay Alammar](https://jalammar.github.io/): home of *The Illustrated Transformer* and *The Illustrated GPT-2*, the most-borrowed diagrams in AI education.
* [Christopher Olah (colah.github.io)](https://colah.github.io/): classic illustrated essays, including *Understanding LSTM Networks*.
* [Andrej Karpathy's blog](https://karpathy.github.io/): essays including *The Unreasonable Effectiveness of Recurrent Neural Networks*.
