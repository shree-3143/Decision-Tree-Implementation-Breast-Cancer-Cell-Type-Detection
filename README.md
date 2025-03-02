# Decision-Tree-Implementation-Breast-Cancer-Cell-Type-Detection

<h3>Project summary:</h3>

The created application is a cell type identifier for breast cancer. As there are 2 outcomes for the classification of each cell, this is a binary classification task. Therefore, implementing a decision tree classifier is ideal – they are utilised to classify data into 2 different groups, by evaluating whether a logical statement is true or false (Codecademy Team, 2024). Using the cell feature values of each cell, the decision tree model is fit to the training data, and the decision tree is built, by learning the patterns in the feature values that correlate to a particular diagnosis. This decision tree is then implemented on the test data; the output of which is the final prediction of the diagnoses for the cells in the test data. (Castillo, 2021). 


<h3>About the dataset:</h3>

The Breast Cancer Wisconsin Diagnostic Dataset contains the cell-nuclei characteristics, for 569 images, from different patients. Each of these are classified as either Malignant or Benign for cancer. There are 31 properties computed for each cell nucleus in total (Chen et al., 2023). Each row represents each patient, and the columns represent the cell properties.

Link to dataset: 
https://www.kaggle.com/datasets/uciml/breast-cancer-wisconsin-data

A decision tree is a hierarchical structure that comprises of a root node, internal nodes, and leaf nodes, with branches connecting to each of these. An object-oriented system, using Classes and Objects, can be utilised to model such a structure. 

There are 2 main Classes:

- The Node class
- The DecisionTree class

**The Node class:** A decision node is an instance of the node class, which acts as a data structure to store particular information about a split in the tree. The attributes that are stored include:

- The feature the split is based on
- The threshold value for the split
- The left and right datasets obtained from the split
- The information gain from the split (reduction in entropy)
- The value of the node (if the node is a leaf node, and it has a classification value; otherwise, this attribute resorts to None).

**The DecisionTree class**: This class contains all the methods required for the creation of the decision tree, and the method for recursively splitting the dataset until a classification category (leaf node, when there is no further information gain from a split) is reached, once the best split is selected - and the conditions required (feature, threshold value, etc) for the best split is stored in a decision node.

Essentially, there are 2 stopping conditions for the building of a decision tree. When the number of samples at a node is less than 2, or the depth of the decision tree exceeds 2, the splitting of a node is ceased, and all current nodes become leaf nodes, obtaining a classification. Until then, the dataset at a “node” is recursively split (the “build_tree()” method is called on itself) - every time a calculated split increases the information gain than before, it is deemed a worthy split, conditions for which are stored in a Node object, and likewise, the child nodes are split until they become leaf nodes. Leaf nodes are obtained when the splitting of a dataset no longer results in more information gain than before. Information gain is defined as a reduction in entropy - i.e., the target values in the child nodes are comparatively more homogenous than the target values in the parent node, before the split. If there is no new homogeneity gained from the split, there is no information gain, meaning the split is ineffective, and the node is classified as the majority class present (becoming a leaf node).

This project proves that decision trees comprehensively analyse all possible consequences of a decision, before utilising a specific split. The method for deciding the best split at a point in time for a dataset is detailed in the “best_split()” function. Essentially, for a specific dataset, firstly, the independent dataset (feature matrix of the cells) is extracted. All features (columns) are looped through, and for every iteration of the loop (every column in the independent dataset), all values in the column are also looped through, and considered as thresholds for the split. The split is then performed; if the data sample in the dataset is less than the threshold, then it is added to the left dataset. Otherwise, it will be added to the right dataset, and the information gain from the split is calculated. For every threshold (feature value considered as a threshold), if the split from the threshold results in a higher information gain than any of the others considered before, the threshold and feature will be updated in the information that allows the best split. Finally, after the loop is completed, the information used for the best hypothetical split is actually implemented on the dataset, and a decision node instance is formed. 

Finally, the training datasets (independent and dependent) are passed into the “fit()” function, where the decision tree is built according to the features and feature values in the initial dataset. Then, the “predict()” function is called on the testing datasets, to implement and traverse through the decision tree for each sample in the data. The root node is what holds the connection to all the different nodes that have been instantiated, in order for data samples in the original dataset to reach leaf nodes. Therefore, each sample in the data is passed into the “make_prediction()” function, along with the root node (as a parameter) - allowing the split of the root node to occur, and recursively classify each sample in the dataset, until they all reach leaf nodes - which is when the final prediction for each of the data samples can be returned. 

### Problems encountered & interesting points of discussion

After writing up all the code according to the other project, and thoroughly code commenting all the lines according to my understanding, I fit the decision tree to the training data and implemented the “predict()” function on the testing data for the first time. When I ran the code, it took nearly 30 mins to run the first time, and the model obtained an accuracy of only 63%. 

![Screenshot 2024-11-12 at 12.17.15 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/49bd02ca-c5e2-464b-a05d-9b876ffc79e8/4a8dc462-ddcf-4920-850f-d0aafc7821ba/Screenshot_2024-11-12_at_12.17.15_PM.png)

![Screenshot 2024-11-12 at 12.17.02 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/49bd02ca-c5e2-464b-a05d-9b876ffc79e8/d73f7870-05ff-4c20-ac9c-54f1bd822bcb/Screenshot_2024-11-12_at_12.17.02_PM.png)

I found this to be very surprising. 30 mins of running time I thought was pretty diabolical - and my first instinct was to attribute this to the time complexity of the actual algorithm, or the way the algorithm has been written (with an object-oriented approach). Many of the methods in the DecisionTree class were recursive - they continued to split a node of the tree during training or continued to traverse across the tree and classify a data sample, until a leaf node was reached. However, this recursion would only be a problem if the extent to which it occurs is not limited - i.e., the recursive splitting of the node happens way too much, and the model is overfit to the data, possibly causing the appalling accuracy. 

However, as seen below, the depth of the DecisionTree was limited to 2, meaning a root node can only split twice at the maximum. This means that the problem of the bad accuracy and the extremely long running time is not due to the recursion in the algorithm. 

I started stressing about how to make the algorithm more efficient - but I didn’t want to do this, because all the different classes and methods that were written, I understood really well - I didn’t want to change the structure of anything that would make me more confused.

It took me a really long time to find this, but the issue was coming from the way I indexed the arrays in the “best_split()” method - this was due to a typo.

Where `right_y = right_dataset[: , -1]`, I had initially written: ,`right_y = right_dataset[:-1]` and missed the comma. 

This is actually very significant:

- `right_y = right_dataset[: , -1]` selects all the rows from only the last column.
- `right_dataset[:-1]` selects all rows and columns except the last row.

The correct way of indexing is written below:

![Screenshot 2024-11-12 at 1.05.33 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/49bd02ca-c5e2-464b-a05d-9b876ffc79e8/91d59f83-4ef7-456a-9e77-7328b849bb31/Screenshot_2024-11-12_at_1.05.33_PM.png)

As seen below, “right_y” is meant to extract the target values from the right node, after a split; in order to compute the information gain from the entire split (which is the weighted entropy of the child nodes subtracted from the entropy of the parent node). However, before I corrected the typo, the entropy for the right child node was being calculated as a function of the entire dataset being passed in; not just the target values (the diagnoses). Essentially, we should only be passing in the diagnosis column, to count the number of M or B (1 or 0) diagnoses in the dataset at that time, but the entire dataset was being passed, meaning the entropy was being calculated for the entirety of the dataset.

![Screenshot 2024-11-12 at 1.51.23 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/49bd02ca-c5e2-464b-a05d-9b876ffc79e8/3ad71662-5ebe-42b2-a88c-6b2456b930b7/Screenshot_2024-11-12_at_1.51.23_PM.png)

Calculating weighted entropy, as part of the “information_gain()” function:

![Screenshot 2024-11-12 at 2.14.19 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/49bd02ca-c5e2-464b-a05d-9b876ffc79e8/572d1cd7-7735-4ef9-a6c6-049098fe105f/Screenshot_2024-11-12_at_2.14.19_PM.png)

Therefore, the entropy was being calculated for a 2D array, much larger than just 1 specific column, which explains why the running time was so large, and the accuracy was horrendous. Fixing this typo dropped the running time from 30 minutes to only 13 seconds, which was very relieving. 

### Real world applications + ethics

As stated before, the running time of the algorithm was 30 minutes, and the accuracy was also 63%. In a real life situation, if this algorithm (with the error not fixed) was utilised for a patient, the 30 minutes runtime could be enough for the patient to go into a critical condition, without yet being diagnosed. Additionally, it is imperative to address that the use of machine learning for cancer detection means that there are significant stakeholders, for false negative or false positive results. i.e., If a patient is diagnosed with a false positive when they should actually receive a Benign diagnosis, this may mean more resources will be consumed for unnecessary treatment. However, the worst case scenario would be when a patient is diagnosed with a false negative, when they should actually receive a Malignant diagnosis - this could be detrimental to the patient’s health, as the cancer would be ignored, without receiving the required treatment, potentially resulting in eventual death. Therefore, a 63% accuracy in the diagnosis could be disastrous in such a scenario.
