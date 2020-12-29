# 4. Gradient Descent

## Comparing and Learning
- How does a neural network learn? Make a prediction and compare to the true value.
  - Measure error (different ways to do this)
    - Usually want error to always be positive (if you're over or under by 3, you still missed by 3)
    - Mean squared error (makes error value exponentially bigger the further it is)
    - Absolute error (error scales linearly)

### Hot and Cold Learning
 - Simply increase or decrease the weight to reduce the error between the prediction and the true value.
 - Try both adjusting up and down and see what reduces error more, then pick the greater reduction
 - Problems:
  - Very inefficient. Need to predict multiple times for one adjustment
  - Constant step size, so can easily overshoot estimate
  - Basically this method tells you which direction to go, but not how much to increase/decrease weight by
 - Need to predict both direction and amount of increase/decrease

### Calculating both direction and magnitude from error
- Gradient Descent
  - basically does hot and cold learning but also includes scaling conditions to more efficiently converge on an error minimum (not just blindly increase/decrease by same amount every time until error is small, will adjust big if error is big and vice versa for small error)
  - one way of doing this is to multiply error by the input, this takes care of scaling and negative flipping
  - can use parameter alpha to adjust how "fast" a network learns (multiplies weight adjustment by 0-1 number)
 - purpose of gradient descent is to make the weights better fit the data (find the optimum weights that best fit all the data)
 - Goal: need to find the error's relationship to the changing of the weights
 - gives a little overview of derivatives
### Big picture for neural networks:
  - Neural networks are a bunch of weights (and biases but book hasn't mentioned this yet)
  - For any error function you choose, you want to calculate the relationship between each weight and the final error of the network
  - You then want to adjust the weights using this information to get the error down to 0 (or as close as possible)
  
### Using derivatives to learn
  - weight_delta is your derivative (error x input)
  - final neural network code:
  ```python
  def gradient_descent(input,initial_weight,goal_pred,max_iter=50,thresh = 0.0000001,verbose=True):
    curr_weight = initial_weight
    
    for iter_num in range(max_iter):
        pred = input * curr_weight
        error = (pred - goal_pred) ** 2
        print(f'Error: {error}  Prediction: {pred}')
        if error < thresh:
            break
        else:
            #this is raw error
            delta = pred - goal_pred
            #this is derivative for error
            weight_delta = delta * input
            curr_weight = curr_weight - weight_delta
    
    return pred
    
    pred = gradient_descent(input=0.5,initial_weight=0.5,goal_pred=0.8)
  ```
  
### Breaking Gradient Descent
  - it's possible that the neural network can diverge and explode in value
  - solution: introduce alpha parameter to reduce amount we adjust weight by to not overshoot minimum
  - large alpha will be prone to diverging, small alpha will make learning very slow (I think also maybe prone to overfitting/getting stuck in local minimum? not sure on that one)
  - finding optimum alpha is really just a matter of guesswork and is dependent on neural network and input data
  - code with alpha:
  ```python
  def gradient_descent(input,initial_weight,goal_pred,alpha=0.1,max_iter=50,thresh = 0.0000001,verbose=True):
    curr_weight = initial_weight
    
    for iter_num in range(max_iter):
        pred = input * curr_weight
        error = (pred - goal_pred) ** 2
        print(f'Error: {error}  Prediction: {pred}')
        if error < thresh:
            break
        else:
            delta = pred - goal_pred
            weight_delta = delta * input
            curr_weight = curr_weight - (alpha * weight_delta)
    
    return pred
    
    pred = gradient_descent(input=0.5,initial_weight=0.5,goal_pred=0.8,alpha=0.1)
  ```
  
