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
  - you calculate each `delta` the same way and then multiply them all by the same single `input`
    - this becomes each weight's `weight_delta`
  - the same `stochastic gradient descent` method is used here and for learning across many network architectures
  - 

