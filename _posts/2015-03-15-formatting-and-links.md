---
layout: post
title:  How to use DIGNEApy to generate Knapsack Problem Instances
date:   2024-05-15
description: A quick example to understand the tool
tags: instance-generation code python optimisation
categories: quick-samples
---
DIGNEApy is based on the C++ software DIGNEA published in SoftwareX. The software allows researchers to generated sets of diverse and discriminatory instances for optimisation domains based on the performance of a portfolio of solvers. The diverse behaviour of the instances is described by a predefined set of features extracted from the instance information while the discrimininatory behaviour is calculated from the performance of a portfolio of solvers. Within the portfolio, a solver is labeled as the *target* solver, meaning that the set of instances produced by DIGNEA will be biased to the performance of such solver. That means, the performance of the target solver over the resulting instances is better than the performance of the remaining solvers in the portfolio.
In this quick example I will show you how to use the more accesible python version of DIGNEA to create set of diverse and discriminatory instances for a portfolio of Knapsack deterministic heuristics. We will be generating Knapsack instances of N = 50 items for each of the four heuristics in the portfolio.

## Generator configuration

It is important to note that the first algorithm in the portfolio will be considered the target. Therefore, we use the method **rotate** to switch the algorithm in the first position of the deque.

```python

    portfolio = deque([default_kp, map_kp, miw_kp, mpw_kp])
    kp_domain = KPDomain(dimension=dimension, capacity_approach=capacity_approach)

    for i in range(len(portfolio)):
        portfolio.rotate(i) 
        eig = EIG(
            population_size,
            generations,
            domain=kp_domain,
            portfolio=portfolio,
            t_a=t_a,
            t_ss=t_ss,
            k=k,
            repetitions=1,
            descriptor=descriptor,
            replacement=first_improve_replacement,
        )
    
```


## Results

The method returns two set of instances. First, and archive of instances used by the generator to drive the evolution and the resulting set known as **solution set** which contains the actual results of the method.

```python
print(eig)
        archive, solution_set = eig()

        print(f"The archive contains {len(archive)} instances.")
        print(f"The solution set contains {len(solution_set)} instances.")
        print("=" * 80 + " Solution Set Solutions " + "=" * 80)
        for i, instance in enumerate(eig.solution_set):
            filename = f"kp_instances_for_{portfolio[0].__name__}_{i}.kp"
            kp_domain.from_instance(instance).to_file(filename)
```


#### Full Code Example

```python

from digneapy.generator import EIG
from digneapy.solvers.heuristics import default_kp, map_kp, miw_kp, mpw_kp
from digneapy.domains.knapsack import KPDomain
from digneapy.operators.replacement import first_improve_replacement
from collections import deque
import configparser
import argparse


def main(default_args):
    if default_args:
        dimension = 50
        capacity_approach = "percentage"
        population_size = 10
        generations = 1000
        t_a, t_ss, k = 3, 3, 3
        descriptor = "features"
    else:
        config = configparser.ConfigParser()
        config.read("knapsack_experiment.ini")
        # Reading the parameters
        dimension = int(config["domain"]["dimension"])
        capacity_approach = config["domain"]["capacity"]

        population_size = int(config["generator"]["population_size"])
        generations = int(config["generator"]["generations"])
        t_a = float(config["generator"]["t_a"])
        t_ss = float(config["generator"]["t_ss"])
        k = int(config["generator"]["k"])
        descriptor = config["generator"]["descriptor"]

    portfolio = deque([default_kp, map_kp, miw_kp, mpw_kp])
    kp_domain = KPDomain(dimension=dimension, capacity_approach=capacity_approach)

    for i in range(len(portfolio)):
        portfolio.rotate(i)
        eig = EIG(
            population_size,
            generations,
            domain=kp_domain,
            portfolio=portfolio,
            t_a=t_a,
            t_ss=t_ss,
            k=k,
            repetitions=1,
            descriptor=descriptor,
            replacement=first_improve_replacement,
        )
        print(eig)
        archive, solution_set = eig()

        print(f"The archive contains {len(archive)} instances.")
        print(f"The solution set contains {len(solution_set)} instances.")
        print("=" * 80 + " Solution Set Solutions " + "=" * 80)
        for i, instance in enumerate(eig.solution_set):
            filename = f"kp_instances_for_{portfolio[0].__name__}_{i}.kp"
            kp_domain.from_instance(instance).to_file(filename)


if __name__ == "__main__":
    parser = argparse.ArgumentParser(
        prog="Instance Generation for Knapsack Domain",
    )
    parser.add_argument(
        "-d",
        "--default",
        action="store_true",
        help="Using the default parameters for the experiment. Otherwise it uses the .ini file",
    )
    args = parser.parse_args()
    main(args.default)

```

<!-- <blockquote>
    We do not grow absolutely, chronologically. We grow sometimes in one dimension, and not in another, unevenly. We grow partially. We are relative. We are mature in one realm, childish in another.
    —Anais Nin
</blockquote> -->

