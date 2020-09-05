## GHSOM, somer than SOM

A self-organizing map (SOM) is a neural network that produces a discreet 2D representation of higher dimension input spaces through unsupervised competitive learning. Once trained, apart from being in itself a data visualization tool, the network will be able to map new input vectors to a unit and classify them if the training data was labeled.

While a SOM has fixed predetermined size, a [growing hierarchical SOM][2] (GHSOM) will start with a single map of minimal initial size (2x2) and then spread (by inserting new rows or columns) or deepen (by attaching new maps to units) where units exhibit higher quantization errors. The algorithm works recursively as the child maps themselves may spread or deepen as needed, and the resulting network will be a tree of 2D SOM of different sizes. A GHSOM is capable of auto-adapting its size to the input data, giving more resolution to some parts of the network and potentially revealing the hierarchical structure of the input space when applicable.

[1]: http://www.ifs.tuwien.ac.at/~andi/ghsom/

## Parameters

The number of iterations *λ* corresponds to how many times the whole training set will be fed into a map before computing its error and possibly expanding it. If the map spreads, the training process restarts (but the model weight vectors keep their values) and another full training pass is performed. Otherwise, it proceeds to the deepening stage.

As in a regular SOM, the learning rate *α* defines how strongly unit weights will be corrected towards the weights of input vectors that matches them. A gaussian neighborhood function is applied: all units around the winner unit are corrected with a learning coefficient decreasing with distance, following a gaussian curve. The learning rate itself is linearly decreasing within each learning pass, meaning that the neighborhood shrinks with time. It is the neighborhood function that gives the map its topology by gathering similar units together.

The spread threshold *τ1* determines until when a map should spread, in relation with the quantization error of its units. The quantization error of a unit is the cumulated difference between its weight vector and the weight vectors of all the training items mapped to this unit. A map will keep spreading until the mean quantization error of its units does not exceed a percentage of the error of the parent unit to which it is attached, and this percentage is represented by the spreading threshold.

Similarly, a map will create and attach child maps to each of its units that has a quantization error higher than a percentage - the depth threshold *τ2* - of the error of the virtual root unit to which belongs the uppermost map. The weights of this root unit are actually set to the average vector of the training set.

In order to preserve the usefulness of the resulting topography as a visualization tool, child maps have to be oriented so as to have their border units match the neighbors of the parent unit. This is done by initializing the model weights in the child map not with random values, but rather center on the mean parent weight plus the average deviation of the relevant neighbors for each of the units in the 2x2 child map.

## Results

Here is a typical output for a run on the [Iris dataset][2] with parameters *τ1* = 0.05 and *τ2* = 0.01, with a treemap-like visualization:

![](https://raw.githubusercontent.com/olvb/somr/master/samples/iris.png)

[2]: https://archive.ics.uci.edu/ml/datasets/iris

*Red = setosa, Green = versicolor, Yellow = virginica*

TODO: Test on a dataset actually actually having a hierarchical structure... One application could be audio categorization (music genre, sound banks, etc), such as described [here][3].

[3]: http://www.ifs.tuwien.ac.at/~andi/somejb/

