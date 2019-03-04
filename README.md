
# FP-Growth Implementation (Python 3) #
One of the major disadvantages of the Apriori algorithm is the tediousness of having to repeatedly scan the database to check for candidate patterns. The FP-tree (Frequent Pattern Tree) uses a compressed representation of the database. Once an FP-tree has been constructed, it stores all the essential information from the database. Further patterns can be obtained by using recursive divide-and-conquer approach to mine the frequent itemsets from the FP-tree itself.

Frequent Pattern Mining using the FP-tree is very important for the retail industry to increase profits. In this assignment, we are given a *groceries.csv* containing the sales record of a store. An FP-Growth algorithm is to be implemented to find all the frequent itemsets with a threshold >= 300.


## 1. Getting Started ##
This assignment has used recursion to implement FP-tree. It has also used a separate table to link all the nodes with same name together. This table helps in mining the tree in later stages.

### 1.1 Prerequisites ###
I have used **numpy**, **csv** and **collections.OrderedDict** libraries for this implementation. They are usually available directly if you are using Python3 via Anaconda. If not, please make sure these libraries are present before running this code.


## 2. Implementation ##
The database is scanned twice in order to create the FP-tree. A table holding the links of similar nodes is continuously updated with each insertion in the FP-tree. The tree is then mined to find conditional frequent items for each initially frequent item.

### 2.1 Required User Inputs ###
1. **file_name** - This variable contains the name of the file. If the tree is to be tested with any other dataset, the name needs to be changed here.
2. **file_delimiter** - This variable contains the delimiter of the given dataset. For the assignment, the delimiter is set as *','*. If datasets of other delimiters are being used, please update this variable. For example, for tab-separated documents use *'\t'*.
3. **support** - This is the threshold value used in constructing the FP-tree.
4. **output_file_name** - This is the name of the output CSV file which will store all the frequent itemsets above the *support* with in the dataset.

### 2.2 FP-Tree Node Structure ###
A class **fpTreeNode()** defines the structure used for creating the FP-tree. Each node has a *name*, a *frequency* - which is constantly updated, an *OrderedDict of children nodes*, a *parent* node link and a *pointer* to link itself to other nodes with same name. The pointer helps in creating and maintaining the tabel containing the links to similar nodes.

The class also contains a **display_tree_list()** method which is used to display the tree in nested list format.

### 2.3 Data Preprocessing ###
The **fp_tree_preprocess()** function takes the input dataset and scans it once. After removing any null values and empty rows, it creates the frequent item dictionary. Once that is created, it deletes any value in the dictionary which is below the threshold.

### 2.4 Ordering the Transactions based on Frquency ###
The **fp_tree_reorder()** function scans the database for the second time. A couple of things occur simultaneously in this function.

1. A *root* node is created for the FP-Tree.
2. The *item-frequency dictionary* is sorted based on its frequency. If two items have the same fequency, they are sorted alphabetically by their key name.
3. The table to hold the nodes of similar items is constructed and initally all its values are set to *None*.
4. Based on the keys of the sorted *item-frequency dictionary*, items in each transaction are re-ordered. Any item which is not present in the keys are dropped.
5. The ordered transaction is now sent to the FP-Tree for construction of the tree.

### 2.5 Creating and Updating the FP-Tree and Similar-Item Table ###
Two recursive functions **fp_tree_create_and_update()** and **similar_item_table_update()** work simultaneously.

In the **fp_tree_create_and_update()** function, each item in a transaction (transaction = row of the dataset), is added to the FP-tree in the correct order. If the item is not present as a child to its parent node, a new node is created. If it is already added, its frequency is incremented. This process goes on for every item in a transaction. Once the transaction is over, the function receives a new transaction (row) from the previous **fp_tree_reorder()** function.

Every time a new node is created, the recursive **similar_item_table_update()** function is also called within the **fp_tree_create_and_update()** function. As mentioned before, the similar_item table helps in linking all nodes with the same name. Hence, every time a new node is created, the **similar_item_table_update()** function traverses through the table to find the last node with the same name and links the new node with the previous node.

### 2.6 Creating Conditional Leaf Base ###
To find the frequent item patterns in the using FP-Tree growth, a conditional FP-Tree has to be generated for each frequently occuring item. This is done with the help of the Similar-Item table created before. For every item occuring in that table, a conditional FP-Tree is created, and itemsets below the threshold are, once again, removed.
The **create_leaf_cond_base()** function does the following:

1. Traverses through every node in a particular 'key' in the Similar-Item Table. Within each node, it traverses till the root of the FP-Tree and maintains logs of every node-name it traversed through to get to the root.
2. Once a condition-base is formed by all the nodes of a particular 'key', a frequent-itemset table is generated for the conditional leaf.
3. Items below given support/threshold is removed from the table and also from the conditional-base.
4. All other items are appended to the CSV file as frequent itemsets. (End of Question 2A)

NOTE: For this implementation, the first item in each row in the CSV file refers to the leaf on which it was conditioned on.

For example, ['rolls/buns', 'root vegetables'] means that this item-set was found while conditioning for 'rolls/buns'.

For question 2B, conditional trees are created from their frequent item-sets found in 2A. This is easy as the list is appended so that the conditional leaf occurs first (as mentioned above). Conditonal trees are created by recursively calling the **conditional_fptree()** function. This function works exactly like the **fp_tree_create_and_update()** function except that it does not internally call the Similar-Item Table anymore. Once all the conditional FP-Trees are created, the ones with height>1 are printed out as the result for Question 2B.

## 3. Authors ##
BANERJEE, Rohini - HKUST Student ID: 20543577

## 4. References ##
1. “Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.

