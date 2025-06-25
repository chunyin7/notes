# text embeddings

deep nnet models, including llms, cannot process raw text directly, thus we need a way to represent words as continuous valued vectors

we refer to the concept of converting data into a vector format as *embedding*, essentially we map nonnumeric data into a mathematical format

the most common form of text embedding are *word embedidngs*, however there are also embeddings for sentences, paragraphs or whole documents, sentence or paragraph embeddings are popular for *retrieval augmented generation*

llms commonly produce their own embeddings that are part of the input layer and are updated during training
