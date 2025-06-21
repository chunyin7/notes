# neural network

A Neural Network involves several layers, each consisting of a group of neurons, which take in input in the form of a vector and perform some weighted sum on the data.

Generally, a neural network will take in some large amount of data, and perform some computation via each layer such that the output data can be easily interpreted to infer some sort of meaning or information about the original data.

## neurons

Each neuron is some function which takes in input data, from previous layers of neurons or from the initial input data, outputting some decimal number between 0 and 1. The output number is referred to the activation of the neuron. 

> The activation of the neurons in one layer determine the activations of the next layer

The activation of the neurons in the layers correspond to some pattern in the data that is used to determine the final output, however, aside from the final layer, each layer does not have a meaning that can necessarily be inferred and understood by humans.

Each layer allows us to further abstract and break down working out the final problem

### underlying maths

The connection between every neuron in the previous layer and one neuron in the next layer is assigned a "weight". These weights are used to perform the weighted sum across all of the activations of the previous layer

As this weighted sum can potentially give a wide range of numbers, where we only desire those in the range of 0-1, we can apply another function that squeezes the real number line into the desired range. A common example of such is the sigmoid function or the ReLU function.

If there is a certain non-zero threshold that is desired for a meaningful activation, we can also apply a "bias", before passing through the sigmoid function, which is some number that we simply add to the weighted sum. 

i.e. If we want the neuron to only meaningfully activate when the weighted sum is greater than 10, we can apply a bias of -10.

This function associated with the neuron can be neatly represented via matrix multiplication and vector addition. We can organise all of the incoming data as a column vector, all of the weights associated with the connections as a matrix. The result of the weighted sum simply corresponds to one of the entries of the resultant vector from this matrix multiplication.

---

## learning

When we refer to a neural network "Learning", this essentially means it is adjusting all of its parameters, its weights and biases, in order to suit the problem at hand.

Training a neural network is typically done through gradient descent, like many other machine learning algorithms.

---

## backpropagation

Backpropagation is an algorithm designed to specifically calculate the gradient vector of the cost function. To influence the activation of a neuron, we can change the bias, the weights from the previous layer in accordance to the activations of the previous layer, or the activations of the previous layer themselves proportionally to the weights.

We can add together all of the desired effects from the last layer to get a list of small changes that we want to happen to the 2nd last layer. This idea can then be recursively applied for all of the layers, hence "backpropagation". When we average these desired changes, we get a vector that is loosely speaking the negative gradient of the cost function

Neurons associated with a result should become more strongly linked to that result.

### underlying maths

Reductively, we can consider a neural network that simply consists of 1 neuron per layer. As we want to consider the sensitivity of the cost in a given training example to the weight of the connection, we can take the partial derivative of the cost by the weight

The derivative of the full cost function can be taken as the average of the derivatives across all training examples. Which then gives us one component of the gradient vector. This process can then be similarly repeated for the sensitivity of the cost with the biases.

When expanding the network to include many neurons per layer rather than 1, we get a similar process that simply involves more indices.

However, when we consider the derivative of the cost function by the activation of a previous layer, it gets more complicated as the activation of that neuron can now influence the activations of many more neurons in further layers, hence we must take a sum

https://youtu.be/aircAruvnKk
http://neuralnetworksanddeeplearning.com/
https://colah.github.io/
https://sausheong.github.io/posts/how-to-build-a-simple-artificial-neural-network-with-go/
