### Evaluation Index

#### Classification

- TP: number of correct classification of positive samples
- TN: number of correct classification of negative samples
- FP: number of wrong classification of negative samples
- FN: number of wrong classification of positive samples

**Precision** (精确度)

Number of correct classification in all samples which is classified as positive.

How many selected item is relevant?

**Recall** (召回率)

Number of samples correctly classified in all positive samples.

How many relevant item is selected?

**Receiver Operating Characteristic (ROC)**

It is graphical plot of the performance of a binary classification system as its decision threshold varies.

- Area Under ROC (AUR)

  AUR is the area under ROC curve, it represents the ratio of having a correct classification for a random positive and negative sample.

  - AUC = 1. This is a perfect classifier and in practice this is very unlikely.
  - AUC > 0.5. This is better than flipping a coin so it is kind of useful.
  - AUC = 0.5. This is same as flipping a coin. No value at all.
  - AUC < 0.5. This is bad but we can just invert the answer to obtain an AUC > 0.5, so no AUC < 0.5 exists.

### Linear Regression

#### Gradient Descent

- If cost is not decreasing for an iteration, it maybe overshoot and we can try to decrease learning rate. In theory, the cost should decrease after every iteration.

**Feature Scaling**

Scale every feature to same range (e.g. 0 ~ 1).

**Mean Normalization**

Replace $x_i$ with $x_i - \mu_i$ to have approximately zero mean.

**Automatic Convergence Test**

Stop if Cost Function decrease less than a certain threshold, (e.g. 1e-3). But choosing this threshold can be tricky.

**Choice of Feature**

We can choose/create different function for feature to create line that fits data better, for example we can use $x^2$ or $\sqrt{x}$ for curve.

#### Normal Equation

For some problems in machine learning, we often want to find a $W$ such that for input $X$ and output $Y$ we have $XW=Y$. Rearranging this we get $W=\frac{Y}{X}$. Note that this requires us to find the inverse of $X$ which only exists if $X$ is a square matrix. We can multiply $X$ by its transpose to get a square matrix, thus the equation becomes:
$$
\begin{align*}
XW=&Y\\
W=&\frac{Y}{X}\\
=&\frac{YX^T}{XX^T}\\
=&(X^TX)^{-1}X^TY
\end{align*}
$$
This is called normal equation, note that even though we ensured that it is a square matrix, there may not be a inverse for it (non-invertible / singular matrix), but luckily we have something called [p-inverse](https://en.wikipedia.org/wiki/Moore%E2%80%93Penrose_inverse) which guaranteed to return an inverse for the given matrix.

Normal equation can compute the optimal weights directly, no training is needed but it only works for small $X$ because finding the inverse is computationally inefficient $O(n^3)$.

Note that if $X^TX$ is non-invertible, this is often due to redundant(linearly dependent)/too many features (#features is more than #examples).

- **Regularization**

  With regularization, it is possible to prove that matrix exists an inverse even if there is too many features:
  $$
  (X^TX+\lambda I)^{-1}
  $$
  where $I$ is the identity matrix and $\lambda$ is the regularization term strictly greater than 0.

### Logistic Regression

Linear Regression can be useful but what if we want to classify data into classes? For binary classes we could apply a threshold to the output but finding this threshold can be tricky as the output can be any number, therefore logistic regression is developed similar to linear regression but it output values ranged from 0 to 1 and we can apply a threshold of 0.5 to the output and get the predict class easily.

The answer turns out to be simple, we can plug the output of the original linear regression to the sigmoid function and we will get a value ranged from 0 to 1.

<img src="D:\University_of_Manchester\Projects\learnML\images\desmos-logistic.png" alt="desmos-logistic-graph" style="zoom: 50%;" />

#### Cost Function

If we use the cost function in the linear regression, it will look something like:
$$
J(\theta)=\frac{1}{m}\sum_{i=1}^{m}\frac{1}{2}(h_\theta(x^i)-y^i)^2
$$
where $h_\theta(x^i)$ is our linear regression output plugged into the sigmoid function and $y^i$ is always $1$ or $0$. But our $h_\theta$ has some nonlinearity and it causes our cost function to be non-convex which means there can be local optimal solutions. We want our function to be convex because it ensured that our result will always be global optimal.

The solution is to make use of the negative log function and get an cost function:
$$
Cost(h_\theta(x),y)=-y\log(h_\theta(x))+(1-y)\log(1-h_\theta(x))
$$
Note that if $y=1$ then only LHS of $+$ is evaluated and if $y=0$ then only RHS of the $+$ is evaluated. Their graph looks like:

<img src="D:\University_of_Manchester\Projects\learnML\images\desmos-neg-log-2.png" alt="desmos-neg-log-2" style="zoom:50%;" />

Also note that since $h_\theta(x)$ is ranged from $[0,1]$, the result of the cost function will be ranged from $[0, \inf)$ determined by how far the output from its target value is.

This cost function has some nice properties including it is convex.

### Neural Network

#### Initialization

If we initialize al weights as zero, some weights maybe updated with the same function and can result in highly redundant unit. Therefore we should initialize parameters as random values (or use other better technique).

### SVM

#### Kernels

Kernels goes particularly well with svm, you can use kernels on other algorithm as well but it will not be as good as it goes with svm because there are computational tricks with svm to make it faster.



## Implementation Notes

- Check out effect of simultaneous/non-simultaneous update on gradient descent.
- Graph of gradients/updates of any variable.
- Try big/small learning rate.
- Gradient Checking is useful for debugging implementation of back prop (the gradient of a parameter of a point should be similar to (grad of a point - $\epsilon$ + grad of a point + $\epsilon$) / 2$\epsilon$)

