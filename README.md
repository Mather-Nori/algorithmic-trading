# algorithmic-trading

# Contents

_About_ : About this repository, about the author.

_Methods_ : The statistical models I use, the intuition behind them and the context/assumptions of my models.

_Results_ : How some of my statistical models have performed.

_Coming Soon_ : Things that I am working on but are not ready/organised enough to be published.

# About

**_What is this?_**

Here I share some results from my personal research project - _Algorithmic Trading_. My goal here is to design, investigate and test various algorithms I invent for analysing stock market data. My work in this project is mostly based on quantitative data and lacks a qualitative insight. Qualitative data and natural language models are something I am very interested in pursuing in more detail but it is beyond the scope of this current project.

Originally I began this project in MATLAB, however, I've since migrated to Python therefore I have fewer results readily available for the work generated by codebase. Previous methods investigated using MATLAB will not be included here, however, as I add to my code I may find the need to replicate some of my MATLAB code and methods and therefore present it here too. Currently, this repository is just a README file so that I can share my results. I have purposely ommitted my code as there are various passwords and files containing senstive information in the codebase. I'm currently working on removing this sensitive data so that I can share my code.

All visualisations are generated using the maplotlib module in Python and all simulations are done using a simulation framework which I've built from scratch in Python. I am continually working to develop my individual decision models and my simulation framework as a whole.

**_Who am I?_**

My name is Mather Nori and I am currently completing the final year of my MEng degree at University College London (UCL). See my LinkedIn for more details about my career: [\<link\>](https://www.linkedin.com/in/mather-n-21750713b/).

**_Who are you?_**

_Recruiter/Hiring Manager:_ I am happy to answer any specific questions about these results, from high level concepts to specific implementations. I am also interested on hearing your thoughts about my methods and receiving any feedback at all.

**_Notes_**

I will try to keep this file updated as I develop more and more advanced techniques or methods. Hopefully you can come across something you might find interesting.

# Attempt 1: Using Symbol Correlation

**_Introduction_**

In trading terms, two symbols or stocks are _correlated_ if their share price moves in a similar fashion. Consider the two symbols, EURUSD and GBPUSD. Upon gathering some historical time-series data for EURUSD and GPBUSD, I noticed that the two symbols had a very similar pattern in their stock price. Thinking about this intuitively, it makes sense that economic factors affecting the Euro are likely to have a similar effect on the Great British Pound since the United Kingdom is so closely related with Europe. Below, I attach the time series data for EURUSD and GBPUSD visualised using matplotlib:

<p align="center" width="100%">
  <img width="804" alt="EURUSD-GPBUSD" src="https://github.com/Mather-Nori/algorithmic-trading/assets/84272766/500d26c9-ca1d-4910-9378-9ce568e74c8d">
</p>

The above captured data for, approximately, a 90 day period towards the tail end of 2022. To the human eye these two prices are clearly moving together, i.e. they're correlated. In this model we try to exploit this correlation in an attempt to predict the future value of either EURUSD or GBPUSD.

**_Idea_**

I believe that, in order for a trading strategy to work, there **must** be some underlying assumption which will hold true **most** of the time. By the philosophy of Occam's razor, the fewer the assumptions the more likely our solution is to being correct. This is especially true in a financial trading system where there is a high degree of stochasticity and therefore high uncertainties everywhere. In this attempt I work with the following underlying assumption:

Underlying assumption: _If the EURUSD and GBPUSD diverge at some point in time, they will eventually reconverge at some later point in time._

This is a direct observation of the correlation property shown in the figure above. In the assumption the words _diverge_ and _reconverge_ are used. We loosely defined _divergence/convergence_ as the EURUSD/GBPUSD prices becoming further/closer apart than _normal_. We then attempt to define the word _normal_ in a stastistical sense, described below.

In order to have some idea of what _normal_ is, we would need some quantitative metrics to measure the state of the two symbols relative to eachother on average. To this end, we define three variables as follows:

- $\delta P_{eur}$ : the price change of EURUSD since midnight of the current day.
- $\delta P_{gbp}$ : the price change of GBPUSD since midnight of the current day.
- $d = \delta P_{eur} - \delta P_{gbp}$ : the difference in price changes between EURUSD and GBPUSD.

The hope here is that $d$ can serve as the variable which we can use to quantify divergence/convergence of the symbols. When the symbols have diverged/converged a _significant_ amount, this suggests that the two symbols are out of sync. If our fundamental assumption is correct, they will eventually sync up again and therefore the variable $d$ will return to its average value. A natrual next step here is to compute and inspect the distribution and time-series data for $d$. The results are attached below:

<p align="center" width="100%">
  <img width="815" alt="ddiff" src="https://github.com/Mather-Nori/algorithmic-trading/assets/84272766/b358dc17-8e21-4a63-9cf5-f7b12f7a70b7">
</p>

<p align="center" width="100%">
  <img width="779" alt="ddiff-dist" src="https://github.com/Mather-Nori/algorithmic-trading/assets/84272766/c354474b-fed7-4ac0-84bd-7e48ef466f6a">
</p>

The first graph shows the value of $d$ at every point in the time seried, and the second shows a histogram of $d$ across the entire dataset. We can see that the histogram correlated very strongly with a slightly off centred Laplace distribution (https://en.wikipedia.org/wiki/Laplace_distribution). In the first graph, I've drawn the mean and +/- 1 stdev as horizontal dashed lines.

Where the EURUSD and GBPUSD graphs have an overall gradient/trend, the $d$ graph is (almost) zero-mean and has no underlying gradient. Furthermore, we can see an oscillatory behaviour in $d$ as every time it crosses the +/- 1 stdev boundary, it always returns back to the mean. This is quite strong evidence for our underlying assumption above. If we could buy/sell $d$ directly this would be an easy profit since as soon as it crosses the 1 stdev boundary we can short-sell $d$ and re-buy it when it returns to the mean. However, the problem here is that $d$ is dependent upon two random variables, EURUSD and GBPUSD. If we can find a way to buy/sell EURUSD and GBPUSD such that it results in a **net** buy/sell of $d$ we would be able to capitalise on this method.

However, this essentially translates the problem predicting whether a stock/symbol will increase or decrease in price into figuring out which configuration of EURUSD and GBPUSD will increase/decrease in such a way that we get a net profit.

**_Next Steps_**

The next steps here would be to research a way to attack the problem of buying/selling the EURUSD and GBPUSD in such a configuration that when $d$ returns back to the mean, we will be in a net profit. My initial thoughts here are to use approximate the trends lines for EURUSD and GBPUSD and to see if there's a way to use their current trajectories to gain insight into their possible future convergence location. I will update this post with my findings.
