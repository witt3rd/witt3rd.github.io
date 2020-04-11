---
layout: post
title:  "New World Models"
date:   2018-06-15 00:00:00 -0700
categories: Deep Learning AI
---

Let's explore the paper, [World Models](https://arxiv.org/pdf/1803.10122.pdf), by David Ha and Jürgen Schmidhuber.

The authors distill decades of research from multiple disciplines and subfields into "a simple model inspired by our own cognitive system" using `#DeepLearning`.  It is perhaps not novel in any of its parts or underlying theories, rather their synthesis, bolstered by modern computational capabilities, has produced something elegant, compelling, and viable.

Lessons from previous work include:

* Brains learn abstract representations of information, including spatial and temporal
* Brains predict future sensory perception based on an internal model of the world and our current motor actions
* A successful agent brain has good representations of past and present states and a good predictive model of the future

Their technical approach ibreaks down along similar lines:

* Agent receives an *observation* from the environement
* A vision model (V) encode the high-dimensional observation into a low-dimensional latent vector using a variational autoencoder (VAE)
* A memory (M) uses a recurrent neural network (RNN) to integrate historical *codes* create a representation that can predict future states
* A controller (C) uses both V and M to select the best *action* to take
* The agent performs the actions in the environment

The VAE will compress each frame of a video sequence, for example, into a small latent vector (*z*) representing spatial information.

The memory system records how spatial information changes over time using an RNN with a Mixture Density Network output layer, i.e., a mixture of Gaussian distribution used to sample a prediction of the next observation (*z_t+1*).

The controller is responsible for maximizing the rewards to the agent.  The authors deliberately made their controller a simple linear model that maps *z_t* and *h_t* through a weighted index with bias factor to produce the next action that should be taken.

![from the paper]({{ site.url }}/assets/worldmodels.png)

1. Raw observations are processed by V at each time step *t*, producing *z_t*
2. The controller C takes both the *z_t* from V and *h_t* from M, producing *a_t*
3. M then takes *z_t* and *a_t* to update its current state *h+t+1* for the next time step

Taken together, V and M form a *world model*.

The psuedocode for this was given as:

```python
def rollout(controller):
  ’’’ env, rnn, vae are ’’’
  ’’’ global variables ’’’
  obs = env.reset()
  h = rnn.initial_state()
  done = False
  cumulative_reward = 0
  while not done:
    z = vae.encode(obs)
    a = controller.action([z, h])
    obs, reward, done = env.step(a)
    cumulative_reward += reward
    h = rnn.forward([a, z, h])
    return cumulative_reward
```