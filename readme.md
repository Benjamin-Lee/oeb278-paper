---
title: "Long-term bacterial adaptation to a constant environment as a model for genetic algorithm design"
author:
  - Benjamin D. Lee:
      institute:
        - Lab41
        - HMS
        - SEAS
      email: benjamindlee@me.com
      orcid: 0000-0002-7133-8397
      correspondence: "yes"
institute:
  - Lab41:
      name: "In-Q-Tel Lab41"
      address: "800 El Camino Real, Suite 300, Menlo Park, CA 94025, USA"
  - HMS:
      name: "Harvard Medical School"
      address: "277 Avenue Louis Pasteur, Boston, MA 02115, USA"
  - SEAS:
      name: "School of Engineering and Applied Sciences, Harvard University"
      address: "29 Oxford St., Cambridge, MA 02138, USA"
bibliography: paper.bib
---

# Abstract

Evolutionary algorithms are a class of computational techniques for problem solving based on Darwinian evolution. These methods are centered on increasing the "fitness" of a population of candidate problem solutions through successive generations exposed to the forces of selection, mutation, and recombination until a solution is found. Using experimental evolutionary results from bacterial populations adapting to a constant environment, a novel genetic algorithm experimental model was tested, yielding an 18% reduction in the amount of computation required to converge on an optimal solution for a baseline problem. These results support the applicability of experimental evolution to evolutionary algorithm research and warrant further investigation.

# Introduction

Algorithm development is one of the central research areas of computer science. One frequent source of inspiration for algorithm design is biology in general and evolution in specific. Indeed, some computationally intractable problems can be approximately solved using evolutionarily inspired methods, broadly termed evolutionary algorithms. 

One specific and widely used evolutionary algorithm method is genetic algorithms [@hollandAdaptationNaturalArtificial1992a]. To imitate evolution, genetic algorithms attempt to solve problems by increasing the fitness of (_i.e._ adapting) a population of breeding individuals, subject to the effects of mutation, recombination, and directional selection, over multiple generations. A genetic algorithm is centered on the optimization (minimization or maximization) of a real-valued fitness function $f$, by which an individual in the population is evaluated. An individual is defined by their "genome", which a list of "genes" (_i.e._ numbers), that can be fed into $f$ to yield another number which represents the individual's fitness. After the creation of the initial population (usually with random genomes), each individual is evaluated with respect to $f$. Then, a selection function is applied over the population to determine which individuals to use as the basis of the next generation. One common selection method is called *tournament selection*, in which random subsetss of the population are chosen, with the fittest member of the subset being propogated. This allows for fit, albeit not the fittest, genes to persist in the population. Often, _elitism_ is used, which ensures that the fittest individual in the population is persevered identically into the next generation, thereby guaranteeing that the fitness function is monotonically increasing between generations. After the selection of individuals, a recombination function can be applied to generate members of the next generation, which takes two parents and yields two children. Additionally, a mutation function can be applied to individuals to introduce further stochasticity. This process is repeated until a stopping criterion is met. The fittest member of the final population is taken as the final result of the optimization.

Even in its basic formulation, there are a variety of parameters left to the genetic algorithm's user, such as the appropriate fitness function, population size, genetic representation, population size, selection function, mutation function and rate, recombination function and rate, and stopping criterion. Furthermore, due to the inherent randomness of the initial population, multiple populations must often be evolved independently to yield an acceptable solution. Thus, a genetic algorithm optimization experiment often consists of multiple randomly initialized populations evolving in parallel, thus increasing the chances of finding an acceptable solution.

Unfortunately, genetic algorithms are not reflective of the biology _in natura_. Biological systems generally do not evolve in a controlled, unchanging environment. Furthermore, populations' genomes are not completely random, but rather highly interrelated and conserved. Indeed, this discrepancy has been one of the major areas of criticism for genetic algorithms. For example, @skienaAlgorithmDesignManual2008 described the use of genetic algorithms as "pseudobiology" and "voodoo". Additionally, the often-large number of generations required to converge on an solution means that numerous invocations of the fitness function are required. If the fitness function is costly to compute, genetic algorithms can be prohibitively slow to compute.

However, there have been experiments experiments which very closely mirror that of genetic algorithms, namely the long-term evolution experiment (LTEE) and its offshoots [@lenskiDynamicsAdaptationDiversification1994]. In the LTEE, twelve identical populations of asexual _Escherichia coli_ have been allowed to evolve in parallel for tens of thousands of generations in a medium containing limited nutrients to study bacterial adaptation to a constant environment. Each day, 1% of the population are moved to a flask containing replenished albeit equally limited nutrients. Throughout the course of the experiment, viable samples have been frozen at 500 generation intervals, allowing biological "time travel" [@goodDynamicsMolecularEvolution2017]. One population of _E. coli_ evolved the ability to metabolize citrate, a nutrient whose nonmetabolizability is a hallmark of the species, thereby dramatically increasing its fitness. While a full overview of the experiment is beyond the scope of this paper, one key insight from the LTEE is that identical starting populations can, without recombination, yield populations with significantly diverging fitnesses. In addition, the fitnesses of the populations have continued improving without bound for 50,000 generations [@wiserLongTermDynamicsAdaptation2013], seeming to follow a power-law relation. 

The implications of this insight are twofold with respect to genetic algorithms. First, the ability to yield different results from the same starting population means that repeating the evolution process on the same population should be investigated, in contrast to the standard genetic algorithm approach of using numerous completely randomized initial populations. Second, by caching the fitness of each individual evaluated, the optimization of costly fitness functions can potentially be improved by reducing the amount duplicated computation.

# Methods

As an experimental test of the applicability of the LTEE results to evolutionary computation, we will use the counting ones problem to examine the behavior of genetic algorithms. The counting ones problem is a model optimization problem in which the goal is to create a genome consisting entirely of 1s. The fitness function is formally defined as $$f(\mathbf{x}) \rightarrow \mathbb{Z}^{*}:\sum_{i=1}^{n} \mathbf{x}_{i}$$ where $\mathbf{x}$ is a genome of size $n$. The mutation function is defined as flipping a random bit in $\mathbf{x}$ and the stopping criterion is when $f(\mathbf{x})=n$. Genomes are initialized at random. This optimization problem is widely used in genetic algorithm research because the optimal solution is known and the fitness function is trivially implementable. 

Three experimental groups were tested, each of which contained twenty populations of one hundred individuals with one hundred genes ($n=100$). That multiple populations were used was intended to be reflective of a real-world genetic algorithm use case. The first experimental group was a baseline of traditional genetic algorithms in which twenty independent randomly initialized population of size 100 were optimized ("control"). The second was a set of twenty initially identical populations of size 100, each of which evolved independently ("LTEE"). The third group was an intermediate of the first two, and consisted of four different subgroups of five identical populations, each of size 100. Each group was allowed to evolve until the optimal solution was found ("LTEE-subpopulations"). Four mutation rates (0.01, 0.02, 0.03, and 0.04) were tested, as these have been shown to have the fastest convergence to the global optimum when $n=100$ [@thierensAdaptiveMutationRate2002]. To characterize the effect of caching costly fitness functions on the different groups, the number of unique individuals in each group was counted. The experiment was repeated twenty times for each mutation rate and experimental group.

To analyze the results, a one way ANOVA was carried out to determine whether the groups had the same mean fitness function invocation count and unique fitness function invocation count. Then, a Tukey HSD test [@tukeyComparingIndividualMeans1949] was carried out with a threshold of $p<0.05$ to identify whether mean unique fitness function invocation count differed between pairs of experimental groups.

The experiments were carried out using the `pyeasyga` genetic algorithm software library version 0.3.0 (https://github.com/remiomosowon/pyeasyga), and Python 3.7.3. The data were analyzed using the `pandas` data analysis library version 0.24.2 (https://github.com/pandas-dev/pandas), the `scipy` scientific computing library version 1.2.1 (https://www.scipy.org), the `numpy` scientific computing library version 1.16.2 (https://www.numpy.org), and the `statsmodels` statistical analysis library version 0.9.0. The full experimental and analytical code is included in the appendix.

# Results

The LTEE-based models both performed comparably to the baseline in terms of mean fitness function invocation count required for convergence to the optimum while requiring significantly fewer unique fitness function invocations on the counting ones problem. Overall, the control required a mean $587977.5 \pm 270040​$ total fitness function invocations to converge, in comparison to $576458.75\pm258589.8​$ invocations for the LTEE group, and $581145\pm255496.1​$ invocations for the LTEE-subpopulations group, which was not statistically significantly different. However, the mean unique function invocation count differed significantly between the groups, with the control requiring $11358.9\pm1119.1​$ invocations to converge, in comparison to $9255.5\pm1180.5​$ and  $9835.5\pm1195.1​$) invocations for the LTEE and LTEE-subpopulation groups, respectively. This corresponds to a decrease of 18.5% and 13.4% unique function invocations for the LTEE and LTEE-subpopulation groups, respectively. A more detailed breakdown of the mean fitness function invocation count and the mean unique fitness function invocation count by mutation rate can be seen in the table below.

| Group                                     | Mean fitness function invocation count (std) | Mean unique fitness function invocation count (std) |
| ----------------------------------------- | -------------------------------------------- | --------------------------------------------------- |
| Control, mutation rate = 0.01             | 1024180 (50925)                              | 9850 (244)                                          |
| mutation rate = 0.02                      | 577885 (32931)                               | 10959 (228)                                         |
| mutation rate = 0.03                      | 413755 (14768)                               | 11826 (211)                                         |
| mutation rate = 0.04                      | 336090 (13158)                               | 12799 (272)                                         |
| LTEE, mutation rate = 0.01                | 994680 (49232)                               | 7680 (314)                                          |
| mutation rate = 0.02                      | 565600 (26572)                               | 8855 (330)                                          |
| mutation rate = 0.03                      | 407780 (23970)                               | 9763 (367)                                          |
| mutation rate = 0.04                      | 337775 (13086)                               | 10722 (363)                                         |
| LTEE-subpopulations, mutation rate = 0.01 | 991435 (52354)                               | 8210 (269)                                          |
| mutation rate = 0.02                      | 576615 (29386)                               | 9385 (228)                                          |
| mutation rate = 0.03                      | 419345 (15192)                               | 10426 (215)                                         |
| mutation rate = 0.04                      | 337185 (12625)                               | 11319 (299)                                         |

The one-way ANOVA analysis yielded an $F$ static of 0.039 and a $p$ value of 0.961 for comparison between the total function invocations between the groups, failing to reject the null hypothesis that the means of the groups differ. However,  a one-way ANOVA analysis of the unique function invocation count yielded an $F$ static of 69.525 and a $p$ value < .0005, rejecting the null hypothesis that the means of the groups differ. The Tukey HSD pairwise comparison of the unique function invocation count yielded significant ($p<0.05$) differences between all pairs.

# Discussion

The results show that the LTEE-based models are able to match the baseline performance in terms of mean number of generations required to converge while requiring significantly fewer unique fitness function invocations. This is an important consideration because, as the cost of calculating the fitness function increases, the importance of reducing the number of unique fitness function invocations grows faster than the importance of the number of generations required for convergence. This is due to the fact that fetching cached genome fitnesses requires significantly fewer computational resources ($O(1)$ computational complexity) than calculating them.

Furthermore, this experiment demonstrates the applicability of long-term bacterial evolution experimental results to that of genetic algorithm research. One area for future investigation is dynamic population sizes such that, as the fitness of the population increases, so does its population. One potential way to increase the population is to include clones of individuals in proportion to the individuals' fitnesses. In addition, the effects of random selection as compared to tournament selection would also be subject to reinvestigation, as random selection would select fit individuals at a greater rate by virtue of their overabundance in the population.

One limitation of this study is that it eliminated recombination, which is often used to speed up convergence. In the long-term bacterial evolution experiments, recombination has been shown to be able overwhelm selection [@maddamsettiAnalysisBacterialGenomes]. This does not generally appear to be the case in genetic algorithm populations, although further investigation into the effect of recombination is warranted, particularly to what types of fitness function, if any, recombination damages the performance of genetic algorithms on.

The mutation rate is of particular biological interest since, unlike the fitness function, there is a direct parallel to actual an actual, measurable biological metric. There are a variety of open research questions about genetic algorithm mutation rates, such as what the optimal mutation rate for a genetic algorithm optimization problem is [@thierensAdaptiveMutationRate2002]. Furthermore, should this rate vary throughout the course of the experiment? If so, how? The answers to these questions could potentially be elucidated by examining the LTEE. In it, some populations became hypermutators [@goodDynamicsMolecularEvolution2017]. One area for experimentation could be to use multiple (and adaptive) mutation rates in different populations when conducting a genetic algorithm optimization experiment.

As the LTEE progresses and more results are generated, more inspiration for genetic algorithm development may be created, allowing for further refinement to this valuable model of computation. 

# Conclusions

This paper introduces two key findings for evolutionary algorithm research. First, it connects the computational model of evolution used in genetic algorithms to long-term bacterial evolution experiments, which it mirrors more closely than evolution in natural environments. This development provides a clear body of experimental data from which to identify sources of inspiration that may be more readily translated into computational advances. Second, using the long-term bacterial evolution experiment model as a basis for computational experimentation, a novel method for reduction in the required number of unique fitness function invocations was identified and validated using the counting ones problem.

The project's software repository and raw data may be viewed at https://github.com/Benjamin-Lee/oeb278-paper. 

# Appendix

Experimental code:

```python
#!/usr/bin/env python
# coding: utf-8

# In[6]:


from pyeasyga.pyeasyga import GeneticAlgorithm
import random

genome_size = 100
population_size = 100

def create_individual(data):
    return [random.randint(0, 1) for _ in range(genome_size)]


# In[66]:


def basic_population(mutation_probability):
    results = []
    cached = set()
   
    def fitness (individual, data):
        if tuple(individual) not in cached:
            cached.add(tuple(individual))

        return sum(individual)

    for _ in range(20):
        ga = GeneticAlgorithm(create_individual(None), 
                              crossover_probability=0, 
                              population_size=population_size,
                              mutation_probability=mutation_probability)
        ga.fitness_function = fitness
        ga.create_individual = create_individual

        generations = 1
        ga.create_first_generation()

        while ga.best_individual()[0] < genome_size:
            generations += 1
            ga.create_next_generation()
            
        results.append(generations)
    return results, len(cached), sum(results)*population_size


# In[68]:


def ltee_population(mutation_probability):
    results = []
    cached = set()
   
    def fitness (individual, data):
        if tuple(individual) not in cached:
            cached.add(tuple(individual))

        return sum(individual)
    
    ga = GeneticAlgorithm(create_individual(None), 
                              crossover_probability=0, 
                              population_size=population_size,
                              mutation_probability=mutation_probability)
    ga.fitness_function = fitness
    ga.create_individual = create_individual


    ga.create_first_generation()
    
    starting_population = ga.current_generation
    for _ in range(20):
        ga = GeneticAlgorithm(create_individual(None), 
                              crossover_probability=0, 
                              population_size=population_size,
                              mutation_probability=mutation_probability)
        ga.fitness_function = fitness
        ga.create_individual = create_individual

        generations = 1
        ga.current_generation = starting_population

        while ga.best_individual()[0] < genome_size:
            generations += 1
            ga.create_next_generation()
            
        results.append(generations)
    return results, len(cached), sum(results)*population_size


# In[71]:


def ltee_subpopulations(mutation_probability):
    results = []
    cached = set()
   
    def fitness (individual, data):
        if tuple(individual) not in cached:
            cached.add(tuple(individual))

        return sum(individual)
    
    for i in range(5):
    
        ga = GeneticAlgorithm(create_individual(None), 
                                  crossover_probability=0, 
                                  population_size=population_size,
                                  mutation_probability=mutation_probability)
        ga.fitness_function = fitness
        ga.create_individual = create_individual


        ga.create_first_generation()

        starting_population = ga.current_generation
        for _ in range(4):
            ga = GeneticAlgorithm(create_individual(None), 
                                  crossover_probability=0, 
                                  population_size=population_size,
                                  mutation_probability=mutation_probability)
            ga.fitness_function = fitness
            ga.create_individual = create_individual

            generations = 1
            ga.current_generation = starting_population

            while ga.best_individual()[0] < genome_size:
                generations += 1
                ga.create_next_generation()

            results.append(generations)
    return results, len(cached), sum(results)*population_size


# In[109]:


results = []
trials = 20

methods = [basic_population, ltee_population, ltee_subpopulations]
mutation_rates = [0.01, 0.02, 0.03, 0.04]

for mutation_rate in mutation_rates:
    print(f"Mutation rate {mutation_rates.index(mutation_rate) + 1}/{len(mutation_rates)}")
    for i in range(trials):
        print(f"\tTrial {i+1}/{trials}")
        for method in methods:
            print(f"\t\tMethod {methods.index(method) + 1}/{len(methods)}")
            result = method(mutation_rate)
            results.append(dict(method=method.__name__,
                                mutation_rate=mutation_rate,
                                generations=result[0],
                                unique_individuals=result[1],
                                total_fitness_invocations=result[2]))


# In[110]:


import pandas as pd


# In[111]:


df = pd.DataFrame.from_dict(results)


# In[112]:


df.to_csv("results.csv")
```

Analysis code:

```python
# In[140]:


df = pd.DataFrame.from_csv("results.csv")


# In[141]:


import ast


# In[142]:


df.generations = df.generations.apply(ast.literal_eval)


# In[114]:


from scipy.stats import f_oneway


# In[268]:


print(f_oneway(df.loc[df.method == "basic_population"].total_fitness_invocations,
         df.loc[df.method == "ltee_population"].total_fitness_invocations,
         df.loc[df.method == "ltee_subpopulations"].total_fitness_invocations))


# In[267]:


print(f_oneway(df.loc[df.method == "basic_population"].unique_individuals,
         df.loc[df.method == "ltee_population"].unique_individuals,
         df.loc[df.method == "ltee_subpopulations"].unique_individuals))


# In[269]:


from statsmodels.stats.multicomp import MultiComparison
print(MultiComparison(df.unique_individuals.to_numpy(), df.method.to_numpy()).tukeyhsd())
```



# References







