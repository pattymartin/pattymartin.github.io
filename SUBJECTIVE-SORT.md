layout: page
title: "Subjective Sort"
permalink: /subjective-sort/

# Subjective Sort

[Subjective Sort](https://github.com/pattymartin/subjective-sort) is a project
that sorts images based on user input. Images are displayed two at a time so
that the user can select which one they prefer. This repeats until the list of
images is fully sorted.

## Sorting

Since comparisons are dependent on the user, they are the most costly part of
the task and should therefore be kept to a minimum.

The algorithm I chose to use for this project uses a binary tree to sort
elements; each element is inserted into the tree, and then the tree is
traversed to obtain the sorted list.

When an item is inserted into the tree, it must be compared to successive nodes
until a leaf node is reached. In order to minimize the number of comparisons,
an optimal algorithm will minimize the distance from the root to any given leaf
node. In other words, a balanced tree with a very low height is best suited to
this task.

I chose to use an [AVL tree](https://en.wikipedia.org/wiki/AVL_tree) due to its
strict balance, but I modified the insertion algorithm such that the tree is
even more strictly balanced than a standard AVL tree.

<img align="right" width="50%" src="https://i.imgur.com/1bjsZNf.png">

Tree A on the right, for example, is a valid AVL tree, but is not as balanced
as Tree B. My version of the algorithm would rebalance a tree such as Tree A to
resemble Tree B by removing one node from the "heavy" side of the tree, and
inserting one node into the opposite side of the tree. Thus, the rule for
rebalancing a node is as follows:

1. Balance according to AVL rules.
2. If the root node's *minimum* height and *maximum* height differ by more than
   one:
   1. Remove the node from the "heavy" subtree with the value nearest to the
      root (i.e. the largest from the left subtree, or smallest from the
	  right).
   2. Set the root node's value to the value of the deleted node.
   3. Insert the root node's old value into the "light" subtree.
   4. Rebalance the left and right subtrees.

The script `comparison_test.py`, included in [the project repository](
https://github.com/pattymartin/subjective-sort), tests my custom tree against a
standard AVL tree by inserting a randomized list of integers into each tree.
The results of this test show that my algorithm results in fewer comparisons
while taking longer to compute (of course, the time difference is negligible in
this particular case).

Example output:

```
Sorting 10 items.
          Comparisons    Seconds
   AVL             24    0.0
MyTree             24    0.0009987354278564453

Sorting 25 items.
          Comparisons    Seconds
   AVL             90    0.0
MyTree             86    0.0009958744049072266

Sorting 50 items.
          Comparisons    Seconds
   AVL            225    0.0
MyTree            213    0.0019958019256591797

Sorting 100 items.
          Comparisons    Seconds
   AVL            537    0.0019562244415283203
MyTree            523    0.004068851470947266

Sorting 1000 items.
          Comparisons    Seconds
   AVL           8696    0.028889894485473633
MyTree           8568    0.16196513175964355

Sorting 10000 items.
          Comparisons    Seconds
   AVL         120983    0.37586259841918945
MyTree         119020    5.0684661865234375
```

## Waiting for Input

The sorting must be done asynchronously, waiting for input from the user before
continuing. The sorting is done in a separate thread from the GUI so that it
can be paused while waiting for a response.

## Undoing Comparisons

The program allows comparisons to be undone by pressing `Ctrl`+`Z`.

To do this, the recursive insertion method raises an exception which is then
caught by the previous iteration. Or, if the current node is the root of the
tree, the tree is reverted to the state it was in before the most recent
insertion, and the previously inserted value is re-inserted into the tree.

## Close and Resume

If the window is closed before sorting is finished, the tree will be pickled
and saved to a file, to be resumed whenever the script is run again.

## Next steps

There are likely sorting algorithms that require fewer comparisons than the one
I used here. In particular, the [Ford-Johnson algorithm](
https://en.wikipedia.org/wiki/Merge-insertion_sort) (also known as
merge-insertion sort) is known for its low number of comparisons. In the
future, I may wish to experiment with this algorithm.
