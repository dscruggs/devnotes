# 5. Generalizing Gradient Descent
  - This chapter is about taking the concepts used to update one weight last chapter to updating a whole neural network
  
## Gradient Descent with Multiple Inputs

### Differences between single and multiple inputs/weights
  - identical through most of it. same process to calculate the output and then compare the prediction to the ground truth goal
  - how do you generate three weight_deltas from that error?
    - ```delta``` is a measure of how much you want a node (output) value to be different to predict perfectly given the current training example 
    - ```weight_delta``` derivative based estimate of the direction and amount you should move a weight to reduce node_delta (output error), accounting for scaling and negative reversal
    - each ```weight_delta``` makes a sort of observation on how a weight's role in delta was affected by its input, so each ```weight_delta``` is a modified version of ```delta```
    - so, to create all the different ```weight_delta``` from the single ```delta```, you multiply delta by the respective inputs of each neuron
  - Multi weight code for multiple inputs single output
  ```python
  def grad_desc_multi_weight(inputs,initial_weights,goal_pred,alpha=0.1,max_iter=50,thresh=0.00001,verbose=True):
    curr_weights = initial_weights
    
    for itern_num in range(max_iter):
        pred = np.sum(inputs*curr_weights)
        error = (pred - goal_pred) ** 2
        delta = pred - goal_pred
        weight_deltas = delta*inputs
        curr_weights = curr_weights - (alpha*weight_deltas)
        if verbose:
            print(f'Error: {error}')
            print(f'Delta: {delta}')
            print(f'weight_deltas: {weight_deltas}')
            print(f'weights: {initial_weights}')
            print(f'curr_weights: {curr_weights}')
      
    return pred
    
    pred = grad_desc_multi_weight(input_arr[0,:],weights,win_loss_binary[0],alpha=0.01)
  ```
  
### Freezing one weight
  - When freezing one weight and only allowing the others to adjust, they will all still find their error minimizing values
  - if you were training weights `a`,`b`, and `c`but froze `a`, `b` and `c` would still adjust, but you would find that if you then unfroze `a` after `error = 0`, `a` would not adjust because `weight_delta` would be 0
    - this is a potential problem with neural networks. `a` might have had a lot of predictive power, but if the the network accidentally figures out how to fit the data without it, it will never learn to incorporate `a` into its prediction.
    - `a` still finds the "bottom of the bowl", because it has still found an error minimizing values. instead of the value of `a` changing though, the error curve with respect to `a` just shifted
      - this is because the 2D graphs of the error curves for each weight are just slices of a 4D shape called an `error plane`. This plane's curvature is determined by the training data. Three of the dimensions are the weight values, and the fourth is the error of the prediction.
      - `error` is determined by the training data because the predictions are dependent on the inputs
      - the goal of a neural network is to find the global minimum on this `error plane`
## Gradient Descent with Multiple Outputs

### Single input multiple outputs
  - you calculate each `delta` the same way (pred - actual) and then multiply them all by the same single `input`
    - this becomes each weight's `weight_delta`
  - the same `stochastic gradient descent` method is used here and for learning across many network architectures

## Gradient Descent with Multiple Inputs and Outputs (arbitrarily large networks)

### Multiple Inputs Multiple Outputs
  - basically just need to calculate delta for each node's prediction for each output, then adjust weights on each node
    - make predictions, compare to desired outputs, calculate delta from that
    - take each delta and multiply it by its respective input (do this for each neuron in a fully connected NN)
    - Example:
      - a fully connected 3 input 3 output neural network with no hidden layer will have a 3x1 input and output vector and a 3x3 weight vector
      - will have 3x1 delta vector and take outer product with input vector to get the 3x3 weight_delta matrix to adjust weights by each iteration
  
  Code I wrote for generic neural network training using gradient descent:
  ```python
  def grad_desc_generalized(inputs,initial_weights,goal_preds,alpha=0.1,max_iter=5000,thresh=0.0000001,verbose=True):
    '''function to perform gradient descent with an arbitrary number of inputs,outputs, and weights for a fully connected
    neural network
    inputs (np.array): inputs to model
    initial_weights (np.array): initial weights, could be randomized
    goal_preds (np.array): ground truth output data that will be used for training model
    alpha (float between 0 and 1): controls learning rate(amount weights adjust each iteration), small 
    will make convergence slower, large values will possibly make model diverge
    max_iter (int): max iterations for training
    thresh (float): error threshold to stop training
    verbose (bool): to print diagnostic text or not    
    '''
    
    curr_weights = initial_weights
    
    for itern_num in range(max_iter):
        preds = inputs.dot(curr_weights)
        error = (preds - goal_preds) ** 2
        
        if np.average(error) < thresh:
            break
            
        delta = preds - goal_preds
        weight_deltas = np.outer(inputs,delta)
        curr_weights = curr_weights - (alpha*weight_deltas)
        if verbose:
            print(f'Error: {error}')
            print(f'Delta: {delta}')
            print(f'weight_deltas: {weight_deltas}')
            print(f'weights: {initial_weights}')
            print(f'curr_weights: {curr_weights}')
            
    # return weights from model after training
    return curr_weights,preds
  ```
  
  ## What do these weights "learn"?
  
  - Each weight tries to reduce the error, but in aggregate what do they do?
  - Example with the MNIST handwritten number images dataset which is common in deep learning tutorials.
    - say we make a neural network with 784 inputs (1 for each pixel in the images) and 10 outputs (1 for each possibly written number)
    - each output neuron has 784 weights associated with it
    - each input neuron has 10 weights it multiplies its input by to the respective outputs
    - if a weight to an output is high, the network believes that input (in this case pixel) has a high correlation with this output (a given number)
      - so say pixel 15 has the highest weight for number 2, the network decided the 15th pixel is a great predictor of it being a 2
    - if you print the weights in this example you can see that the weights around the area where the digits are typically drawn are considered important
      - this is probably because the pixels outside this area are constant so the model just ignored them
  - Dot products
    - dot products are a measure of similarity between two vectors. since we're taking the dot product between the input and weight vectors to get the output, in the MNIST dataset this means an input vector that is not similar to the weights for a 2 will output a small score, and those input vectors that have similar values to the weights will get a high score
      - not sure if this is generalizeable (input vectors being similar to the weights for an output will give high scores) but makes sense for this example
  

