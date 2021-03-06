# Homework3-Policy-Gradient report

TA: try to elaborate the algorithms that you implemented and any details worth mentioned.

## Problem 1: Construct a neural network to represent policy

```python
# fc1
layer1 = tf.layers.dense(
    inputs=self._observations,
    units=hidden_dim,
    activation=tf.nn.tanh,  # tanh activation
    kernel_initializer=tf.random_normal_initializer(mean=0, stddev=0.3),
    bias_initializer=tf.constant_initializer(0.1),
    name='fc1'
)
# fc2
probs = tf.layers.dense(
    inputs=layer1,
    units=out_dim,
    activation=tf.nn.softmax,
    kernel_initializer=tf.random_normal_initializer(mean=0, stddev=0.3),
    bias_initializer=tf.constant_initializer(0.1),
    name='fc2'
)        
```

> 建立兩個Fully-connected layer 的隱藏層<br>
> 參考視頻 莫煩<br>
> https://morvanzhou.github.io/tutorials/machine-learning/reinforcement-learning/5-1-policy-gradient-softmax1/

## Problem 2: Compute the surrogate loss
```python
surr_loss = -tf.reduce_mean(log_prob * self._advantages)
```
<p align="left"><img src="https://morvanzhou.github.io/static/results/reinforcement-learning/5-1-1.png" height="200"/></p>

## Problem 3: Use baseline to reduce the variance of our gradient estimate
```python
"""
1. b: values predicted by our baseline
2. use baselineto reduce variance
"""
a = r - b
```

<p align="left"><img src="image/p3-1.png" height="250"/><img src="image/p3-2.png" height="250"/></p>
> 藉由baseline去對reward進行調整 (63回合收斂)

## Problem 4: Replace baseline with None
```python
baseline = None
```
<p align="left"><img src="image/p4-1.png" height="250"/><img src="image/p4-2.png" height="250"/></p>
> 不用baseline對Loss做調整，原本以為會變更差，結果好像也還好 (59回合收斂)

## Problem 5: Actor-Critic algorithm (with bootstrapping)
```python
def discount_bootstrap(x, discount_rate, b):
    b = np.append(b[1:],0)
    y = x + discount_rate*b
    return y
```

## Problem 6: Generalized Advantage Estimation
```python
r = util.discount_bootstrap(p["rewards"], self.discount_rate, b)
target_v = util.discount_cumsum(p["rewards"], self.discount_rate)
a = r - b     
a = util.discount(a, self.discount_rate * LAMBDA)
```
<p align="left"><img src="image/p6-1.png" height="250"/><img src="image/p6-2.png" height="250"/></p>
> 使用GAE的方法，收斂時間會比較久 (95回合收斂)
