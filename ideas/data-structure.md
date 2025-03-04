---
title: Ideas - data-structures
date: March 03, 2025
tags: [data-structures]
---

# RB trees

- Properties 

A red-black tree has the following properties that ensure balance:

Each node is either red or black.
The root is always black.
Red nodes cannot have red children (i.e., there are no two consecutive red nodes on any path from the root to a leaf). This ensures that no path can be overly long compared to another.
Every path from a given node to its descendant leaves has the same number of black nodes. This property ensures that the tree is balanced in terms of black-height, which keeps the overall height of the tree logarithmic.

# RB Tree Operations

Red-Black Tree Algorithm
The algorithm for insertion and deletion is slightly more complex than in a regular binary search tree because after each operation, we must ensure that the tree remains balanced according to the Red-Black Tree properties.

-1. Insertion Algorithm
The insertion operation consists of two main steps:

Insert the node as a red node following the usual binary search tree insertion.
Fix the tree to restore the Red-Black Tree properties, which may require recoloring or rotations.
Here’s a step-by-step breakdown of the insertion algorithm:

Step 1: Insertion as in a Binary Search Tree
Insert the new node as a red node following the regular binary search tree (BST) rules. This keeps the tree balanced in terms of ordering.
Step 2: Fix the Tree
After insertion, the tree may violate one or more of the red-black properties, especially property #3 (two consecutive red nodes) or property #4 (black-height imbalance). To fix this, we perform a set of recoloring and rotations (left or right) depending on the case.

Cases for Fixing Insertion
There are four main cases to consider after inserting a node:

Case 1: The uncle of the newly inserted node is red.

In this case, we recolor the parent and the uncle to black and recolor the grandparent to red. Then, we move the newly inserted node up the tree and repeat the fixing process if needed (i.e., move up to the grandparent).
Case 2: The uncle is black and the newly inserted node is a right child.

In this case, we need to rotate left around the parent of the newly inserted node to convert it into a left child.
Case 3: The uncle is black and the newly inserted node is a left child.

In this case, we need to perform a right rotation around the grandparent of the node and swap the colors of the parent and grandparent to restore the properties.
Case 4: The newly inserted node is the root of the tree.

If the node is the root, we color it black to maintain the Red-Black Tree property that the root must always be black.
Step 3: Maintain Properties
After addressing the insertion case, we may need additional adjustments, like rotations or recoloring, to ensure the tree satisfies all red-black properties.

- Deletion Algorithm

The deletion operation is more complicated than insertion. After deleting a node, the tree may violate some red-black properties, especially related to black height. The process involves:

Remove the node (like a standard BST deletion).
Fix the Tree: If necessary, balance the tree by performing recoloring and rotations.
Step 1: Deletion
Perform standard binary search tree deletion. If the node to be deleted has two children, we replace it with its in-order successor or in-order predecessor and delete the successor.
After deletion, the node removed might have affected the black-height property (the number of black nodes from the root to any leaf).
Step 2: Fix the Tree
Post-deletion, the algorithm must restore the red-black properties. The main issues arise from the black-height imbalance caused by removing a black node. In this case, the tree may need to perform rotations and recoloring to restore balance.

Here’s a breakdown of the fix:

Case 1: The sibling of the node is red.

Perform a left rotation or right rotation around the sibling to make it black, and move the red node to the parent's position. After the rotation, recalculate the balance and perform the next case.
Case 2: The sibling is black and the sibling’s children are black.

Recolor the sibling to red and the parent to black. This fixes the black height imbalance but doesn’t necessarily balance the tree. We then check the parent for further fixes.
Case 3: The sibling is black, and one of the sibling’s children is red.

Perform a double rotation (a right rotation followed by a left rotation or vice versa) to fix the tree structure and balance the black heights.
Case 4: The sibling is black, and the sibling’s children are red.

Perform a rotation and recoloring to ensure the tree remains balanced and adheres to red-black properties.
Finally, if the root is ever red after deletions, it is recolored to black.

=======================================================================================================================================================


# AVL trees
-----------

A balanced BST ( Binary Search Trees).An AVL tree (named after its inventors Adelson-Velsky and Landis) is a self-balancing binary search tree (BST), where the difference in heights between the left and right subtrees of any node is at most 1. This balance condition ensures that the tree remains balanced, allowing search, insert, and delete operations to be performed in O(log n) time.

Balance Factor (BF)=Height of Left Subtree−Height of Right Subtree

*Types of Rotations*
--------------------
There are four types of rotations used to maintain the AVL tree's balance:

Left Rotation (LL Rotation): Used when the right subtree is heavier.
Right Rotation (RR Rotation): Used when the left subtree is heavier.
Left-Right Rotation (LR Rotation): A combination of left and right rotations, used when the left subtree is heavy, but the right child of the left subtree is heavier.
Right-Left Rotation (RL Rotation): A combination of right and left rotations, used when the right subtree is heavy, but the left child of the right subtree is heavier.

