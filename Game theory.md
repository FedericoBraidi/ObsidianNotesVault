#### What is a game?
>[!definition] Game
> A game is a multi-person multi-objective problem.

We would like the problems to be described by easy mathematical models, so that we can better find solutions. Single-player problems are usually much simpler than multi-player. In these cases the uncertainties are only related to lack of information, random noise and approximations.
When we are faced with more complex multi-player games an additional uncertainty is added, which is related to the actions of other players. Anyways, to study these kinds of games effectively we must make two assumptions:

- The rules of the game are known in advance.
- The players are rational.
#### Decision problems

>[!definition] Decision problem
>We can define a decision problem as one that has three elements:
>- Actions: elements of a set $A$ which are what can be decided upon.
>- Outcomes: the possible results of the actions in $A$.
>- Preferences: A way to describe what outcomes are preferable for the decider.
##### Preference
>[!definition] Preference
>A preference is a relationship $\geq$ on $A$. If $a \geq b$ then $a$ is ranked above $b$.

Preferences can be:
- Complete: $\forall \, a,b \in A$ then $a \geq b$ or $b \geq a$ or both.
- Transitive: $\forall \, a,b,c \in A, a \geq b \,\, \text{and} \,\, b \geq c \rightarrow a \geq c$ 
If a preference is both complete and transitive it is called rational.
##### Utility functions
>[!definition] Utilities
>A utility (or payoff) function is an arbitrary function $u(q)$ that quantifies the goodness coming from an input $q$.

They are usually increasing functions of $q$ and have $u'(q) \geq 0, u''(q) \leq 0$ .
The formulation of $u$ doesn’t matter but if $u(q) \geq u(q')$ a rational player prefers $q$ over $q'$ .

We say that $u$ represents $\geq$ if $\forall \, a,b \in A$: $a \geq b \Leftrightarrow u(a) \geq u(b)$ .

#### Lotteries (randomness)
Lotteries are games where the payoff has random parts to it. Rational players don’t like this because it messes with preference orders and with our knowledge of the system.
##### Nature
In game theory random events are the consequences of the choices of other players, the sum of which we call Nature.

Lotteries can be discrete or continuous based on the nature of the probabilities of the outcomes.
Since a random component is involved, to be able to define a preferred step we need to first define:

>[!definition] Expected Utility
>We define the expected utility from lottery p as:
>
>$\mathbb{E}[u(x)|p]=\sum_{k=1,\dots,n}p(x_{k})x(x_{k})$

Using this we can redefine preference relations in presence of randomness. Some of the important properities that we would like $\geq$ to have are:

- Rationality
- Continuity axiom
- Independence axiom

We already know what rationality is, let’s define the other two.

>[!definition] Continuity axiom
>Arbitrarily small variations in the gamble should not change my preference.
>For $p,q,r \in P(A)$, set of the lotteries on A, it must hold that the sets:
>- $\{a \in [0,1] : ap + (1-a)q \geq r\}$
>- $\{a \in [0,1] : r \geq ap + (1-a)q\}$
>
>are closed.

>[!definition] Independence axiom
>When introducing new gambles we mantain the same original order of preference independent on the new option.
>For $p,q,r \in P(A)$, it holds that $\forall a \in [0,1]$:
>- if $p \geq q$ then: $ap + (1-a)r \geq aq + (1-a)r$

A good candidate for a utility measure is the vN-M utility.

>[!definition] vN-M utility
>Any function such that $\forall p,q \in P(A)$:
>
>$p \geq q\,$ implies $\,\mathbb{E}[u(x)|p] \geq \mathbb{E}[u(x)|q]$

##### Risk attitude
Risk attitude comes into play when two lotteries have the same expected utility (for example a game with a 1 euro return or one with 5% of having a 20 euro return).
There are 3 different profiles of players:

- Risk neutral: doesn’t care as long as the expected utility is the same.
- Risk averse: prefers a degenerate lottery (the one with the sure result).
- Risk loving: opposite of risk averse.

They can respectively be represented by:

- Linear $u$
- Concave $u$
- Convex $u$

Let’s see an example of a decision tree. $r,s$ are the choice between ravioli and soup, then each one can be tasty(10) or non tasty(1) with probabilites 50-50 for ravioli and 20-80 for soup. For the ravioli you can add cheese which then has a 60-40 chance to be good or not. Bad cheese has 0 value.

![[DecisionOverTime.png]]

We solve this with dynamic programming. We define:

>[!definition] Group 1 nodes
>Group 1 nodes are those that, among their direct and indirect children, don’t have any player choice nodes. Basically a node is a group 1 if its children are leaves or only Nature moves are present after.

>[!definition] Group k nodes
>Group k nodes are those that are followed by at least one group k-1 node, without any higher order nodes.

![[Pasted image 20241009212919.png]]

In this case we have 2 Group 1 nodes (orange) and one Group 2 node (yellow). 
At any Group 1 node we already know what to do, then we collapse each Group 1 with its best outcome and every Group k node becomes a Group k-1 node.

![[Pasted image 20241009213206.png]]

We can just repeat until we get a single Group 1 node.
##### Information
Suppose a friend of the player already knows what the canteen has today and would tell P about it for a reward. We need to balance the cost of the reward for the friend with the value the information provides. The information effectively makes us move after Nature has already moved. We can rearrange the tree like this:

![[Pasted image 20241009213635.png]]

In the case without information the expected utility was:

$\mathbb{E}(u|notknowledge)=3.5$

While now it is:

$\mathbb{E}(u|knowledge)=4.8$

We can easily get that the information is worth 1.3

#### Multi player games
