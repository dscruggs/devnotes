# 2. Fundamental Concepts
## Supervised vs Unsupervised ML
- This distinguishes the type of pattern being learned
- **Supervised**: learns to transform one dataset (input) into another dataset (output) using labeled data
- **Unsupervised**: Typically groups data, looks for patterns
  - All unsupervised learning can be thought of in a clustering framework
  
## Parametric vs Nonparametric learning
- This disginguishes the way learning is stored and the method for learning
- **Parametric**: model with a fixed number of parameters
  - Think a baby fitting shapes to holes, and does a brute force attempt at just slamming into holes until it fits
  - Simplistically can be thought of as trial and error
- **Nonparametric**: Number of parameters determined by the model (can be infinite)
  - Think a teenage counting the number of sides of the shape and then searching for the hole with the same number of sides
  - Simplisitcally can be thought of as counting
  
### Supervised Parametric Learning
- Think of the model as a board with fixed number of knobs (parameters) where the prediction is made based on the angle of the knobs
  - Will make a prediction, check if prediction is correct. if incorrect will change angle of knobs and try again
- entirety of what the model will predict can be captured by the angle of the knobs at any time
- can be thought of as a search algorithm (not literally I don't think but conceptually. Searching for the right combination of knobs)
- each parameter can be thought of as the prediction's sensitivity to different types of input data
- 3 steps
  - Predict
  - Compare to ground truth
  - learn the pattern
    - if prediction incorrect adjust the parameters by how much the prediction was incorrect
    
### Unsupervised Parametric Learning
- Uses knobs to group data, but in this case it usually has several knobs for each group. Each of these map to the input data's affinity to that particular group (exceptions, 
this is high level description)
- Model transforms the data into its subscribing groups, variation in ways different models do this

### Nonparametric learning (this is a pretty bad explanation probably want to read up on this more)
- Counting based methods (oversimplified, basically just doesn't have predefined parameters)
- Stop light example
  - count how many cars go at each light color of a stoplight
  - model would have 3 parameters. 3 counts of how many cars go at each light
  - would have 5 parameters if there were 5 lights. number of parameters adjusted by the input data, this is what makes it nonparametric
