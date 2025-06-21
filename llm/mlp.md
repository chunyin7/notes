# multi-layer perceptrons

the part of the llm which stores facts?

we receive a sequence of vectors corresponding to each token

for each vector we receive as input, we perform some series of operations and receive a vector of the same dimensions, which we add to the input vector for the output

we perform some matrix multiplcation with weights and add a bias vector, mapping to a higher dimension vector space

intuitively, we might say that each row of the weight matrix asks the vector some semantic question, and thus in the output, the dot product between these 2 might encode some answer

as we want a binary yes/no answer, we can perform a normalization step with a ReLU function which clips all negative values to 0 (similar to AND gate)

then we map that higher order vector back down to its original dimension
