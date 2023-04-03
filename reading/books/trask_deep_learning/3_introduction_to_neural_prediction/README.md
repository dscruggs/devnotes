# 3. Introduction to Neural Prediction

## Prediction

### Single Input
- How many data points to propogate at once depends on the data. for example a photo needs all pixels but another type of data might only need 1 point
- need to know the shape of the input and output datasets before you make the network
- 1 input -> 1 output only requires a single weight
- code example on a simple 1 input 1 output NN
  ```python
  def simple_neural_network(input, weight):
    #input/weight are scalars
    prediction = input * weight
    return prediction
  ```
- it will take the output prediction and compare the the ground truth, then adjust the weight based on the error

### Multiple inputs
- Just have a weight for each input
- can increase accuracy of network
- code example
  ```python
  def multi_input_nn(input_vector,weight_vector):
    #input/weight vectors are np arrays from one observation
    #output is just a weighted sum of the inputs
    pred_vector = input_vector * weight_vector
    pred = pred_vector.sum()
    return pred
  ```

### Single Input Multiple Outputs
  - Equivalent to if you had 3 separate single weight neural networks
  
### Multiple Inputs Multiple Outputs
  - independent weighted sum with the weights.
  ```python
  def mult_input_and_output_nn(inputs,weights):
    # weights 3x3 matrix inputs 3x1 vector
    preds = np.dot(weights,inputs)
    return preds
  ```
### Multi Layer NN
  - inputs of hidden layers come from outputs of previous layers
  - example
  ```python
  #this example shows predicting 3 probabilities with 3 inputs into a 1 hidden layer network and number of toes, w/l record, and number of fans as inputs
  def multi_layer_nn(inputs,weights):
      #output of input layer feeds into hidden layer which feeds out to pred (output layer)
      hid = inputs.dot(weights[0])
      pred = hid.dot(weights[1])

      return pred

  toes = np.array([8.5,9.5,9.9,9.0])
  wlrec = np.array([0.65,0.8,0.8,0.9])
  nfans = np.array([1.2,1.3,0.5,1.0])
  input_array = np.array([toes,wlrec,nfans]).transpose()

  ih_wgt = np.array([[0.1,0.2,-0.1],[-0.1,0.1,0.9],[0.1,0.4,0.1]]).T
  hp_wgt = np.array([[0.3,1.1,-0.3],[0.1,0.2,0.0],[0.0,1.3,0.1]]).T
  weights_multi = [ih_wgt,hp_wgt]

  pred = multi_layer_nn(input_array[0,:],weights_multi)
  print(pred)
  
  #[0.2135 0.145  0.5065]
  ```
  
## Summary
  - this process of passing inputs into model and outputs of layers into more layers to make a prediction is known as ```forward propogation```
    - it's called this because we are propogating activations forward in the network.
