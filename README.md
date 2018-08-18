# Fuzzy Cognitive Maps

# Parallelism in Fuzzy Cognitive Maps

When simulating Fuzzy Cognitive Maps (FCMs), there are a variety of ways to implement parallelism. We are going to look at a few of the areas that we can parallelize to enhance the speed of simulating, and some cases of parallelization in learning methods. First, we will look at a few areas in the simulation phase that can be parallelized and a couple different ways to run them in parallel. Second, we will look at running genetic and learning algorithms in parallel for some cases that have been found. 
During the simulation step there are not many parts that can be parallelized. Though they are few, they are significant as they are the parts with the highest time and computational complexity. The first of these we will look at is the simultaneous updating of the concept values. 
Concept values are updated by multiplying the edge matrix by the column vector that holds the concept values at that time step. Matrix multiplication can get costly very quickly with size and makes sense to parallelize. One such way to do this is to call a function that is set to run in parallel called a remote function. They will execute on one or more engines instead of locally. There are two ways to makes remote functions and there is no difference in execution between them. Both are placed before the def line. You can either,

@dview.remote(block=True)    

or

@dview.parallel(block=True)

The @parallel method however breaks up the element wise operations and distributes them to engines to later reconstruct the result. Of these to options for easy parallelism it would be preferable to use the @parallel decorator.  
	Second, it is possible to parallelize applying the transfer function. To do this the easiest way is to apply the parallel map function. This function excels at taking elements one at a time. However, since the parallel map is a similar decoration to remote functions, it acts in a similar way as a lambda function would except for it run on multiple engines. 
	Of course these are all the easy made ways to generate parallelism. There is of course the method of manually implementing parallelism. To do this you simply use the apply method. Using the id’s with apply we can run different actions in parallel. However, The decorators discussed earlier are built on the apply method and thus can be used instead of doing the work manually.

On top of being able to parallelize during the simulation phase, parallelization is also applicable to run during learning algorithms. Since these algorithms aim to optimize large systems, the problem of scalability and time complexity can become an issue. In the case of real-coded genetic algorithm (RCGA), Wojciech Stach [1] presented an approach for parallelizing this algorithm to minimize the computational time. 
In the process of running a genetic algorithm (GA), a fitness function is used in order to calculate a fitness value to see how good a solution is for the problem the GA is optimizing. Seeing as the fitness function is usually most expensive part of a GA, the parallel approach (used in the referenced paper) is based on assessing this function over multiple processors. This is defined as the global single-population master slave approach. The master evaluates the population of the matrix and the slaves/processors evaluate the fitness function to obtain the fitness values. Once the fitness values are computed, they are returned from the processors to the master. This parallelization method not only expedites the overall computational time, but it additionally allows for the scalability of large maps to be more efficiently implemented and analyzed.
Another parallel architecture can be applied to FCMs; this method coordinates multiple FCMs into a single FCM and allows for simulation of each individual model as well as the overarching model [2]. The purpose of this process allows for a deeper analysis of complex dynamic systems by being able to observe every FCM in the model as well as the system as a whole. Furthermore, implementation of this parallel FCM would require less work on the individual processor and allow for a simulated schedule to be executed.
This approach is based around the premise of a coordinator module. This module not only arranges the FCMs, but it determines the stopping criteria for each FCM. The termination criteria is needed because each FCM is processed on a schedule. They are responsible for fulfilling a task at a given iteration, and are forced to wait on other FCMs’ tasks if they are not yet completed. This means the coordinator module must check to see when each task is scheduled and stop individual simulations based on the amount of work needed in the overall model. As opposed to the problem-based genetic algorithm example (where communication only took place to return the fitness value to the population), this method is dependent on the schedule and must communicate which tasks are completed.

References:

[1] W. Stach. Parallel Genetic Learning of Fuzzy Cognitive Maps, Final Report for IEEE-CIS Walter Karplus Summer Research Grant, 2006
[2] W. Stach, L. Kurgan, W. Pedrycz, M. Reformat, Parallel fuzzy cognitive maps as a tool for modeling software development project, Proc. 2004 North American Fuzzy Information Processing Society Conf. (NAFIPS’04), Banff, AB, 2004, pp. 28–33
