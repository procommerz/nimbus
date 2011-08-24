# Nimbus
Random Forest algorithm for genomic selection.

## Random Forest

The [random forest algorithm](http://en.wikipedia.org/wiki/Random_forest) is an classifier consisting in many random decision trees. In machine learning terms, it is an ensembler classifier, so it uses multiple models to obtain better predictive performance than could be obtained from any of the constituent models. The forest outputs the class that is the mean or the mode (in regression problems) or the majority class (in classification problems) of the node's output by individual trees.

## Genomic selection context

Nimbus is a Ruby gem implementing Random Forest in a genomic selection context, meaning every input file is expected to contain genotype and/or fenotype data from a sample of individuals.

Other than the ids of the individuals, Nimbus handle the data as genotype values for [single-nucleotide polymorphisms](http://en.wikipedia.org/wiki/SNPs) (SNPs), so the variables in the classifier must have values of 0, 1 or 2, corresponding with SNPs classes of AA, AB and BB.

you can use nimbus to:

* Create a random forest using a training sample of individuals with fenotype data.
* Use an existent random forest to get predictions for a testing sample.

## Learning algorithm

**Training**: Each tree in the forest is constructed using the following algorithm:

1. Let the number of training cases be N, and the number of variables (SNPs) in the classifier be M.
1. We are told the number mtry of input variables to be used to determine the decision at a node of the tree; m should be much less than M
1. Choose a training set for this tree by choosing n times with replacement from all N available training cases (i.e. take a bootstrap sample). Use the rest of the cases (Out Of Bag sample) to estimate the error of the tree, by predicting their classes.
1. For each node of the tree, randomly choose m SNPs on which to base the decision at that node. Calculate the best split based on these m SNPs in the training set.
1. Each tree is fully grown and not pruned (as may be done in constructing a normal tree classifier).
1. When in a node there is not any SNP split that minimizes the general loss function of the node, or the number of individuals in the node is less than the minimum node size then label the node with the average fenotype value of the individuals in the node.

**Testing**: For prediction a sample is pushed down the tree. It is assigned the label of the training sample in the terminal node it ends up in. This procedure is iterated over all trees in the ensemble, and the average vote of all trees is reported as random forest prediction.

## Install

You need to have Ruby (1.9.2 and higher) and Rubygems installed in your computer. Then install nimbus with:

  $ gem install nimbus
  
## Getting Started

Once you have nimbus installed in your system, you can run the gem using the `nimbus` executable:

  $ nimbus

It will look for these files: 

* `training.data`: If found it will be used to build a random forest
* `testing.data` : If found it will be pushed down the forest to obtain predictions for every individual in the file
* `random_forest.yml`: If found it will be the forest used for the testing.

That way in order to train a forest a training file is needed. And to do the testing you need two files: the testing file and one of the other two: the training OR the random_forest file, because nimbus needs a forest from which obtain the predictions.

## Configuration (config.yml)

The values for the input data files and the forest can be specified in the `config.yml` file that shouldbe locate in the directory where you are running `nimbus`.

The `config.yml` has the following structure and parameters:

  #Input files
  input:
    training: training_regression.data
    testing: testing_regression.data
    forest: my_forest.yml
    
  #Forest parameters
  forest:
    forest_size: 10 #how many trees
    SNP_sample_size_mtry: 60 #mtry
    SNP_total_count: 200
    node_min_size: 5
    
Under the input chapter:

 * `training`: specify the path to the training data file (optional, if specified `nimbus` will create a random forest).
 * `testing`: specify the path to the testing data file (optional, if specified `nimbus` will traverse this dat through a random forest).
 * `forest`: specify the path to a file containing a random forest structure (optional, if there is also testing file, this will be the forest used for the testing).
 
Under the forest chapter:
 
 * forest_size: number of trees for the forest.
 * SNP_sample_size_mtry: size of the random sample of SNPs to be used in every tree node.
 * SNP_total_count: total count of SNPs in the training and/or testing files
 * node_min_size: minimum amount of individuals in a tree node to make a split.
    
    
## Input files

The three input files you can use with Nimbus should have proper format:

**The training file** has any number of rows, each representing data for an individual, with this columns:
1. A column with the fenotype for the individual
1. A column with the ID of the individual
1  M columns (where M = SNP_total_count in `config.yml`) with values 0, 1 or 2, representing the genotype of the individual.

**The training file** has any number of rows, each representing data for an individual, similar to the training file but without the fenotype column:
1. A column with the ID of the individual
1  M columns (where M = SNP_total_count in `config.yml`) with values 0, 1 or 2, representing the genotype of the individual.

**The forest file** contains the structure of a forest in YAML format. It is the output file of a nimbus training run.

## Output files

Nimbus will generate the following output files:

After training:

 * `random_forest.yml`: A file defining the structure of the computed Random Forest. It can be used as input forest file.
 * `generalization_errors.txt`: A file with the generalization error for every tree in the forest.
 * `training_file_predictions.txt`: A file with predictions for every individual from the training file.
 * `snp_importances.txt`: A file with the computed importance for every SNP.

After testing:

 * `testing_file_predictions.txt`: A file defining the structure of the computed Random Forest.

## Resources

* [Source code](http://github.com/xuanxu/nimbus) – Fork the code
* [Issues](http://github.com/xuanxu/nimbus/issues) – Bugs and feature requests
* [RF Leo Breiman page](http://www.stat.berkeley.edu/~breiman/RandomForests/)

## Credits

Nimbus was developed by [Juanjo Bazán](http://twitter.com/xuanxu) in collaboration with Oscar González-Recio.

Copyright © 2011 - 2012 Juanjo Bazán, released under the MIT license