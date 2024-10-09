# Lecture 1
The goals of this course are as follows:

- Solving computational problems
- Arguing the correctness of the solution
- Arguing the efficiency of the solution

#### What is an algorithm?

>[!definition] Algorithm
An algorithm is a finite sequence of mathematically rigorous instructions that solves a specific problem.
#### How to prove correctness?
Since our algorithm should work ideally with a large size input we could use induction. Let’s do an example problem of finding matching birthdays in a classroom.

>[!hypothesis]  Inductive Hypothesis
>if the first k students contain a double birthday we should return the match before checking student k+1

And now we start from the easiest case: $k=0$. In this case of course there are no matches, so then for each $k^{'}$ we need to check it against every person in the range $[0,k^{'}-1]$ and return the match if we find one.

#### Efficiency
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
# Lecture 2
#### Data structure Vs Interface
>[!definition] DS Vs API
>An interface is a description (specification) of what you can store and do on data, a data structure stores the data and provides the representation of it and the algorithms to act on it.

#### Base data structures
We first focus on two general data structures:

- Sets
- Sequences

#### Sequences
A sequence is a data structure where data is ordered linearly.
##### Static sequence interface
We want the static sequence DS to support some operations:

- **build(x)**: takes some items and creates a DS of size len(x) 
- **len()**: returns n=len(x)
- **iter_seq()**: outputs $x_{i}$ for i in $[0,n-1]$
- **get_at(i)**: returns $x_{i}$
- **set_at(i,x)**: sets $x_{i}=x$

The solution for this interface is:

##### Static Array (DS)
Static arrays are stored consecutively in memory so that to access a certain value in them we can use:

$array[i]=memory(address(array)+i)$

The computational time for the needed functions are:

- **set_at,get_at,len** take $O(1)$ time
- **build,iter_seq** take $O(n)$ time

##### Dynamic sequence interface
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

##### Dynamic Arrays (DB)
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

# Lecture 3
#### Sets
A set is a data structure we use when we are interested in addressing data according to a specific key.
##### Set interface
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

##### Array as Set (DS)
We can just use an array to store in memory the set, all operations previously described take $O(n)$ time.

##### Sorted Array as Set (DS)
Now the computational times are:

- **build(x)**: $O(n\log n)$, needs to keep them organized ($O(\log n)$ for each element)
- **find(k)**: $O(\log n)$, cause of BS
- **insert(x),delete(x)**: $O(n)$
- **find_min(),find_max()**: $O(1)$, beginning or end
- **find_prev(k),find_next(k)**: $O(\log n)$

This is better on all computations except for **build(x)**, because we need to sort the array when building it.

#### Sorting
We need to define sorting and find a good way to implement it in order to get the Sorted Array we need as a set.
Let’s define versions of sorting relative to different aspects:

>[!definition] Destructive Vs Non destructive
>Destructive sorting overwrites the input array while sorting, while Non Destructive creates a new Array altogether.

>[!definition] In Place Vs Not In Place
>In Place algorithms only use constant extra space, Not In Place might use linear or worse.

##### Permutation Sort
Check every possible permutation of the array in input ($\Omega(n!)$) and, for each, check sorting ($O(n)$). This algorithm is obviously not efficient and takes $\Omega(n\cdot n!)$ .
##### Selection Sort
![[SelectionSort.png]]

We write a function *prefix_min* that can find the index of the minimum value in the array starting from index i and call it recursively from itself.
This function needs to be called inside of another function *selection_sort* that calls itself recursively and updates 1 value for every complete call of the first function.
Since a complete call to *prefix_min* takes $O(n)$ time, the complete call to *selection_sort* will take $O(n^{2})$ time.

##### Merge Sort
![[MergeSort.png]]

As we can see the idea is to start from the single elements of the list (which are sorted because they contain 1 element only). We then take 2 items and sort them, then 2 couples (which are sorted) and we use 2 pointers (one for each size 2 array) starting from the ends and add the value of the biggest element pointed to the end of the merged array. If we do this recursively and move the pointers when we write a certain value, we will get the whole sorted array.

The merging takes on average $O(n)$ time and it is called $O(\log n)$ times. The total complexity is $O(n \log n)$.

# Lecture 4
##### Hashtables as Sets
The current best look-up time achieved is $O(\log n)$, the next question we ask ourselves is: **can we do better than that?**
The idea is to use a contiguous static array because it has constant look up time. The look-up function *find(k)* searches for the value with key k, which in this case translates to looking for the k-th element in the array. 
We can see a problem in this: suppose we have 3 key-value pairs to put into the array, namely {(0,10),(1,25),(100000,3)}. Do we need to create a 100000 sized array to store this data?
No, we define an array of size $\Theta(n)$ and only use that. We now need a way to map the keys to the indexes of the array: we need a hashing function!
The innovation is not to store value v from pair (k,v) into position k but into position $h(k)$, where $h$ is the chose hashing function. These types of functions, however, introduce the problem of **hash collision**.

>[!definition] Hash Collision
>Hash collision happens when two values $x$ and $y$ become the same value $k$ when passed thru a hashing function ($h(x)=h(y)=k$).

To fix this issue our array will not contain the value directly pointers to other dynamic data structures, like Linked Lists or Dynamic Arrays that will contain all the values with the same hashed key.

We now only need to decide on the hashing function, let’s see some options.
###### Modulo hashing
>[!definition] Modulo hashing
>$$
>h(k) = k \; \text{mod} \;  m
>$$
>where m is the size of the array and k is a generic key.

But this way we could have a input that is bad for this hashing function, for example keys that all map to the same hashed value, and we would lose efficiency.
Instead we want to first see the values and only later pick a hashing function at random from a family of valid ones, so that its difficult for a user to give us bad data for our function.
Let’s define this family:
###### Universal hashing function family
>[!definition] Universal hashing function
>$$
>\mathcal{H_{p,m}}=\{h_{a,b}\;\rvert a,b \in \{0,\dots,p-1\}, a \neq 0\}
>$$
>with
>$$
>h_{a,b}(k)=(((a \cdot k +b)\; \text{mod}\;p)\;\text{mod}\; m)
>$$

This has expected value for the length of each of the Linked Lists equal to $1+\frac{n-1}{m}$

# Lecture 5
Before, with merge sort, we achieved $O(n\log n)$ computation time. Can we do better?

We can think of the sorting of a size $n$ list as outputting a list representing the correct permutation to bring it to the sorted state. Having size $n$, we have $n!$ possible permutation and we can represent the sorting algorithm with a decision three where each leaf is a comparison and where at the end of each path we have a possible output. The depth of a path is, on average $O(\log(\text{number\_of\_leaves}))$ which, in our case brings us to $O(\log n!)$.

Using stirling’s formula we can rewrite it as $O(n \log n)$ which shows that this complexity is the best one we can get when working in a comparison model.

Let’s see if we can do better under some restrictions.
##### Direct Access Array Sort (DAA Sort)
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




