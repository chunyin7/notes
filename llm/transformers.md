# transformers

generative pretrained **transformer** -> transformers are responsible for the underlying next token prediction

## tokenization & encoding

we break up the input data into a series of tokens, where we associate the token with a vector embedding that encodes some meaning regarding the token

the sequence of vectors then passes through an *attention block*, which allows the vectors the communicate with each other to update their values -> responsible for updating contextual meaning

then in a feed forward layer, we essentially perform a process of *asking the vector a series of questions* and then updating meaning (values) based on responses

after a series of attention blocks and multi-layer perceptron blocks, the idea is that all meaning is baked into the last vector in the sequence
