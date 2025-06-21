# transformers

generative pretrained **transformer** -> transformers are responsible for the underlying next token prediction

## tokenization & encoding

we break up the input data into a series of tokens, where we associate the token with a vector embedding that encodes some meaning regarding the token

the sequence of vectors then passes through an *attention block*, which allows the vectors the communicate with each other to update their values -> responsible for updating contextual meaning

then in a feed forward layer, we essentially perform a process of *asking the vector a series of questions* and then updating meaning (values) based on responses

after a series of attention blocks and multi-layer perceptron blocks, the idea is that all meaning is baked into the last vector in the sequence

the model has a pre-defined vocabulary, some set of all possible tokens it can use, then in the *embedding matrix*, each column represents each of these tokens

the values associated to each token begin random but will be adjusted as the model learns via data

the model converges such that directions in the embedding space will encode some semantic meaning

*unembedding matrix* ->

*temperature* -> alters the way we normalize vector outputs into probability distributions, when temperature is higher, we give more weight to the lower values, producing a more uniform distribution

while output vector values can be described as probabilities, the inputs are called *logits*

## attention

in a *single head* of attention, we use a query matrix and apply it to the vector of a token, in order to adjust meaning of each embedding by all of the other embeddings, as meaning is contextual -> we wish to produce a query vector for every token

we produce a key vector as well for each token which in some manner encodes the answer to the query -> we can take the dot product to match key and query pairs and retrieve answers, thus giving us an effective score of how relevant one token is to updating the meaning of another

i.e. the embeddings of some token(s) *attend to* the embeddings of another

we call the matrix of normalized query, key dot products an *attention pattern*

we don't want later tokens to influence the meanings of previous tokens -> would 'give away the answer' for what comes next, to reduce this effect we can effectively 0 them by setting them to -inf before softmax normalisation this is called **masking**

we can use a *value matrix*, which we multiply with the first token's vector to get a *value vector*, which we can add to the 2nd token's vector, such that we may get a more refined vector that captures a greater extent of contextual meaning

[3b1b transformers](https://www.youtube.com/watch?v=eMlx5fFNoYc&list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi&index=7)
