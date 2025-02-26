The goals of this course are as follows:

- Solving computational problems
- Arguing the correctness of the solution
- Arguing the efficiency of the solution

# What is an algorithm?

>[!definition] Algorithm
An algorithm is a finite sequence of mathematically rigorous instructions that solves a specific problem.
# How to prove correctness?
Since our algorithm should work ideally with a large size input we could use induction. Let’s do an example problem of finding matching birthdays in a classroom.

>[!hypothesis]  Inductive Hypothesis
>if the first k students contain a double birthday we should return the match before checking student k+1

And now we start from the easiest case: $k=0$. In this case of course there are no matches, so then for each $k^{'}$ we need to check it against every person in the range $[0,k^{'}-1]$ and return the match if we find one.

# Efficiency
Now that we defined the algorithm and argued its correctness we would like to argue its efficiency as well.

To do this we use **asymptotic analysis**:
$$
	O(\cdot) = \text{upper bounds}
$$
$$	
	\Omega(\cdot) = \text{lower bounds}
$$
$$	
	\Theta(\cdot) = \text{upper and lower bounds}
$$
To get these values we need to know what che computer actually does whenever it needs to run a specific command.
# Data structure Vs Interface
>[!definition] DS Vs API
>An interface is a description (specification) of what you can store and do on data, a data structure stores the data and provides the representation of it and the algorithms to act on it.

# Base data structures
We first focus on two general data structures:

- Sets
- Sequences
## Sequences
A sequence is a data structure where data is ordered linearly.
### Static sequence interface
We want the static sequence DS to support some operations:

- **build(x)**: takes some items and creates a DS of size len(x) 
- **len()**: returns n=len(x)
- **iter_seq()**: outputs $x_{i}$ for i in $[0,n-1]$
- **get_at(i)**: returns $x_{i}$
- **set_at(i,x)**: sets $x_{i}=x$

The solution for this interface is:
#### Static Array (DS)
Static arrays are stored consecutively in memory so that to access a certain value in them we can use:

$array[i]=memory(address(array)+i)$

The computational time for the needed functions are:

- **set_at,get_at,len** take $O(1)$ time
- **build,iter_seq** take $O(n)$ time

### Dynamic sequence interface
Dynamic sequences should have all methods present in Static sequences, plus two new ones:

- **insert_at(i,x)**: shift every $x_{k}$ with $k>i$ by one to the right and do $x_{i} = x$ 
- **delete_at(i)**: shift every $x_{k}$ with $k>i$ by one to the left

It is sometimes beneficial to define special cases for operations that might be more optimized, even if the general operation would still be able to solve them:

- **insert_first(x),insert_last(x)**
- **delete_first(),delete_last()** 

The solution for the Dynamic sequence interface is:
##### Linked Lists (DB)
![[LinkedList.png]]

Linked Lists are series of size two arrays where the first element is a datapoint and the second is a pointer to the address of the next array. 
The head can be another node where the first element is the length and the second one points to the first datapoint. 

Let’s see the performance of this DS on the operations we want to implement:

- **insert_first,delete_first** take $O(1)$ time
- **get_at,set_at** take $O(n)$ time (actually $O(i)$)

Other operations at the end become fast (constant time) if we make the first “node” (head) a size 3 array that also contains the pointer to the last element. This needs to be kept up to date. 
#### Dynamic Arrays (DB)
![[DynamicArray.png]]

Dynamic arrays are another solution to the Dynamic sequence interface that takes the best aspects of Linked Lists and Static Arrays.

They are born when we relax the requirement of $\text{size(array)}=n$
We instead define the size as $size=\Theta(n)$ $\&$ $\text{size}\geq n$
We also keep the idea of $array[i]=x_{i}$
Finally we keep a 3 element head with:

- the pointer to the beginning of the array
- the current length of the array
- the max allocated size of the array

So now for example **insert_last** can be done in constant time by just using the current length, inserting at that place and updating $len$ (if $len<size$).
In case $len=size$ we need to allocate a new array of increased size. Usually we choose to give create an array of $new\_size=c*old\_size$. We do this to avoid having to redefine the array often, because it takes linear time.
## Sets
A set is a data structure we use when we are interested in addressing data according to a specific key.
### Set interface
Let’s list the methods we want to be able to perform on a set:

- **build(x)**: takes data in x and builds the set for that data
- **len()**: returns number of elements in set
- **find(k)**: returns the stored item with key k
- **insert(x)**: add x to the set (replace if x.key is alread present)
- **delete(k)**: removes (and returns) item with key k
- **iter_ord()**: return items in key order
- **find_max()**: returns item with max key
- **find_min()**: returns item with min key
- **find_next(k)**: returns item with smallest key larger than k
- **find_prev(k)**: returns item with largest key smaller than k

We can implement this interface in many ways:
#### Array as Set (DS)
We can just use an array to store in memory the set, all operations previously described take $O(n)$ time.
#### Sorted Array as Set (DS)
Now the computational times are:

- **build(x)**: $O(n\log n)$, needs to keep them organized ($O(\log n)$ for each element)
- **find(k)**: $O(\log n)$, cause of BS
- **insert(x),delete(x)**: $O(n)$
- **find_min(),find_max()**: $O(1)$, beginning or end
- **find_prev(k),find_next(k)**: $O(\log n)$

This is better on all computations except for **build(x)**, because we need to sort the array when building it.
#### Hashtables as Sets
The current best look-up time achieved is $O(\log n)$, the next question we ask ourselves is: **can we do better than that?**
The idea is to use a contiguous static array because it has constant look up time. The look-up function *find(k)* searches for the value with key k, which in this case translates to looking for the k-th element in the array. 
We can see a problem in this: suppose we have 3 key-value pairs to put into the array, namely {(0,10),(1,25),(100000,3)}. Do we need to create a 100000 sized array to store this data?
No, we define an array of size $\Theta(n)$ and only use that. We now need a way to map the keys to the indexes of the array: we need a hashing function!
The innovation is not to store value v from pair (k,v) into position k but into position $h(k)$, where $h$ is the chosen hashing function. These types of functions, however, introduce the problem of **hash collision**.

>[!definition] Hash Collision
>Hash collision happens when two values $x$ and $y$ become the same value $k$ when passed thru a hashing function ($h(x)=h(y)=k$).

To fix this issue our array will not contain the value directly but pointers to other dynamic data structures, like Linked Lists or Dynamic Arrays that will contain all the values with the same hashed key.

We now only need to decide on the hashing function, let’s see some options.
##### Modulo hashing
>[!definition] Modulo hashing
>$$
>h(k) = k \; \text{mod} \;  m
>$$
>where m is the size of the array and k is a generic key.

But this way we could have a input that is bad for this hashing function, for example keys that all map to the same hashed value, and we would lose efficiency.
Instead we want to first see the values and only later pick a hashing function at random from a family of valid ones, so that its difficult for a user to give us bad data for our function.
Let’s define this family:
##### Universal hashing function family
>[!definition] Universal hashing function
>$$
>\mathcal{H_{p,m}}=\{h_{a,b}\;\rvert a,b \in \{0,\dots,p-1\}, a \neq 0\}
>$$
>with
>$$
>h_{a,b}(k)=(((a \cdot k +b)\; \text{mod}\;p)\;\text{mod}\; m)
>$$

This has expected value for the length of each of the Linked Lists equal to $1+\frac{n-1}{m}$.
# Sorting
We need to define sorting and find a good way to implement it in order to get the Sorted Array we need as a set.
Let’s define versions of sorting relative to different aspects:

>[!definition] Destructive Vs Non destructive
>Destructive sorting overwrites the input array while sorting, while Non Destructive creates a new Array altogether.

>[!definition] In Place Vs Not In Place
>In Place algorithms only use constant extra space, Not In Place might use linear or worse.
## Permutation Sort
Check every possible permutation of the array in input ($\Omega(n!)$) and, for each, check sorting ($O(n)$). This algorithm is obviously not efficient and takes $\Omega(n\cdot n!)$ .
## Selection Sort
![[SelectionSort.png]]

We write a function *prefix_min* that can find the index of the minimum value in the array starting from index i and call it recursively from itself.
This function needs to be called inside of another function *selection_sort* that calls itself recursively and updates 1 value for every complete call of the first function.
Since a complete call to *prefix_min* takes $O(n)$ time, the complete call to *selection_sort* will take $O(n^{2})$ time.
## Merge Sort
![[MergeSort.png]]

As we can see the idea is to start from the single elements of the list (which are sorted because they contain 1 element only). We then take 2 items and sort them, then 2 couples (which are sorted) and we use 2 pointers (one for each size 2 array) starting from the ends and add the value of the biggest element pointed to the end of the merged array. If we do this recursively and move the pointers when we write a certain value, we will get the whole sorted array.

The merging takes on average $O(n)$ time and it is called $O(\log n)$ times. The total complexity is $O(n \log n)$.

Up until now, with merge sort, we achieved $O(n\log n)$ computation time. Can we do better?

We can think of the sorting of a size $n$ list as outputting a list representing the correct permutation to bring it to the sorted state. Having size $n$, we have $n!$ possible permutation and we can represent the sorting algorithm with a decision three where each leaf is a comparison and where at the end of each path we have a possible output. The depth of a path is, on average $O(\log(\text{number\_of\_leaves}))$ which, in our case brings us to $O(\log n!)$.

Using stirling’s formula we can rewrite it as $O(n \log n)$ which shows that this complexity is the best one we can get when working in a comparison model.

Let’s see if we can do better under some restrictions.
## Direct Access Array Sort (DAA Sort)
If we have a set of keys which is fairly small and every key is unique we can use a direct access array.

In this method we:

- Create a direct access array of size $u$, where the keys are in $[\;0,u\;]\;\rightarrow \; O(u)$ time complexity
- Store each item in the corresponding slot for its key $\rightarrow \; O(n)$ time complexity
- Go through the DAA and read only values that exist $\rightarrow \; O(u)$ time complexity

So the whole process has a time complexity of $O(u+n)$ and, if $u$ and $n$ are of the same order, we have a sorting algorithm working in linear time.

The restrictions we made at the beginning are a bit too strict so let’s try to relax them.
Take $u\lt n^{2}$ , this produces a version of the previous algorithm that runs in $O(n^{2})$ time which is not good. 
Try with an example:

>[!example] Tuple Sort
>We want to sort the array:
>$$
>list=[17,3,24,22,12]
>$$
>
>$\text{with}\; n = 5, u\lt n^{2}$
>
>Now in this case the keys are the numbers themselves, let’s define:
>$$
>a = k\;//\;n 
>$$
>$$
>b = k\;\%\;n
>$$
>So that we now get:
>$$
>list=[(3,2),(0,3),(4,4),(4,2),(2,2)]
>$$
>And we can get a decent sorting by using only $a$.
>This is obviously not enough because it’s not really a sort.
>We can then sorb by $b$ but this erases the work we’ve done in the first pass.
>A better way is to first sort by the least significant value and then by the most, keeping in mind that during the second sort we should keep the relative order of values with same most significant item.
>Unfortunately, to use a Direct Access Array, there must be no duplicates so they are not compatible.
>
## Counting Sort (CS)
Counting sort is similar to the previously seen sort in the sense that it sorts by least significant bit first and most significant bit later. To avoid collisions the data is stored in an contiguous array that stores pointers to sequences DS, where the data is actually put (similar to hashing). This also preserves the order of the data as we wanted and takes $O(u+n)$.
## Radix Sort (RS)
This is useful if the size of the integers is much larger than $n$. In this case, we first break up the values into a tuple of size $n$ values (for example 3 size $n$ values if the size $u$ of the integers is in the order of $n^{3}$) and then repeat a counting sort for each value in the tuple. This takes $O(n)$ for breaking up all the digits into tuples and then $O(n\log_{n}{u})$ for the $\log_{n}{u}$ digit sorts. In total, it takes $O(n+n\log_{n}{u})$ which, if $u$ is polynomial in $n$ ($u\leq n^{c}$), becomes $O(n(c+1))=O(n)$.
# Binary trees
![[Pasted image 20250224170217.png]]

Binary trees are data structures built upon Nodes. A Node is an object which has a value, a parent link, a right child link and a left child link. The links are just pointers to the parent and the two children, respectively. Binary trees are good because allow for logarithmic time traversal due to how they’re built.

Let’s define some terminology:

- Ancestors of a Node: all the nodes that one gets by going backwards following the parent link, up to the root. 
- Descendants of a Node: all the nodes one finds following the children links towards the leaves. 
- Subtree: a the tree formed by a Node and all of its descendants.
- Node depth: The number of edges found when going from the node up to the root. It is also the number of ancestors of the node.
- Node height: The number of edges found when going from the node down to the furthest leaf. It is also the maximum depth of a Node in the subtree of the node we’re considering.
- Height of the tree: The height of the root node ($h$).
- Traversal order: It is a way to order the nodes in a tree. For every node, every node in its left subtree should come before in the ordering, then the node itself, then the nodes in the right subtree. Defining this order allows us to define operations such as “finding the first node in traversal orden in the subtree” which goes to the left most leaf and prints it (might even be the node itself). Another operation is “finding the successor of a node in traversal order” which either finds the first of the right subtree or goes back until it moves up through a left link and outputs that. Both operations have $O(h)$ time complexity. 

The idea is that now it is very easy to insert new values wherever we want. For example if we want to insert a new value after a specific other one, we can just take the subtree of the value that is already in the tree and place the new value as the left child of the left-most leaf. For an insertion to the right we just attach the new value as a left child of the left-most leaf in the right subtree.

We can also have efficient deletion. If we want to delete a leaf, we can just eliminate the pointer to that node, since it doesn’t mess with the order. If we want to eliminate a node that isn’t a leaf, we find its predecessor in the order and swap the two values, continue doing this until the value we want to eliminate is on a leaf, then eliminate the leaf as before. This also doesn’t mess with the order because we always swap to adjacent values and then eliminate the only one that’s out of order.

Both insertions and deletions have time complexity of $O(h)$.

To implement a Set with a Binary Tree we make the traversal order the order of increasing keys (they’re called Binary Search Trees (BST)).

To instead implement a Sequence with a Binary Tree we just make the traversal order the order of the sequence. By doing this we can search for the $i$-th element in the sequence easily if we know, for each node, the size of the subtree that starts at that node (the number of the nodes in that subtree). If we know all that, when we need to find the $i$-th element in the subtree of a node we have three choices:

- $i=node.left.size$ : then the element is the one associated with $node$.
- $i<node.left.size$ : the element is in the left subtree and we can iterate.
- $i>node.right.size$ : the element is in the right subtree and we can iterate.

This way we build a Binary Search-like algorithm to find the data we need.
Insertion and deletion are implemented in the same way by first searching the right position and then using insert_before or insert_after.

To be able to perform a search even after adding or deleting a value we need to keep the sizes updated, fortunately this is easy since we always add or subtract a new leaf so we just need to add or subtract 1 to the sizes of every ancestor of the new node (/eliminated node).

Size of a subtree is a **subtree property** which means that it can be calculated as a function of only the properties of the left and right subtree and of the original node. Example of other subtree properties are product, min, max or, in general, the sum of some feature over all nodes in the subtree. Examples of things that we cannot mantain efficiently are node’s index or depth.

Up until now, we demonstrated that all the operations (except build) can be completed in $O(h)$ time. We now want to give a bound to this in terms of $n$.

Trees which have $h=\log n$ are called **balanced trees**. If we find a way to “balance” an unbalanced tree, then we have logarithmic time complexity on all operations. The trick is that there are many tree representations for every ordering and we can switch a bad tree representation with another one for the same order which is closer to a balanced tree.

This condition of almost-perfect balance is described by AVL trees, which are trees where, for every node, the difference between the left and right depths is either $1,0,-1$. It is said that they maintain height balance.

Now the worst case scenario for an AVL tree to be different from a balanced tree is if at every step we have the right and left subtrees being 1 (or -1) different in height. In this case, the total number of nodes in such a tree with height $h$ will be:

$N_{h}=N_{h-1}+N_{h-2}+1$

This is a recursive formula, where the $N_{h-1}$ is for example the number of nodes of the left subtree, $N_{h-2}$ the one of the right subtree (or viceversa) and the 1 counts the root of the subtree. This grows faster than the fibonacci sequence (which is exponential), which means that, if $N$ scales exponentially with $h$, $h$ scales logarithmically with $N$.

Now we know that we should always keep the tree as an AVL tree. The only times when we can mess up the AVL property are when inserting or deleting a node (which is always a leaf). This modifies the height of all the ancestors of the node in question (height is a subtree property) and so we can travel backwards in the tree to check where an imbalance happens. When and imbalance happens it always creates skew of 2. We need to first determine the node which has a skew of 2 between its children. Then we are presented with 3 cases:

- The child node with the highest height has skew 0 among its children: In this case we perform a left rotation as in the image below.

![[Pasted image 20250226003020.png]]

- The child node with the highest height has skew 1 among its children: Same as previous case. 
- The child node with the highest height has skew -1 among its children: We should perform first a right rotation of that node, then a left rotation of what was previously the root.

![[Pasted image 20250226003101.png]]
# Priority queue interface
Priority queues are subsets of Sets in which we want to implement the following functions:

- **build(x)**: Initialize a priority queue with the items in x.
- **insert(x)**: Add the x item to the queue.
- **delete_max()**: Delete and return the max key item.
- **find_max()**: Find the max key item.

This interface can be implemented with different data structures, such as:
## Set AVL as Priority queues
This is able to implement all functions except **build(x)** (which takes $O(n\log n)$) in $O(\log n)$ time complexity. Furthermore, using subtree properties (in this case max) we can implement **find_max()** in constant time by saving the max of the subtree in any subtree.
Unsorted and sorted arrays have better performance in one of either **insert(x)** or **delete_max()**:

![[Pasted image 20250226162940.png]]

We want to try to mix these structures to get the best of both worlds.
## Priority queue sort
The idea is to define a sorting algorithm that uses the functionalities of a priority queue. It works by first inserting all the unsorted items and then iterating **delete_max()** to get them in reverse sorted order. This takes:

$T_{build}+n \cdot T_{delete\_max} \leq n \cdot (T_{insert}+T_{delete\_max})$
## Complete binary trees and arrays
We define a complete binary tree as one having the following properties:

- At any depth $i$ (except the last one) there should be $2^{i}$ nodes.
- All the nodes in the last layer (deepest depth) are left justified.

Then we have a bijection between any such tree and an array, obtained by listing the nodes from top to bottom and from left to right. Complete binary trees also don’t need rotations since they already have the best depth possible. Instead of storing the binary tree we just store array and use maths to find the children or parents of any node.
## Heaps
A Heap is just a Complete Binary Tree which also has the following **Heap** property:

- For any specific node, its key should be greater than both of its children’s keys.

If this property is satisfied, then we can implement the functionalities we need as:

- **insert(x)**: We **insert_last(x)** which is easy in an array and then we need to check that the **Heap** property is still verified. To do this it’s enough to check with its parent, if the property is violated we just swap the two values and recurse up to the next parent. This takes $O(\log n)$.
- **delete_max()**: Just as before, we swap the first and last element, eliminate the last and then check for the **Heap** property. To do that we start from the first element (which is the root) and, if the property is not valid, swap it with the highest valued children. Then recurse.

The linear time complexity build is based on inserting all items and max_heapifying_down.
# Graphs
A graph $G(E,V)$ is a network where the vertices $V$ are connected by edges $E$. Graphs can be either directed or undirected, depending if vertices specify the direction of the link or not.
Mostly, here, we will talk about:

>[!definition] Simple Graph
>A simple graph is a graph with no self loops and where every edge is distinct.

Obviously these graphs have an upper bound to the number of edges, which is in the order of $|V|^{2}$.

![[Pasted image 20250226180008.png]]

Given a graph like the one in the image, we can define:

- The outgoing set from a node: $Adj^{+}(0)=\{2\}$
- The incoming set of a node: $Adj^{-}(0)=\{1\}$
- The outgoing degree of a node (number of outgoing edges): $deg^{+}(1)=2$
- The incoming degree of a node: $deg^{-}(1)=1$

Note that:

$\sum_{u \in V}deg^{+}(u)=|E|$ if directed, $2|E|$ if undirected

Most of the times we want to find the shortest path between two vertices. A path is defined as a list of vertices where, for every couple of adjacent vertices, there is an edge connecting them in the right direction.

If we are able to compute the shortest path from a specific node to any other node we can store this information occupying only $|V|$ space by creating a shortest-path tree where every vertices specific node points to its predecessor in the shortest path.
This is good because shortest paths have the property that if the shortest path from $A$ to $C$ is $(A,B,C)$, then the shortest path from $A$ to $B$ is just $(A,B)$. Note that changing anything in the original network changes the  shortest-path tree completely.

So, how do we compute shortest paths? First let’s define:

>[!definition] Level sets
>A level set is the set of all vertices which are at the same shortest distance from a another given vertex.
>
>$L_{k}=\{v \in V : d(s,v)=k\}$

Once we know these, then we can easily recover the paths.
One way to compute them is via a common algorithm:
## Breadth-first search (BFS)
The idea here is to assign the source (the node we want to calculate the shortest-path to) to the $L_{0}$, set the distance $\delta(s,s)=0$ and leave the value in the shortest-path tree for node $s$ empty since it doesn’t have a previous node.
Then we iterate for every $L_{i}$, based on $L_{i-1}$. We iterate over the nodes $u$ of $L_{i-1}$ and, for each, over the nodes $v$ that are adjacent to $u$ but which we haven’t already inserted into any previous $L$. Then, we add the $v$ s to $L_{i}$, set $\delta(u,v)=1$ and define $P(v)=u$. 

BFS takes $O(|V|+|E|)$ time.
## Depth First Search (DFS)
Obtaining the shortest path between two vertices is not the only problem we might deal with when talking about graphs. Another important problem is the one of determining whether a node is reacheable or not from another.

To store this information we use Parent Trees which are normal trees where every node is decorated with its predecessor in a path starting at the source. The paths delineated this way don’t have to be the shortest.

How to construct such a tree?

An algorithm to solve this problem and compute this tree is Depth First Search (DFS). This algorithm is a recursive one that, starting from the source, recurses to the left child and the right child while setting the parent to the node which called the recursion.

DFS takes $O(|E|)$ time.

Depth First Search can also be used to enumerate the connected components in an undirected graph. A connected component in an undirected graph is a set of vertices where every vertex is reacheable from all other vertices. A graph can contain multiple disjoint connected components. We can solve the problem by just choosing 1 node and applying DFS starting from it, while marking all the visited nodes to avoid checking them again, then continue if some node hasn’t been checked. This is called **Full DFS** and takes $O(|V|+|E|)$ time.

Now let’s define a special type of graph:

>[!definition] Directed Acyclic Graph (DAG)
>A DAG is a directed graph without any directed cycles.
>

An example of these are trees (if edges are pointed downwards). We also define:

>[!definition] Topological order
>A Topological order is an ordering $f$ where $f(u)<f(v) \,\,\forall \,\,(u,v)\in E$, where $(u,v)$ is directed from $u$ to $v$.
>

An example of a topological order is:

>[!definition] Reverse Finishing order
>A reverse finishing order is the ordering of the nodes which a Full DFS visits, reversed.



