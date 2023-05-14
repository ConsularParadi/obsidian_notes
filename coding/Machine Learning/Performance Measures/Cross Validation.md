Allows us to compare different machine learning methods and determine which works well in practice.

> [!note] 
> Divide the dataset into multiple blocks and train & test models for different possible combinations of training and testing blocks
> 

> [!example] 
> **Four-fold cross validation** - 4 blocks of data, 3:1 train-test split,  train for every combination of 3 blocks and test in fourth
> ![[four-fold cross validation.png]]  ![[leave one out.png]]  ![[ten fold.png]]
> Similarly **leave one out validation** (each data point as a block) and **ten-fold**


1. <u>Training the data</u> - Estimate the parameters for machine learning methods.
2. <u>Testing the data</u> - Evaluate how well the model works.

> [!tip] 
> Can be applied to find best tuning parameter value for model

