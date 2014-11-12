---
layout: post
title: Evolving Trading Strategies With Genetic Programming - GP Parameters and Operators
comments: true
tags: [genetic programming, trading]
---
# Part 4
Genetic Programming at its core uses a set of operators (selection, mutation, crossover, elitism etc.) and parameters (number of generations, population size etc.). As there is a [vast literature](http://cswww.essex.ac.uk/staff/poli/gp-field-guide/) on this subject, I will skip the basics and assume that the reader is already familiar with the topic.<!--more-->

The first thing to understand about GP parameters and operators is that they essentially make up a complex system. Changes in one parameter might or might not effect the way other parameters behave. Small changes in one parameter might lead to non-linearly changes in whole the evolutionary outcome. At other time a big change in parametrers might not change the overall result at all. A good example is setting the population size. Beginners exposed freshly to GP often think that the bigger the population size the better. After all, the more individuals in a population, the higher the overall chance a good search result will be found, right? Quite wrong. Often, a small (but not soo small!) population size is actually prefereable to a bigger one. There is a tradeoff between having more individuals in a population and increasing the overall level of noise. Having more individuals per population decreases the chance that a single, relatively fit individual will be able to create offspring. Hence, the level of noise is increased. Some commercially available GP software systems are advertised to be able to process thousends of individuals in a very short time. From my experience, if you need more than, let's say, 500 individuals per population, you are probably doing something wrong. So, let's take a closer look at some parameters and operators.

## Population Size
I already mentioned the population size. Try to start with a small population of maybe 100 individuals. This will also decrease the computing time. As said above, increasing the population size will not necessarily lead to better results due to the increased level of noise.

## Number of generations
If your GP algorithm works, you will observe that in the early stages of the evolutionary process fitter individuals can be found relatively easily, although sometimes a few generations might pass by without any improvement before another big improvement happens. After a number of generations, improvements occur less frequently. Try starting with maybe 10 to 15 generations only. If there is only marginal improvement from the first to the last generation, then probably you are doing something wrong. It is also useful to introduce a _stall generations counter_ and stop the evolutionary process if after a certain number of generations no further improvements was observed.

## Number of decision trees and tree size
I have already written on [how to encode decision trees](post_url 2014-09-03-evolving-trading-strategies-with-genetic-programming-encoding-trading-strategies %}). Once more there is a tradeoff. The more complicated your overall decision rules both concerning tree depth/size and number of subtrees used, the higher the chance of overfitting. Less complex decision rules are nearly always preferable, yet it will probably still make sense to use dedicated decision trees for long and short rules. I will focus on the subject of punishing complexity by means of parsimony pressure in a later article.

## Genetic Programming Algorithm
Although the [basic GP algorithm](http://www.geneticprogramming.com/Tutorial/index.html) is relatively simple, there are various alternatives to it, for example [Linear Genetic Programming](http://en.wikipedia.org/wiki/Linear_genetic_programming), [Cartesian Genetic Programming](http://researchcommons.waikato.ac.nz/bitstream/handle/10289/6838/Cartesian%20genetic%20programming%20for%20trading.pdf?sequence=1), [Non-dominated Sorting Genetic Algorithm (NSGA)](http://www.cleveralgorithms.com/nature-inspired/evolution/nsga.html), [Strength Pareto Evolutionary Algorithm (SPEA)](http://www.cleveralgorithms.com/nature-inspired/evolution/spea.html) and many others. The choice of a particular alternative often has implications on how the individuals are encoded, on the fitness function, and also on the implementation of the selection, mutation and crossover operators. Traditionally, the mutation, crossover and elitism operator are used mutually exclusively and not in combination. That is, one of these operators is selected by an operator selection strategy, and only this operator is then applied. For instance, two children produced as offspring are not additionally subject to mutation, although this would of course be possible.

## Selection
A selection operator is a strategy how to select one or several individuals from a pool for a specific purpose such as mutation or crossover. There are many selection strategies and it really depends on the context which one to use preferably. Every selection strategy relies on a certain criterion to compare individuals. In most cases the best (in terms of fitness) individual is wanted, but sometimes it can also be the worst individual. In case of multi-objective fitness functions, the selection process might become quite complicated. Often, a selection operator must select two individuals and yet ensure not to select the same individual twice. Selecting only the best individuals for reproduction is not wise, because this could lead to a premature convergence to a local instead of global optimum in the search space. A balanced approach is necessary which gives better individuals a higher chance to be selected for reproduction while still continuing to also select weaker individuals. This will keep up a mix between survival pressure and leaving room for new solutions to appear and be explored.  
There are [various selection methods](http://geneticprogramming.us/Selection.html), but probably the most popular selection strategy is [_tournament selection_](http://en.wikipedia.org/wiki/Tournament_selection). Tournament selection is a two-step process. First, a few (e.g. 7) individuals are selected randomly - the "tournament". Second, one or two individuals are selected from the tournament according to their fitness. A larger tournament size gives weaker individuals an overall lower chance to be selected.

## Elitism
Sometimes, it might be a good idea to simply allow the best one or two individuals in a population to be copied over to the next generation, because otherwise they might be lost. The problem with elitism is generally that without further measures taken the later generations might be filled up with many copies of identical individuals. Comparing individuals for equality might however be a computationally expensive operation. I personally prefer not to use elitism for this reason.

## Mutation
Mutation should only occur with a low probability, e.g. in 0% - 2% of cases. Some GP implementations vary the probability of mutations during the evolutionary process. For the different versions of mutation (e.g. point mutation or subtree mutation), consult [one of the many literature sources](http://cswww.essex.ac.uk/staff/poli/gp-field-guide/52GPMutation.html) on the subject. Be aware that mutation must comply with the [typisation of nodes in a decision tree]({% post_url 2014-09-03-evolving-trading-strategies-with-genetic-programming-encoding-trading-strategies %}).

## Crossover
Crossover is the GP operator with the highest probability (e.g. 80% - 100%) for being chosen to produce offspring. Using typed nodes ensures that only valid and meaningful offspring is created by the crossover operator. One problem is that during the evolutionary process, this operator has a tendency to increase the average size of the decision trees and thus lead to code bloat. Often, the fittest individuals are the ones actually overfitting the historical time series. They are also the ones with the biggest decision rules. The selection operator picks them with the highest probability, and therefore the crossover operator subsequently produces offspring increased decision tree size. The proper counter-measure is parsimony pressure, which I will write about in a later post.


----

## Other articles

### Previous
* Part 3: [Evolving Trading Strategies With Genetic Programming - Data]({% post_url 2014-09-22-evolving-trading-strategies-with-genetic-programming-data %})

### Next

----

# References