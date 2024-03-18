# Assignment_4

**You**

How should I approach generating a markov chain of words when I have a start word and the end word of the chain along with a file to refer what the sentance formation  should be like?

**ChatGPT**

Generating a Markov chain of words involves creating a statistical model based on the probabilities of transitioning from one word to another, given a large corpus of text. The process generally involves the following steps:

1. **Preprocessing the Corpus**: Clean the corpus by removing punctuation, special characters, and any irrelevant information. Tokenize the text into words or phrases.
2. **Building the Transition Matrix**: Create a matrix or dictionary to store transition probabilities between words. For each word in the corpus, record the words that follow it and their respective frequencies.
3. **Calculating Transition Probabilities**: Normalize the frequencies to obtain probabilities. This involves dividing the frequency of each word pair by the total occurrences of the starting word.
4. **Generating the Markov Chain**: Starting with the given start word, randomly select the next word based on the probabilities defined in the transition matrix. Repeat this process until the end word is reached or a maximum length is reached.

Here's a Python example to illustrate how you might implement this:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_path):
    with open(file_path, 'r') as file:
        corpus = file.read().lower()  # Read and convert to lowercase
        corpus = re.sub(r'\n', ' ', corpus)  # Replace newlines with spaces
        corpus = re.sub(r'[^\w\s]', '', corpus)  # Remove punctuation
        return corpus.split()  # Tokenize into words

def build_transition_matrix(corpus):
    transition_matrix = {}
    for i in range(len(corpus) - 1):
        current_word = corpus[i]
        next_word = corpus[i + 1]
        if current_word not in transition_matrix:
            transition_matrix[current_word] = {}
        if next_word not in transition_matrix[current_word]:
            transition_matrix[current_word][next_word] = 0
        transition_matrix[current_word][next_word] += 1
    return transition_matrix

def calculate_transition_probabilities(transition_matrix):
    for current_word, next_words in transition_matrix.items():
        total_occurrences = sum(next_words.values())
        for next_word in next_words:
            transition_matrix[current_word][next_word] /= total_occurrences

def generate_markov_chain(start_word, end_word, transition_matrix, max_length=50):
    chain = [start_word]
    current_word = start_word
    while current_word != end_word and len(chain) < max_length:
        if current_word in transition_matrix:
            next_word = random.choices(
                list(transition_matrix[current_word].keys()),
                weights=list(transition_matrix[current_word].values())
            )[0]
            chain.append(next_word)
            current_word = next_word
        else:
            break
    return ' '.join(chain)

# Example usage
corpus = preprocess_corpus('corpus.txt')
transition_matrix = build_transition_matrix(corpus)
calculate_transition_probabilities(transition_matrix)
start_word = "start"
end_word = "end"
generated_sentence = generate_markov_chain(start_word, end_word, transition_matrix)
print(generated_sentence)

```

Replace **`'corpus.txt'`** with the path to your corpus file. This script assumes that each word in the corpus is separated by whitespace. Adjust the preprocessing steps according to the format of your corpus. Additionally, make sure that your corpus contains examples of sentence formations that you want to mimic in your Markov chain generation.

**You**

Give an example to explain this code.

**ChatGPT**

Sure, let's walk through an example to explain the code.

Suppose we have a corpus file named **`corpus.txt`** containing the following text:

```kotlin
kotlinCopy code
I love programming. Programming is fun.
Programming makes me happy.

```

We want to generate a Markov chain of words starting with "programming" and ending with "happy," mimicking the structure of sentences in our corpus.

Here's how we'll use the code:

1. **Preprocess the Corpus**: The **`preprocess_corpus`** function will read the file and tokenize the text into individual words, converting everything to lowercase and removing punctuation.
2. **Build the Transition Matrix**: The **`build_transition_matrix`** function will create a transition matrix, capturing the frequency of word transitions in the corpus.
3. **Calculate Transition Probabilities**: The **`calculate_transition_probabilities`** function will normalize the frequencies in the transition matrix to obtain transition probabilities.
4. **Generate the Markov Chain**: Using the **`generate_markov_chain`** function, we'll start with the word "programming" and generate a sequence of words until we reach "happy" or a maximum length.

Now, let's see the code in action:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_path):
    with open(file_path, 'r') as file:
        corpus = file.read().lower()  # Read and convert to lowercase
        corpus = re.sub(r'\n', ' ', corpus)  # Replace newlines with spaces
        corpus = re.sub(r'[^\w\s]', '', corpus)  # Remove punctuation
        return corpus.split()  # Tokenize into words

def build_transition_matrix(corpus):
    transition_matrix = {}
    for i in range(len(corpus) - 1):
        current_word = corpus[i]
        next_word = corpus[i + 1]
        if current_word not in transition_matrix:
            transition_matrix[current_word] = {}
        if next_word not in transition_matrix[current_word]:
            transition_matrix[current_word][next_word] = 0
        transition_matrix[current_word][next_word] += 1
    return transition_matrix

def calculate_transition_probabilities(transition_matrix):
    for current_word, next_words in transition_matrix.items():
        total_occurrences = sum(next_words.values())
        for next_word in next_words:
            transition_matrix[current_word][next_word] /= total_occurrences

def generate_markov_chain(start_word, end_word, transition_matrix, max_length=50):
    chain = [start_word]
    current_word = start_word
    while current_word != end_word and len(chain) < max_length:
        if current_word in transition_matrix:
            next_word = random.choices(
                list(transition_matrix[current_word].keys()),
                weights=list(transition_matrix[current_word].values())
            )[0]
            chain.append(next_word)
            current_word = next_word
        else:
            break
    return ' '.join(chain)

# Example usage
corpus = preprocess_corpus('corpus.txt')
transition_matrix = build_transition_matrix(corpus)
calculate_transition_probabilities(transition_matrix)
start_word = "programming"
end_word = "happy"
generated_sentence = generate_markov_chain(start_word, end_word, transition_matrix)
print(generated_sentence)

```

When you run this code, you might get outputs like:

```

programming makes me happy

```

Or:

```

programming is fun

```

These outputs are generated by following the transitions captured in the transition matrix, starting with "programming" and ending with "happy." The specific output will vary due to the randomness in selecting the next word based on transition probabilities.

**You**

I want a simple code where i store the word and next word as key and value in dictionary and use random to pick the next word. Create a function which takes start word, output length and file name as arguments

**ChatGPT**

Sure, here's a simpler version of the code that stores word pairs as keys and the following word as the corresponding value in a dictionary. Then, it uses random selection to pick the next word:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_name):
    with open(file_name, 'r') as file:
        text = file.read().lower()  # Read and convert to lowercase
        text = re.sub(r'\n', ' ', text)  # Replace newlines with spaces
        text = re.sub(r'[^\w\s]', '', text)  # Remove punctuation
        words = text.split()  # Tokenize into words
    return words

def generate_markov_chain(start_word, output_length, file_name):
    word_pairs = {}
    words = preprocess_corpus(file_name)

    # Construct word pairs and their following word
    for i in range(len(words) - 1):
        current_word = words[i]
        next_word = words[i + 1]
        if current_word not in word_pairs:
            word_pairs[current_word] = []
        word_pairs[current_word].append(next_word)

    # Generate the chain
    current_word = start_word
    chain = [current_word]
    for _ in range(output_length - 1):
        if current_word in word_pairs:
            next_word = random.choice(word_pairs[current_word])
            chain.append(next_word)
            current_word = next_word
        else:
            break

    return ' '.join(chain)

# Example usage
start_word = "programming"
output_length = 10
file_name = "corpus.txt"  # Replace with your file name
generated_sentence = generate_markov_chain(start_word, output_length, file_name)
print(generated_sentence)

```

This code defines a function **`generate_markov_chain`** that takes the start word, desired output length, and the file name as arguments. It preprocesses the corpus, constructs a dictionary of word pairs, and generates the Markov chain using random selection based on the probabilities encoded in the dictionary.

Make sure to replace **`"corpus.txt"`** with the path to your corpus file.

**You**

I got the output as programming programming programming makes me happy, I dont want consecutive words to be similar. How can I make that happen?

**ChatGPT**

To ensure that consecutive words are not repeated, you can modify the code to exclude the current word from the list of possible next words when selecting randomly. Here's the updated code:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_name):
    with open(file_name, 'r') as file:
        text = file.read().lower()  # Read and convert to lowercase
        text = re.sub(r'\n', ' ', text)  # Replace newlines with spaces
        text = re.sub(r'[^\w\s]', '', text)  # Remove punctuation
        words = text.split()  # Tokenize into words
    return words

def generate_markov_chain(start_word, output_length, file_name):
    word_pairs = {}
    words = preprocess_corpus(file_name)

    # Construct word pairs and their following word
    for i in range(len(words) - 1):
        current_word = words[i]
        next_word = words[i + 1]
        if current_word not in word_pairs:
            word_pairs[current_word] = []
        word_pairs[current_word].append(next_word)

    # Generate the chain
    current_word = start_word
    chain = [current_word]
    for _ in range(output_length - 1):
        if current_word in word_pairs:
            next_word_options = [word for word in word_pairs[current_word] if word != current_word]
            if next_word_options:
                next_word = random.choice(next_word_options)
                chain.append(next_word)
                current_word = next_word
            else:
                break
        else:
            break

    return ' '.join(chain)

# Example usage
start_word = "programming"
output_length = 10
file_name = "corpus.txt"  # Replace with your file name
generated_sentence = generate_markov_chain(start_word, output_length, file_name)
print(generated_sentence)

```

In this updated version, before choosing the next word, we filter out the current word from the list of possible next words. This ensures that consecutive words will not be the same.

**You**

I got the output as programming is fun programming is fun programming makes me happy. How do I avoid repeatation of a clause?

**ChatGPT**

To prevent repeating entire clauses or phrases, you can modify the generation process to consider word pairs or n-grams instead of individual words. This way, the model will have a better chance of avoiding repetition of entire phrases. Here's how you can do it:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_name):
    with open(file_name, 'r') as file:
        text = file.read().lower()  # Read and convert to lowercase
        text = re.sub(r'\n', ' ', text)  # Replace newlines with spaces
        text = re.sub(r'[^\w\s]', '', text)  # Remove punctuation
        words = text.split()  # Tokenize into words
    return words

def generate_markov_chain(start_word, output_length, file_name):
    word_pairs = {}
    words = preprocess_corpus(file_name)

    # Construct word pairs and their following word
    for i in range(len(words) - 2):
        current_pair = (words[i], words[i+1])
        next_word = words[i + 2]
        if current_pair not in word_pairs:
            word_pairs[current_pair] = []
        word_pairs[current_pair].append(next_word)

    # Generate the chain
    current_pair = (start_word, random.choice([word for word in words if word != start_word]))
    chain = list(current_pair)
    for _ in range(output_length - 2):
        if current_pair in word_pairs:
            next_word_options = [word for word in word_pairs[current_pair] if word != current_pair[1]]
            if next_word_options:
                next_word = random.choice(next_word_options)
                chain.append(next_word)
                current_pair = (current_pair[1], next_word)
            else:
                break
        else:
            break

    return ' '.join(chain)

# Example usage
start_word = "programming"
output_length = 10
file_name = "corpus.txt"  # Replace with your file name
generated_sentence = generate_markov_chain(start_word, output_length, file_name)
print(generated_sentence)

```

In this version, the **`generate_markov_chain`** function now considers word pairs instead of individual words. This helps prevent repeating entire phrases. Additionally, when selecting the next word, it avoids choosing the same word as the second word in the current pair. This way, repetition of clauses is reduced.

**You**

I got the output as programming fun, how do I make sure that the output is of the length specified as argument

**ChatGPT**

To ensure that the output is of the length specified as an argument, you can adjust the generation process to continue until the desired length is reached or until there are no more valid next words available. Here's the modified code to achieve this:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_name):
    with open(file_name, 'r') as file:
        text = file.read().lower()  # Read and convert to lowercase
        text = re.sub(r'\n', ' ', text)  # Replace newlines with spaces
        text = re.sub(r'[^\w\s]', '', text)  # Remove punctuation
        words = text.split()  # Tokenize into words
    return words

def generate_markov_chain(start_word, output_length, file_name):
    word_pairs = {}
    words = preprocess_corpus(file_name)

    # Construct word pairs and their following word
    for i in range(len(words) - 2):
        current_pair = (words[i], words[i+1])
        next_word = words[i + 2]
        if current_pair not in word_pairs:
            word_pairs[current_pair] = []
        word_pairs[current_pair].append(next_word)

    # Generate the chain
    current_pair = (start_word, random.choice([word for word in words if word != start_word]))
    chain = list(current_pair)
    while len(chain) < output_length:
        if current_pair in word_pairs:
            next_word_options = [word for word in word_pairs[current_pair] if word != current_pair[1]]
            if next_word_options:
                next_word = random.choice(next_word_options)
                chain.append(next_word)
                current_pair = (current_pair[1], next_word)
            else:
                break
        else:
            break

    return ' '.join(chain)

# Example usage
start_word = "programming"
output_length = 10
file_name = "corpus.txt"  # Replace with your file name
generated_sentence = generate_markov_chain(start_word, output_length, file_name)
print(generated_sentence)

```

In this version, the **`generate_markov_chain`** function continues generating the chain until its length reaches the specified **`output_length`**. If it reaches a point where there are no valid next words available, it stops the generation process and returns the current chain. This ensures that the output is of the specified length.

**You**

Got the output as programming makes me happy, which is still not of 10 words

**ChatGPT**

Apologies for the oversight. We need to adjust the generation process to ensure that the output is exactly of the specified length. We can achieve this by limiting the generation process to the desired number of words and handling the case where the corpus does not provide enough valid next words to reach the specified length. Here's the revised code:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_name):
    with open(file_name, 'r') as file:
        text = file.read().lower()  # Read and convert to lowercase
        text = re.sub(r'\n', ' ', text)  # Replace newlines with spaces
        text = re.sub(r'[^\w\s]', '', text)  # Remove punctuation
        words = text.split()  # Tokenize into words
    return words

def generate_markov_chain(start_word, output_length, file_name):
    word_pairs = {}
    words = preprocess_corpus(file_name)

    # Construct word pairs and their following word
    for i in range(len(words) - 2):
        current_pair = (words[i], words[i+1])
        next_word = words[i + 2]
        if current_pair not in word_pairs:
            word_pairs[current_pair] = []
        word_pairs[current_pair].append(next_word)

    # Generate the chain
    current_pair = (start_word, random.choice([word for word in words if word != start_word]))
    chain = list(current_pair)
    while len(chain) < output_length:
        if current_pair in word_pairs:
            next_word_options = [word for word in word_pairs[current_pair] if word != current_pair[1]]
            if next_word_options:
                next_word = random.choice(next_word_options)
                chain.append(next_word)
                current_pair = (current_pair[1], next_word)
            else:
                break
        else:
            break

    # If chain is shorter than the desired length, fill it with random words from the corpus
    while len(chain) < output_length:
        next_word = random.choice(words)
        chain.append(next_word)
        current_pair = (current_pair[1], next_word)

    return ' '.join(chain)

# Example usage
start_word = "programming"
output_length = 10
file_name = "corpus.txt"  # Replace with your file name
generated_sentence = generate_markov_chain(start_word, output_length, file_name)
print(generated_sentence)

```

In this version, after the initial generation loop, if the chain is shorter than the desired length, we fill it with random words from the corpus until it reaches the specified length. This ensures that the output is exactly of the specified length.

**You**

Add chain_length as argument so that the dictionary should have key of words of chain_length.

**ChatGPT**

To include **`chain_length`** as an argument and ensure that the dictionary has keys consisting of words of **`chain_length`**, we need to adjust the code to construct word pairs of length **`chain_length`**. Here's the modified code:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_name):
    with open(file_name, 'r') as file:
        text = file.read().lower()  # Read and convert to lowercase
        text = re.sub(r'\n', ' ', text)  # Replace newlines with spaces
        text = re.sub(r'[^\w\s]', '', text)  # Remove punctuation
        words = text.split()  # Tokenize into words
    return words

def generate_markov_chain(start_word, output_length, chain_length, file_name):
    word_pairs = {}
    words = preprocess_corpus(file_name)

    # Construct word pairs of given chain length and their following word
    for i in range(len(words) - chain_length):
        current_words = tuple(words[i:i+chain_length])
        next_word = words[i + chain_length]
        if current_words not in word_pairs:
            word_pairs[current_words] = []
        word_pairs[current_words].append(next_word)

    # Generate the chain
    current_words = (start_word,) + tuple(random.choice([word for word in words if word != start_word]) for _ in range(chain_length-1))
    chain = list(current_words)
    while len(chain) < output_length:
        if current_words in word_pairs:
            next_word_options = [word for word in word_pairs[current_words] if word != current_words[-1]]
            if next_word_options:
                next_word = random.choice(next_word_options)
                chain.append(next_word)
                current_words = current_words[1:] + (next_word,)
            else:
                break
        else:
            break

    # If chain is shorter than the desired length, fill it with random words from the corpus
    while len(chain) < output_length:
        next_word = random.choice(words)
        chain.append(next_word)
        current_words = current_words[1:] + (next_word,)

    return ' '.join(chain)

# Example usage
start_word = "programming"
output_length = 10
chain_length = 2
file_name = "corpus.txt"  # Replace with your file name
generated_sentence = generate_markov_chain(start_word, output_length, chain_length, file_name)
print(generated_sentence)

```

In this version, the function **`generate_markov_chain`** now accepts the additional argument **`chain_length`**. It constructs word pairs of length **`chain_length`** and their following words. The rest of the logic remains similar, but we maintain and update tuples of words instead of single words. This ensures that the keys in the dictionary correspond to word chains of length **`chain_length`**.

**You**

make the strart word of length two too

**ChatGPT**

To make the start word of length two as well, we need to adjust the initial generation of the current words to include the start word of length two. Here's the modified code:

```python
pythonCopy code
import random
import re

def preprocess_corpus(file_name):
    with open(file_name, 'r') as file:
        text = file.read().lower()  # Read and convert to lowercase
        text = re.sub(r'\n', ' ', text)  # Replace newlines with spaces
        text = re.sub(r'[^\w\s]', '', text)  # Remove punctuation
        words = text.split()  # Tokenize into words
    return words

def generate_markov_chain(start_word, output_length, chain_length, file_name):
    word_pairs = {}
    words = preprocess_corpus(file_name)

    # Construct word pairs of given chain length and their following word
    for i in range(len(words) - chain_length):
        current_words = tuple(words[i:i+chain_length])
        next_word = words[i + chain_length]
        if current_words not in word_pairs:
            word_pairs[current_words] = []
        word_pairs[current_words].append(next_word)

    # Generate the chain
    current_words = tuple(random.choice([word for word in words if len(word) == chain_length]) for _ in range(chain_length))
    chain = list(current_words)
    while len(chain) < output_length:
        if current_words in word_pairs:
            next_word_options = [word for word in word_pairs[current_words] if word != current_words[-1]]
            if next_word_options:
                next_word = random.choice(next_word_options)
                chain.append(next_word)
                current_words = current_words[1:] + (next_word,)
            else:
                break
        else:
            break

    # If chain is shorter than the desired length, fill it with random words from the corpus
    while len(chain) < output_length:
        next_word = random.choice(words)
        chain.append(next_word)
        current_words = current_words[1:] + (next_word,)

    return ' '.join(chain)

# Example usage
start_word = "programming"
output_length = 10
chain_length = 2
file_name = "corpus.txt"  # Replace with your file name
generated_sentence = generate_markov_chain(start_word, output_length, chain_length, file_name)
print(generated_sentence)

```

In this version, the **`generate_markov_chain`** function ensures that the initial current words have a length of two by selecting random words from the corpus that match the specified length. The rest of the code remains the same.

**You**

I meant take the start_word as two words in the example usage

**ChatGPT**

Apologies for the confusion. To take the start word as two words in the example usage, we can adjust the function call accordingly. Here's the modified example usage:

```python
pythonCopy code
# Example usage
start_words = ("programming", "is")  # Start word of length two
output_length = 10
chain_length = 2
file_name = "corpus.txt"  # Replace with your file name
generated_sentence = generate_markov_chain(start_words, output_length, chain_length, file_name)
print(generated_sentence)

```

In this example, **`start_words`** is a tuple containing two words. This tuple represents the start word of length two. We pass this tuple as the start word argument when calling the **`generate_markov_chain`** function. The rest of the code remains the same.

**You**

show the dictionary

**ChatGPT**

To display the constructed dictionary, we can add a print statement after the dictionary construction. Here's how you can modify the **`generate_markov_chain`** function to print the dictionary:

```python
pythonCopy code
def generate_markov_chain(start_words, output_length, chain_length, file_name):
    word_pairs = {}
    words = preprocess_corpus(file_name)

    # Construct word pairs of given chain length and their following word
    for i in range(len(words) - chain_length):
        current_words = tuple(words[i:i+chain_length])
        next_word = words[i + chain_length]
        if current_words not in word_pairs:
            word_pairs[current_words] = []
        word_pairs[current_words].append(next_word)

    # Print the constructed dictionary
    print("Word pairs dictionary:")
    for key, value in word_pairs.items():
        print(key, "->", value)
    print()

    # Generate the chain
    current_words = start_words
    chain = list(current_words)
    while len(chain) < output_length:
        if current_words in word_pairs:
            next_word_options = [word for word in word_pairs[current_words] if word != current_words[-1]]
            if next_word_options:
                next_word = random.choice(next_word_options)
                chain.append(next_word)
                current_words = current_words[1:] + (next_word,)
            else:
                break
        else:
            break

    # If chain is shorter than the desired length, fill it with random words from the corpus
    while len(chain) < output_length:
        next_word = random.choice(words)
        chain.append(next_word)
        current_words = current_words[1:] + (next_word,)

    return ' '.join(chain)

```

With this modification, when you call the **`generate_markov_chain`** function, it will print the constructed word pairs dictionary before generating the chain. This will allow you to see the dictionary content. After examining the dictionary, you can observe the associations between word pairs and their following words.