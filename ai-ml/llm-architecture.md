# Architecture
At its core, a large language model is built on a Transformer architecture, which processes and generates text by predicting the next token in a sequence. This step-by-step process enables the model to understand context and generate coherent, human-like responses.

**1. Input & Embedding Layer**
This is where raw text is converted into a numerical format that the model can understand.

Tokenization: The first step is to break down the input text into a sequence of smaller units called tokens. These can be words, subwords, or individual characters. This is a crucial step because the model works with numbers, not words.

Token Embedding: Each token is then mapped to a numerical vector, known as an embedding. This vector is a multi-dimensional representation of the token's meaning, capturing semantic relationships. For instance, words like "king" and "queen" will have similar embedding vectors.

Positional Encoding: Because the Transformer processes tokens in parallel, it needs a way to understand the order of words. Positional encodings are added to the token embeddings to provide this crucial sequential information. They are unique vectors that encode the position of each token in the input sequence.

**2. Transformer Decoder Blocks**
The heart of the LLM is a stack of these identical blocks, which are the main engine for processing and understanding the input. Each block contains several key components.

Masked Multi-Head Self-Attention: This is the most important part of the model. It's an attention mechanism that allows the model to weigh the importance of all other tokens in the sequence when processing a single token.  It's called "multi-head" because it performs this calculation several times in parallel, each time focusing on a different aspect of the relationships between words. The "masked" part is vital for text generation; it ensures that a token can only pay attention to the tokens that came before it, preventing the model from "cheating" by looking at the answer.

Add & Layer Normalization: This step is critical for training stability. Residual connections are used to create "shortcuts" that allow information to bypass certain layers, which helps prevent the model's performance from degrading as it gets deeper. Layer normalization is applied to ensure that the values within the neural network remain in a stable range, which is essential for training models with billions of parameters.

Feed-Forward Neural Network (FFN): Following the attention mechanism, a standard neural network with multiple layers is applied to each token's representation independently. This network adds non-linearity and allows the model to learn more complex patterns and relationships.


---
** Note: **
Multi-Head Attention
The Problem: One attention mechanism might miss some patterns.
The Solution: Run many attention mechanisms in parallel (called "heads").
Simple Example:

Head 1 might focus on "who did what" (subject-verb)
Head 2 might focus on "what describes what" (adjectives)
Head 3 might focus on "what happened when" (time relations)

How many heads: Typical models have 32-128 attention heads per layer.

---

**3. Output Layer**
After the final Transformer block, the output is prepared for a prediction.

Linear Projection & Softmax: The final vector from the last token is fed into a linear layer, which converts it into a set of logits—a numerical score for every word in the model's vocabulary. A softmax function then converts these logits into a probability distribution, where the probabilities of all words add up to 1. The word with the highest probability is the model's prediction for the next token.

**4. Training Objective**
LLMs are typically trained with a Causal Language Modeling objective. The model is given a sequence of text and its task is to predict the next token. It does this repeatedly on a massive dataset, learning the statistical patterns of language. This pre-training phase allows the model to develop its broad understanding, which can then be fine-tuned for specific tasks.

## **Scale and Why Size Matters**
#### Why LLMs Need to Be Huge

  1. More Layers = Better Understanding:
     - Early layers learn simple patterns (grammar, basic words)
     - Middle layers learn complex relationships (who did what)
     - Deep layers learn reasoning and logic

  2. More Parameters = More Memory:
     - Each parameter stores one piece of learned information
     - More parameters = can remember more patterns
     - GPT-3: 175 billion parameters = 175 billion learned patterns

  3. More Training Data = Better Patterns:
     - See more examples = learn better rules
     - Modern LLMs train on trillions of words
     - Like a person reading millions of books

**Real Scale Examples**
1. Small model (1 billion parameters):
- Can complete sentences
- Basic grammar understanding
- Simple question answering

2. Medium model (7-13 billion parameters):
- Good conversation ability
- Can write stories and code
- Basic reasoning

3. Large model (70+ billion parameters):
- Advanced reasoning
- Complex problem solving
- Expert-level knowledge in many fields


## Training - How LLMs Learn
#### The Learning Process
**Step 1:** Show Examples

→ Give the model text: "The cat sat on the"
→ Ask it to predict: "mat"
→ It guesses: "chair" (wrong!)

**Step 2:** Learn from Mistakes

→ Compare guess vs. correct answer
→ Adjust all the billions of parameters slightly
→ Try to make "mat" more likely next time

**Step 3:** Repeat Billions of Times
→ Do this for trillions of text examples
→ Each mistake teaches the model something
→ Gradually gets better at predictions

Why This Creates Intelligence
Pattern Recognition: By predicting billions of words, the model learns:

Grammar rules (without being taught grammar)
Facts about the world (by seeing them repeatedly)
Logical reasoning (by seeing examples of good reasoning)
How to write code, poetry, essays, etc.

Emergent Abilities: At large scales, unexpected abilities emerge:

Can learn new tasks from just a few examples
Can explain its reasoning step-by-step
Can engage in complex conversations


## Different Types of LLM Architectures
#### Decoder-Only (Like GPT, Claude)

→ Only predicts next word
→ Can't look at future words
→ Best for text generation and conversation

#### Encoder-Only (Like BERT)

→ Can look at entire sentence at once
→ Good for understanding and classification
→ Not great for generation

#### Encoder-Decoder (Like T5)

→ Has two parts: one for understanding, one for generating
→ Good for translation and summarization

**Why decoder-only won:** Turns out predicting the next word is enough to learn everything else!

**Key Insights - Why This Works So Well**

- Self-Supervised Learning: Don't need labeled data - just predict next word
- Scale: Bigger models with more data consistently perform better
- Attention: Allows models to focus on relevant information
- Transfer Learning: Skills learned during training transfer to new tasks
- Emergence: Complex abilities emerge from simple next-word prediction
