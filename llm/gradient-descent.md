# Cost Functions
We can define the **cost** of a training input as *how far off the final results are from the desired result*. One such method of calculating cost is summing the squares of the differences between the final activation of each neuron and the desired activation. The goal of gradient descent is to *minimise the average cost*, where cost is the measure of how badly the computer is performing and analogously, how badly the computer should 'feel'. 

The cost function takes in all of the parameters of the machine learning algorithm and outputs a singular number, the cost.

## Minimising Cost
The process of finding the minimum of the cost function can be quite complicated, especially for highly complex cost functions. This can be done by taking a random point on the cost function and finding the slope of the function at this point, and shifting the inputs such as to step in the direction where the slope goes down, analogously like a ball rolling down a hill. We can also scale the size of the step by the magnitude of the slope. This will allow us to approach some local minima of the function, however, there is no guarantee that this will be the global minimum. 

With multivariable calculus, there is some means of calculating the direction of steepest descent, with the negative gradient of the cost function, which will return some vector, when the function takes in many inputs.

The value and sign of the components of the negative gradient vector correspond to how each weight should change and by how much. Essentially, we can abstract this vector as encoding the relative importance of each parameter.

## Stochastic Gradient Descent
As performing backpropagation on all of the training data for each gradient descent step is computationally time intensive, we can split the training data into batches of equal size, performing a gradient descent step based only on the data in one batch at a time. 

While this will not result in the most efficient gradient descent process, it reduces the amount of time and computational power required by a significant amount

https://youtu.be/IHZwWFHWa-w
https://youtu.be/Ilg3gGewQ5U
https://youtu.be/tIeHLnjs5U8
