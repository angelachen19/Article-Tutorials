
# Implementing Neural Networks with Numpy for Absolute Beginners - Part 2: Linear Regression

##### In this tutorial, you will learn to implement Linear Regression for prediction using Numpy in detail and also visualize how the algorithm learns epoch by epoch. In addition to this, you will explore two layer Neural Networks.

In the previous tutorial, you got a very brief overview of the field of AI and learnt 
about perceptrons. 
In this tutorial, you will dig very deep into implementing a Linear Perceptron and build upon 
those concepts to understand Linear Regression.



## Linear Activation Function

Lets assume that there is only one input and bias to the perceptron as shown below:

<p align="center">
<img src="
https://docs.google.com/drawings/d/e/
2PACX-1vQ0sj3f-bHdNDVltSyUEHQqccTNxA9zWCeskyI5pdpbyoWmYODkGc_J_T_8PYdhvUZ5jUJG-XhuU8-D/
pub?w=2800&h=2168" 
alt="Drawing" width="600"/>
</p>

The resulting linear output(i.e., the sum) will be 
<img src="http://latex.codecogs.com/gif.latex?y&space;=&space;m.x&space;&plus;&space;b" 
title="y = m.x + b" />. This is the equation of a straight line, 
as shown in the below figure. 

<p align="center">
<img src="http://mathonweb.com/help_ebook/html/equations_1/eqs21.gif" alt="Drawing" width="300"/>
</p>

*It must be noted here that when no activation function is used, 
we can say that the acitvation function is linear.*

This is a <b>multivariate(multiple variables) linear equation.</b>

Let us see how this is utilized for predicting the actual output of 
<img src="http://latex.codecogs.com/gif.latex?y" title="y" /> in the 
next section i.e., <b>*Linear Regression*</b>.

## Linear Regression

Fitting a linear equation on a given set of data in 
<img src="http://latex.codecogs.com/gif.latex?n" title="n" />-dimensional 
space is called <b>Linear Regression</b>. The image below shows an example of Linear Regression. 

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*eeIvlwkMNG1wSmj3FR6M2g.gif" 
alt="Drawing" width="500"/>
</p>

In simple words, you try to find the best values of 
<img src="http://latex.codecogs.com/gif.latex?m" title="m" /> and 
<img src="http://latex.codecogs.com/gif.latex?b" title="b" /> 
that best fits the set of points as shown in the above figure. 
When we have obtained the best possible fit, we can predict the 
<img src="http://latex.codecogs.com/gif.latex?y" title="y" /> 
values given <img src="http://latex.codecogs.com/gif.latex?x" title="x" />.

A very popular example is the *housing price prediction* problem. 
In this problem you are given a set of values like the area of 
the house and the number of rooms etc. as features and you 
must predict the price of the house given these values.

So the big question is... How does the prediction algorithm work? 
How does it learn to predict?

Let's learn this on the go!

Let's start importing the required packages.


```python
# Numpy for efficient Matrix and mathematical operations.
import numpy as np

# Pandas for table and other related operations
import pandas as pd

# Matplotlib for visualizing graphs
import matplotlib.pyplot as plt
from matplotlib.pylab import rcParams

# Sklearn for creating a dataset
from sklearn.datasets import make_regression

# train_test_split for splitting the data into training and testing data
from sklearn.model_selection import train_test_split

% matplotlib inline

# Set parameters for plotting
params = {'axes.titlesize': 'xx-large',               # Set title size
          'axes.labelsize': 'x-large',                # Set label size
          'figure.figsize': (8, 6)                    # Set a figure Size
}

rcParams.update(params)
```

You'll use the sklearn dataset generator for creating the dataset. 
You shall also the sklearn package for splitting the data into 
training and test data. If you are not aware of sklearn, it 
is a rich package with many machine learning algorithms. 
Although, you get prebuilt functions for performing linear 
regression, you are going to build it from scratch.

For creating the dataset, you must first set a list of hyperparameters $-$ 
while $m$ and $b$ are parameters, the number of samples, the number of 
input features, the number of neurons, the learning rate, the number 
of iterations/epochs for training etc. are called hyperparameters. 
You shall learn about these hyperparameters as you implement the algorithm.

For now, you shall set the number of training samples, the number of 
input features, the learning rate and epochs.You shall 
understand learning rate and epochs in a short while.


```python
# Sample size
M = 200

# No. of input features
n = 1

# Learning Rate - Define during explanation
l_r = 0.05

# Number of iterations for updates - Define during explanation
epochs = 51
```

Your first task would be to import or generate the data. In this tutorial, you'll 
generate the dataset using `sklearn`'s `make_regression` function.

For purpose of learning, we shall keep the number of features minimal so that it is 
easy to visualize.
Hence, you must choose only one feature.


```python
>>>X, y = make_regression(n_samples=M, n_features=n, n_informative=n, 
                             n_targets=1, random_state=42, noise=10)
```

Now, it's time to visualize what the data generator has cooked up!


```python
def plot_graph(X, y):
    
    # Plot the original set of datapoints
    _ = plt.scatter(X, y, alpha=0.8)
    
    _ = plt.title('Plot of Datapoints generated')
    _ = plt.xlabel('x')
    _ = plt.ylabel('y')

    plt.show()
```


```python
>>>plot_graph(X, y)
```


![png](output_16_0.png)


Let's check the shape of the vectors for consistency.


```python
>>>print('Shape of vector X:', X.shape)
>>>print('Shape of vector y:', y.shape)
```

    Shape of vector X: (200, 1)
    Shape of vector y: (200,)
    


```python
# Function to reset the sizes 
def reset_sizes(*args):
     
    return tuple(arg.reshape((arg.shape[0], 1)) for arg in args)
```


```python
# Reset the size from (200,) -> (200, 1)
>>>X, y = reset_sizes(X, y)
>>>X.shape
```




    (200, 1)



Next you will have to split the dataset into train and test sets, 
so that you can test the accuracy of the 
regression model using a part of the dataset once you have trained the model.

Now let's split the data into train set and test set. You shall 
also reset the sizes so there is no 
discrepency in doing matrix computations.


```python
# Split the data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
```

In our case, the training set is 80% and the test set is 20%.

Let's check the shape of the Train and Test datasets created.


```python
>>>print(X_train.shape, y_train.shape)
>>>print(X_test.shape, y_test.shape)
```

    (160, 1) (160, 1)
    (40, 1) (40, 1)
    

As you can see, 80% of the data i.e., 80% of 200 data points is 160 which is correct.

The next step is to randomly generate a line with a random slope and an intercept(bias). 
The goal is to achieve the best fit for the line.


```python
# Function to generate parameters of the linear regression model, m & b.
def init_params():
    
    m = np.random.normal(scale=10)
    b = np.random.normal(scale=10)
   
    return m, b
```


```python
# Call function to generate paramets
>>>m, b = init_params()
```

Now, given <img src="http://latex.codecogs.com/gif.latex?m" title="m" /> & 
<img src="http://latex.codecogs.com/gif.latex?b" title="b" />, we can 
plot the line so generated.

Let's update the function ```plot_graph``` to show the predicted line too.


```python
def plot_graph(dataset, pred_line=None):
    
    X, y = dataset['X'], dataset['y']
    
    # Plot the set of datapoints
    _ = plt.scatter(X, y, alpha=0.8)                                
    
    if(pred_line != None):
        
        x_line, y_line = pred_line['x_line'], pred_line['y_line']
        
        _ = plt.plot(x_line, y_line, linewidth=2, markersize=12, color='red', alpha=0.8)      # Plot the randomly generated line
        
        _ = plt.title('Random Line on set of Datapoints')
    
    else:
        _ = plt.title('Plot of Datapoints')
   
    _ = plt.xlabel('x')
    _ = plt.ylabel('y')

    plt.show()
```


```python
# Function to plot predicted line
def plot_pred_line(X, y, m, b):
    
    # Generate a set of datapoints on x for creating a line.
    x_line = np.linspace(np.min(X), np.max(X), 10)

    # Calculate the corresponding y with random values of m & b
    y_line = m * x_line + b
    
    dataset = {'X': X, 'y': y}
    
    pred_line = {'x_line': x_line, 'y_line':y_line}
    
    plot_graph(dataset, pred_line)
    
    return 
```


```python
>>>plot_pred_line(X_train, y_train, m, b)
```


![png](output_32_0.png)


Since the line is now generated, you'll need to predict the 
values it is producing for a given value 
of <img src="http://latex.codecogs.com/gif.latex?x" title="x" />. 
From this value, all there is to do is to calculate 
their mean squared error.

Let us predict the values of <img src="http://latex.codecogs.com/gif.latex?y(y_{pred})" 
title="y(y_{pred})" /> 
from the parameters <img src="http://latex.codecogs.com/gif.latex?m" title="m" /> & 
<img src="http://latex.codecogs.com/gif.latex?b" title="b" /> given the datapoints 
<img src="http://latex.codecogs.com/gif.latex?X_{train}" title="X_{train}" /> 
by defining a function ```forward_prop```


```python
def forward_prop(X, m, b):
    
    y_pred = m * X + b
    
    return y_pred
```


```python
>>>y_pred = forward_prop(X_train, m, b)
```

## Cost/Loss Function

Now, that you have both the corresding values for 
<img src="http://latex.codecogs.com/gif.latex?X_{train}" title="X_{train}" /> 
and the predicted values 
for <img src="http://latex.codecogs.com/gif.latex?y(y_{pred})" title="y(y_{pred})" /> you'll 
calculate the Cost/Error/Loss Function. We shall stick to the term Loss. 

For the <img src="http://latex.codecogs.com/gif.latex?Loss" title="Loss" />, you must compute 
the <img src="http://latex.codecogs.com/gif.latex?Mean&space;\&space;Squared&space;\&space;Error$" 
title="Mean \ Squared \ Error$" /> and minimize it.

The <img src="http://latex.codecogs.com/gif.latex?Loss(Mean&space;\&space;Squared&space;\
&space;Error)" 
title="Loss(Mean \ Squared \ Error)" /> is:

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?MSE&space;=&space;(y'^{(i)}&space;-&space;y^{(i)})^2" 
title="MSE = (y'^{(i)} - y^{(i)})^2" />
</p>

<br>Summing over all <img src="http://latex.codecogs.com/gif.latex?M" title="M" /> examples, 
we obtain the <img src="http://latex.codecogs.com/gif.latex?Cost/Loss&space;\&space;fn." 
title="Cost/Loss \ fn." /> 
as below:

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?L&space;=&space;\frac{1}{2M}\sum_{i=0}^M(y'
^{(i)}&space;-&space;y^{(i)})^2" title="L = \frac{1}{2M}\sum_{i=0}^M(y'^{(i)} - y^{(i)})^2" />
</p>

Hence, our goal would be to minimize the $Loss$ thereby fitting the curve.

Let us now codify this.

We shall also save each value of $loss$ that will be computed.


```python
def compute_loss(y, y_pred):
    loss = 1 / 2 * np.mean((y_pred - y)**2)
    
    return loss
```


```python
>>>losses = []
```

```python
>>>compute_loss(y_train, y_pred)
```




    4005.265725705774



Lets modify the above ```plot_graph``` function defined above to plot the Loss too.




```python
def plot_graph(dataset, pred_line=None, losses=None):
    
    plots = 2 if losses!=None else 1
    
    fig = plt.figure(figsize=(8 * plots, 6))
    
    
    X, y = dataset['X'], dataset['y']
    
    ax1 = fig.add_subplot(1, plots, 1)
    ax1.scatter(X, y, alpha=0.8)                                # Plot the original set of datapoints
    
    if(pred_line != None):

        
        x_line, y_line = pred_line['x_line'], pred_line['y_line']
        
        ax1.plot(x_line, y_line, linewidth=2, markersize=12, color='red', alpha=0.8)      # Plot the randomly generated line
        
        ax1.set_title('Predicted Line on set of Datapoints')
    
    else:
        ax1.set_title('Plot of Datapoints generated')
   
    ax1.set_xlabel('x')
    ax1.set_ylabel('y')
    
    if(losses!=None):
        ax2 = fig.add_subplot(1, plots, 2)
        ax2.plot(np.arange(len(losses)), losses, marker='o')
        
        ax2.set_xlabel('Epoch')
        ax2.set_ylabel('Loss')
        ax2.set_title('Loss')

    plt.show()
```


```python
def plot_pred_line(X, y, m, b,losses=None):
    
    # Generate a set of datapoints on x for creating a line.
    # We shall consider the range of X_train for generating the line so that the line superposes the datapoints.
    x_line = np.linspace(np.min(X), np.max(X), 10)             
    
    # Calculate the corresponding y with the parameter values of m & b
    y_line = m * x_line + b                                                
    
    plot_graph(dataset={'X': X, 'y': y}, pred_line={'x_line': x_line, 'y_line':y_line}, losses=losses)
    
    return 
```

You'll shall visualize the line created from the parameters 
<img src="http://latex.codecogs.com/gif.latex?m" title="m" /> 
and <img src="http://latex.codecogs.com/gif.latex?b" title="b" />.


```python
>>>plot_pred_line(X_train, y_train, m, b,losses)
```


![png](output_44_0.png)


Now that you have computed the loss, let's minimize it.

## Gradient Descent for Linear Regression

Since, $Loss$ is the dependent variable and <img src="http://latex.codecogs.com/gif.latex?m" 
title="m" /> 
& <img src="http://latex.codecogs.com/gif.latex?c" title="c" /> are the independent variables, 
we'll have to update <img src="http://latex.codecogs.com/gif.latex?m" title="m" /> 
& <img src="http://latex.codecogs.com/gif.latex?b" title="b" /> so as to find the minimum Loss.

So the next question would be, How can I update the parameters 
<img src="http://latex.codecogs.com/gif.latex?m" title="m" /> and 
<img src="http://latex.codecogs.com/gif.latex?b" title="b" />?


Let us for instance consider just a single parameter <img src="http://latex.codecogs.com/gif.latex?p" title="p" /> 
as shown below and let <img src="http://latex.codecogs.com/gif.latex?t(target)" title="t(target)" /> 
be the actual value that has to be predicted. 
We see that as <img src="http://latex.codecogs.com/gif.latex?cost" title="cost" /> 
converges to the minima, 
the parameter <img src="http://latex.codecogs.com/gif.latex?p" title="p" /> 
reaches an optimum value for the minimum 
<img src="http://latex.codecogs.com/gif.latex?cost" title="cost" />. 
Let's say the optimum value of <img src="http://latex.codecogs.com/gif.latex?p" title="p" /> is 
<img src="http://latex.codecogs.com/gif.latex?a" title="a" />.

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*pwPIG-GWHyaPVMVGG5OhAQ.gif" alt="Drawing" 
width="800"/>
</p>

Let's gain a bit of intuition as to what the graph is saying.

It is clear from the graph, that as 
<img src="http://latex.codecogs.com/gif.latex?p" title="p" /> 
moves towards <img src="http://latex.codecogs.com/gif.latex?a" title="a" />, 
the Cost decreases and as it 
moves away from it, the cost increase.

<b>Now, how can we make <img src="http://latex.codecogs.com/gif.latex?p" title="p" /> 
move towards <img src="http://latex.codecogs.com/gif.latex?a" title="a" />
, whether it is on the 
left or to the right of $a$ as shown in figure?</b>

Let us consider the <img src="http://latex.codecogs.com/gif.latex?p" title="p" /> 
of the curve. From calculus, we know that 
the <img src="http://latex.codecogs.com/gif.latex?slope" title="slope" /> 
of a curve at a point is 
given by <img src="http://latex.codecogs.com/gif.latex?\mathrm&space;dy/\mathrm&space;dx" 
title="\mathrm dy/\mathrm dx" />
(here it is <img src="http://latex.codecogs.com/gif.latex?
\mathrm&space;dL/\mathrm&space;dp" title="\mathrm dL/\mathrm dp" /> 
where <img src="http://latex.codecogs.com/gif.latex?L&space;\rightarrow&space;Loss" 
title="L \rightarrow Loss" />). From the fig., 
when <img src="http://latex.codecogs.com/gif.latex?p" title="p" /> 
is to the right of 
<img src="http://latex.codecogs.com/gif.latex?a" title="a" />, 
the <img src="http://latex.codecogs.com/gif.latex?slope" title="slope" /> is 
obviously <img src="http://latex.codecogs.com/gif.latex?-ve" title="-ve" /> 
and when it's to the right, 
the <img src="http://latex.codecogs.com/gif.latex?slope" title="slope" /> 
would be <img src="http://latex.codecogs.com/gif.latex?+ve" title="+ve" />. 
But we see that if 
<img src="http://latex.codecogs.com/gif.latex?p" title="p" /> is to 
the left of <img src="http://latex.codecogs.com/gif.latex?a" title="a" />, 
some value must be added to 
<img src="http://latex.codecogs.com/gif.latex?p" title="p" />. 
Likewise, some value must subtracted when 
<img src="http://latex.codecogs.com/gif.latex?p" title="p" /> 
is to the right of <img src="http://latex.codecogs.com/gif.latex?a" title="a" />.

This means that when 
<img src="http://latex.codecogs.com/gif.latex?slope&space;\rightarrow&space;-ve&space;\
implies&space;p&space;=&space;p&space;&plus;&space;(some&space;\space&space;val.)" 
title="slope \rightarrow -ve \implies p = p + (some \space val.)" /> 
and when 
<img src="http://latex.codecogs.com/gif.latex?slope&space;\
rightarrow&space;&plus;ve&space;\implies&space;p&space;=&space;p&space;-&space;
(some&space;\space&space;val.)" 
title="slope \rightarrow +ve \implies p = p - (some \space val.)" /> 
to move towards <img src="http://latex.codecogs.com/gif.latex?a" title="a" />

<img src="http://latex.codecogs.com/gif.latex?\therefore" title="\therefore" /> 
We subtract 
<img src="http://latex.codecogs.com/gif.latex?slope" title="slope" /> itself to 
<img src="http://latex.codecogs.com/gif.latex?p" title="a" />. 
This way, slope is negated so that it could be appropriately 
added or subtracted.The resulting equation would be, 

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?
p&space;=&space;p&space;-&space;slope" 
title="p = p - slope" />
<p align="center">
<img src="http://latex.codecogs.com/gif.latex?=&space;p&space;-&space;
\dfrac{\mathrm&space;dL}{\mathrm&space;dp}" 
title="= p - \dfrac{\mathrm dL}{\mathrm dp}" />
<p align="center">
<img src="http://latex.codecogs.com/gif.latex?\
implies&space;p&space;=&space;p&space;-&space;\mathrm&space;dp" 
title="\implies p = p - \mathrm dp" />

It must also be observed that if the cost is too high, 
the <img src="http://latex.codecogs.com/gif.latex?slope" title="slope" />
 will be too high. Hence, while subtracting 
the <img src="http://latex.codecogs.com/gif.latex?slope" title="slope" /> 
from <img src="http://latex.codecogs.com/gif.latex?p" title="p" />, 
<img src="http://latex.codecogs.com/gif.latex?p" title="p" /> 
value might overshoot 
<img src="http://latex.codecogs.com/gif.latex?a" title="a" />. 
It implies that it is necessary to decrease the 
value of <img src="http://latex.codecogs.com/gif.latex?slope" title="slope" />
 so that <img src="http://latex.codecogs.com/gif.latex?p" title="p" />
 does not overshoot <img src="http://latex.codecogs.com/gif.latex?a" title="a" />. 
Therefore, we introduce a dampening factor called 
<img src="http://latex.codecogs.com/gif.latex?Learning&space;\&space;Rate&space;(\alpha)" 
title="Learning \ Rate (\alpha)" />
to the <img src="http://latex.codecogs.com/gif.latex?slope" title="slope" />.

What we finally obtain would be,

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?p&space;=&space;p&space;-&space;
\alpha&space;.\mathrm&space;dp" title="p = p - \alpha .\mathrm dp" />

A shown in the figure, the trajectory taken by 
<img src="http://latex.codecogs.com/gif.latex?p" title="p" /> against 
<img src="http://latex.codecogs.com/gif.latex?Cost" title="Cost" />
 is that of a Bel curve.

This method is called the <b>Gradient Descent</b>.

In our case, we use two parameters 
<img src="http://latex.codecogs.com/gif.latex?m" title="m" /> 
and <img src="http://latex.codecogs.com/gif.latex?b" title="b" />. 
Therefore, the bel curve would be *3-dimensional* as shown in the below figure.
<p align="center">
<img src="https://media.giphy.com/media/O9rcZVmRcEGqI/giphy.gif" 
alt="Drawing" width="700"/>
</p>

We compute the partial derivative of the loss function w.r.t to the 
parameters <img src="http://latex.codecogs.com/gif.latex?m" title="m" /> & 
<img src="http://latex.codecogs.com/gif.latex?b" title="b" /> to obtain i.e.,

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?\frac{\partial&space;L}
{\partial&space;m}&space;=&space;\partial{m}&space;=&space;
\frac{1}{M}.\sum_{i=0}^M\Big(y'^{(i)}&space;-&space;y^{(i)}\Big).x^{(i)}\qquad--(1)" 
title="\frac{\partial L}{\partial m} = \partial{m} = \frac{1}{M}.\sum_{i=0}^M
\Big(y'^{(i)} - y^{(i)}\Big).x^{(i)}\qquad--(1)" />

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?\&" title="\&" /> 

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?\quad&space;\frac{\partial&space;L}
{\partial&space;b}&space;=&space;\partial{b}&space;=&space;\frac{1}{M}.\sum_{i=0}^M\
Big(y'^{(i)}&space;-&space;y^{(i)}\Big)\qquad\qquad--(2)" 
title="\quad \frac{\partial L}{\partial b} = \partial{b} = \frac{1}{M}.\sum_{i=0}^M\
Big(y'^{(i)} - y^{(i)}\Big)\qquad\qquad--(2)" />


```python
def grad_desc(m, b, X_train, y_train, y_pred):
    dm = np.mean((y_pred - y_train) * X_train)
    db = np.mean(y_pred - y_train)
    
    return dm, db
```

### Updating the parameters

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?m&space;=&space;m&space;-&space;
\alpha&space;.&space;\partial{m}&space;\qquad\qquad\qquad\&space;--(3)\\" 
title="m = m - \alpha . \partial{m} \qquad\qquad\qquad--(3)\\" />
</p>
<p align="center>
<img src="http://latex.codecogs.com/gif.latex?b&space;=&space;b&space;-&space;
\alpha&space;.&space;\partial{b}&space;\qquad\qquad\qquad\&space;--(4)" 
title="b = b - \alpha . \partial{b} \qquad\qquad\qquad\ --(4)" />
</p>



```python
def update_params(m, b, dm, db, l_r):
    
    m -= l_r * dm
    b -= l_r * db
    
    return m, b
```


Let us define a function ```back_prop```, which calls both ```grad_desc``` 
and ```update_params```.


```python
def back_prop(X_train, y_train, y_pred, m, b, l_r):

    dm, db = grad_desc(m, b, X_train, y_train, y_pred)
    
    m, b = update_params(m, b, dm, db, l_r)

    return m, b
```

We shall now combine and call all the functions at once to see how the algorithm works.

We shall again set and tune the parameters to improve the accuracy of our linear regression model.


```python
# Sample size
M = 200

# No. of input features
n = 1

# Learning Rate - Define during explanation
l_r = 0.05

# Number of iterations for updates - Define during explanation
epochs = 61
```


```python
X, y = make_regression(n_samples=M, n_features=n, n_informative=n, 
                         n_targets=1, random_state=42, noise=10)

dataset = {'X': X, 'y': y}

plot_graph(dataset)

m, b = init_params()

X, y = reset_sizes(X, y)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

losses = []

for i in range(epochs):
    y_pred = forward_prop(X_train, m, b)

    loss = compute_loss(y_train, y_pred)
    losses.append(loss)

    m, b = back_prop(X_train, y_train, y_pred, m, b, l_r)

    if(i%10==0):
        print('Epoch: ', i)
        print('Loss = ', loss)
        plot_pred_line(X_train, y_train, m, b, losses)

del losses[:]
```


![png](output_58_0.png)


    Epoch:  0
    Loss =  2934.082243250548
    


![png](output_58_2.png)


    Epoch:  10
    Loss =  1246.3617292447889
    


![png](output_58_4.png)


    Epoch:  20
    Loss =  546.310951004311
    


![png](output_58_6.png)


    Epoch:  30
    Loss =  255.88020867147344
    


![png](output_58_8.png)


    Epoch:  40
    Loss =  135.36914932067438
    


![png](output_58_10.png)


    Epoch:  50
    Loss =  85.35744394597806
    


![png](output_58_12.png)


    Epoch:  60
    Loss =  64.60029693013243
    


![png](output_58_14.png)

## Prediction


```python
# Prediction
print('Prediction: ')
y_pred = forward_prop(X_test, m, b)
loss = compute_loss(y_test, y_pred)
print('Loss = ', loss)
accuracy = np.mean(np.fabs((y_pred - y_test) / y_test)) * 100
print('Accuracy = {}%'.format(round(accuracy, 4)))
plot_pred_line(X_test, y_test, m, b)

print('Hence \nm = ', m)
print('b = ', b)
```

    Prediction: 
    Loss =  56.53060443946197
    Accuracy = 80.1676%
    


![png](output_58_16.png)


    Hence 
    m =  82.34083095217943
    b =  0.46491578390750576
    


## Two Layer Neural Neutwork with Linear Activation Function

Now, let us consider scaling this up to a 2 layer network as shown in the below figure.
<p align="center">
<img src="https://docs.google.com/drawings/d/e/
2PACX-1vQGsJESLwUjPIqrxaK4tZBsOBNuSQbzl0RKd0vL3nV8-QEf8rbP6ZqbKTszCUxdgvzcCSgl6WTQikSu/
pub?w=3649&h=2880" alt="two layer network with Linear Activation Function." width="600"/>
</p>

From the image, we observe that there are two inputs each to the 
two neurons in the first layer and an output neuron in the second layer.

We can represent them in vector form as:
<p align="center">
<img src="http://latex.codecogs.com/gif.latex?z_1^{[1]}&space;=&space;x.w_1^{[1]}&space;
\\&space;\\&space;=&space;\begin{bmatrix}&space;x_0&space;&&space;x_1&space;
&&space;x_2&space;\end{bmatrix}&space;.&space;\begin{bmatrix}&space;w_{10}^{[1]}&space;
\\&space;w_{11}^{[1]}&space;\\&space;w_{12}^{[1]}&space;\end{bmatrix}\\&space;\\&space;
\\&space;=&space;w_{10}^{[1]}&space;&plus;&space;w_{11}^{[1]}.x_1&space;&plus;&space;
w_{12}^{[1]}.x_2,&space;\\" 
title="z_1^{[1]} = x.w_1^{[1]} \\ \\ = \begin{bmatrix} x_0 & x_1 & x_2 \end{bmatrix} . 
\begin{bmatrix} w_{10}^{[1]} \\ w_{11}^{[1]} \\ w_{12}^{[1]} \end{bmatrix}\\ \\ 
\\ = w_{10}^{[1]} + w_{11}^{[1]}.x_1 + w_{12}^{[1]}.x_2, \\" />

While doing matrix computations, we'll need to take care of the dimensions 
and multiply. Hence, we rearrange a bit to arrive at the required output.

The expansion of the equation is not required and hence let's stick to 

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?z_1^{[1]}&space;=&space;x.w_1^{[1]}" 
title="z_1^{[1]} = x.w_1^{[1]}" />


Similarly, the value of 

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?z_2^{[1]}&space;=&space;x.w_2^{[1]}" 
title="z_2^{[1]} = x.w_2^{[1]}" />

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?\therefore&space;z^{[1]}&space;=&space;
\begin{bmatrix}&space;z_1^{[1]}&space;&&space;z_2^{[1]}&space;\end{bmatrix}" 
title="\therefore z^{[1]} = \begin{bmatrix} z_1^{[1]} & z_2^{[1]} \end{bmatrix}" />


Now the ouput from the <img src="http://latex.codecogs.com/gif.latex?2^{nd}" 
title="2^{nd}" /> 
layer will be:

<p align="center">
<img src="http://latex.codecogs.com/gif.latex?z^{[2]}&space;=&space;z^{[1]}.w^{[2]}&space;
=&space;w_{0}^{[2]}&space;&plus;&space;w_{1}^{[2]}.z_1^{[1]}&space;&plus;&space;w_{2}^{[2]}.
z_2^{[1]}" 
title="z^{[2]} = z^{[1]}.w^{[2]} = w_{0}^{[2]} + w_{1}^{[2]}.z_1^{[1]} + w_{2}^{[2]}.z_2^{[1]}" />
<p align="center">
<img src="http://latex.codecogs.com/gif.latex?=&space;w_{0}^{[2]}&space;&plus;
&space;w_{1}^{[2]}.(x.w_1^{[1]})&space;&plus;&space;w_{2}^{[2]}.(x.w_2^{[1]})" 
title="= w_{0}^{[2]} + w_{1}^{[2]}.(x.w_1^{[1]}) + w_{2}^{[2]}.(x.w_2^{[1]})" />
<p align="center">
<img src="http://latex.codecogs.com/gif.latex?\implies&space;z^{[2]}&space;=&space;w_0'
&space;&plus;&space;w_1'.x&space;\qquad&space;\text{[where,&space;}&space;w_0'
&space;\&space;\&&space;\&space;w_1'&space;\text{are&space;some&space;values]}" 
title="\implies z^{[2]} = w_0' + w_1'.x \qquad \text{[where, } w_0' \ \& \ w_1' 
\text{are some values]}" />

From the above set of equations, we see that a neural network 
with a linear activation function reduces to a *linear equation*. 
This defeats the purpose of the whole neural network which tries 
to approximate to complex curves in space, so as to find the 
optimal solution. <b>Hence, it should be strictly noted that a 
linear function cannot be used as an activation function for 
the neural network,</b> *although it can be used only in the 
last layer for regression problems*.

### Conclusion

In this tutorial, you learnt
1. The concept of  perceptron and also neural networks.

2. Linear Activation functions perform the tasks of regression i.e., 
learn to predict and forecast values. This method is extensively 
called *Linear Regression* everywhere.

3. An MLP(Multi-Layer Perceptron) with a linear activation 
function reduces to a normal Linear Regression task. Hence, 
linear activations must not be used in the hidden layers of a network. 
However, it can be used in the last layer for regression/prediction tasks.

In the next tutorial, you'll learn about Sigmoid Activation Function 
and perform Logistic Regression.