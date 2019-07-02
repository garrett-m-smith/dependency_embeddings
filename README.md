# Developing corpus-based lexical features and retrieval cues

Cunnings & Sturt (2018) reported illusion of plausibility effects. They argue in that this type of effect requires lexically specific retrieval cues, e.g., *shattered* is looking for direct objects that are shatterable and not ones that are fluffy, like what *pet (the bunny, e.g.)* is looking for. They don't provide a principled way of deriving those retrieval cues, though.

This Python package allows us to develop such features and cues in a principled way. The processing pipeline goes as follows:

1. Parse a corpus to get dependency_type(head, dependent) triples. We use [Stanford NLP's pipeline](https://stanfordnlp.github.io/stanfordnlp/) because it is quite fast---it processes the Brown corpus (Kucera & Francis 19XX) in less than 2hrs. on a pretty average laptop---and it provides state-of-the-art dependency parsing (Qi et al., 2018). We provide the whole Brown corpus (saved in managable chunks created using brown_to_text.py) and two parses of it, one based on the actual word tokens and one based on lemmas (created by parse.py).

2. With the dependency triples, create word embeddings (using functions in gen_embeddings.py). There are many ways of doing this. We chose to use a simple, fast, but effective method. We calculate the positive point-wise mutual information (PPMI) between a word and each possible dependency context it could appear in (Church & Hanks, 1990). We then reduce the dimensionality of these embeddings using singular value decomposition, which seems to help with generalization in NLP tasks. We follow Levy et al. (2015)'s recommendation of using the square root of the eigenvalue diagonal matrix. This leaves us with lexical features for each word in the corpus that are based on the syntactic contexts it appears in. These are saved in a zipped file.

3. To calculate the retrieval cues, we simply take a word for which we want the cues, the type of dependent we're interested in (e.g., direct object), and finally take the average of all of the lexical features of words that appear as that dependent of the given word. These are also saved. We now have what we need.

4. The last step is to apply these vectors to the a scientific question. Here, we focus on cases where there is a grammatical retrieval target and an ungrammatical distractor. Cunnings and Sturt (2018) varied the plausibility of the target and distractor (validated by a norming study). We hypothesized that the difference in plausibility, i.e., the cosine similarity between an embedding and the retrieval cue, between the target and distractor should predict the reading time at the retrieval site. Calculating these differences is done in the script differences.py.
