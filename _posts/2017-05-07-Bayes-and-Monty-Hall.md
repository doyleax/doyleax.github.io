---
layout: post
title:  "Bayes and Monty Hall"
date:   2017-05-07
categories: how-to
---

When learning about Bayesian Statistics in class, we discussed the Monty Hall problem: the gameshow has 3 doors and behind one of them is a car; the other two have goats. You can pick one door that you think the car is behind, and then Monty Hall will open one of the remaining two. 

Monty has two rules to abide by as host: Monty has to choose between the two doors that weren't chosen by the contestant, and he must choose the door with the goat behind it. 

At this point, you have the door that you chose initially, and you are told you can keep that guess or you can switch it to the one remaining door. What should you do?

The idea is that you initially have equal probability in guessing which door out of the three has a car behind it. Your **prior** probability, therefore, is 1/3. Once Monty opens one of the two remaining doors, this all changes. Now, there are two doors: the one you chose, and the door that wasn't chosen by either you or Monty.

We know that Monty opened a door with a goat behind it, because that is a rule of the game. With that, we know that one of the remaining doors has a goat, and one has a car. This makes our **marginal likelihood**--the probability that we pick the correct door, given that there are only two--equal to 1/2. The last important probability to know is the **likelihood**--how likely is Monty to pick the remaining door with the goat? The likelihood would be 1, since he has two doors to choose from, and MUST open a door with a goat. The likelihood of Monty choosing the door with the car would be 0, since he simply cannot (as a rule of the host).

What is our new probability, the posterior? The **posterior** specifically is asking, now that you chose one door and Monty opened another, what is the probability that the remaining door has the car? This would involve the contestant switching their answer. If you switch, the probability of getting the car, our posterior, = (likelihood * prior) / marginal likelihood == (1/3 * 1) / (1/2) = 2/3

This means that when you switch, your chances of getting the car go up to 2/3, as opposed to the initial 1/3 probability you had of picking the car door. Because of this, it is always best to switch your answer.

The following function allows you to input your initial guess and then decide whether or not to keep it or switch doors, and then will output whether or not you lose. Each time, the function randomly chooses which door the car is behind.

```python
import random
import numpy as np
# door- guess which door the car is behind (A, B, C)
# action- decide whether to keep or switch (K, S)

def lets_make_a_deal(door,action): 
    doors = ['A','B','C']
    car_door = np.random.choice(doors)
    if door==car_door:
        if action=='K':
            return 'win'
        elif action=='S':
            return 'lose'
    else:
        if action=='K': # you haven't picked the correct door and you stay put
            return 'lose'
        elif action=='S':
            return 'win'

lets_make_a_deal('A','K')
#lose
```
