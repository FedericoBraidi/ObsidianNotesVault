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
Let’s start from the easies form of mutli player games:
##### Static games of complete information
As the name suggests, these games are characterized by being:

- Static: All players move without knowledge of everybody else’s moves (either together or in fow).
- Complete information: Everyone’s payoff functions are known. Payoff functions are Common Knowledge (i.e. everybody knows them, everybody knows that everybody knows them, etc.)

It is useful to think about moves in terms of a Plan of Action, a strategy that tells you, given what happened previously, what is your next best move.
There are different kinds of Plans of Action, including random ones. We start with Certain plans, also called Pure Strategies, which are deterministic.

Player $i$ chooses a strategy $s_{i}\in S_{i}$ and plays according to it, getting a series of moves $(s_{1},\dots, s_{i}, \dots,s_{n})$ and gets payoff $u(s_{1},\dots, s_{i}, \dots,s_{n}) \in \mathbb{R}$.
We say that the normal form of the game is written as $G = \{S_{1},\dots,S_{n}\,;u_{1},\dots,u_{n}\}$
##### Pareto efficiency
A joint strategy $s$ is Pareto dominated by another strategy $s'$ if:

- $u_{i}(s') \geq u_{i}(s) \, \text{for every player} \, i$
- $u_{i}(s') > u_{i}(s) \, \text{for some players} \, i$

A joint strategy $s$ is Pareto Efficient if it is not Pareto dominated by any strategy $s'$

##### Strictly dominated strategies
Given a certain game and a set of strategies $S$, take $s_{i},s_{i}' \in S_{i}$ .
We say that $s_{i}$ is strictly dominated by $s_{i}'$ if  the payoff of player $i$ when playing $s_{i}'$ is greater than when playing $s_{i}$ for every choice of the moves of other players. Rational players would never play strictly dominated strategies.

##### Best response
Strategy $s_{i} \in S_{i}$ is the best response for player $i$ to the opponent moves $(s_{1},\dots,s_{i-1},s_{i+1},\dots,s_{n})$ if :
$u(s_{1},\dots,s_{i-1},s_{i},s_{i+1},\dots,s_{n}) \geq u(s_{1},\dots,s_{i-1},s_{i}',s_{i+1},\dots,s_{n})$ for every $s_{i}' \in S_{i}$

The sequence of moves $(s_{1},\dots,s_{i-1},s_{i+1},\dots,s_{n})$ is often written as $s_{-i} \in S_{-i}$ .
The rational player that believes the opponent is gonna play $s_{-i}$ will always play the best response to that. If a strategy $s_{i} \in S_{i}$ is strictly dominated, then it can’t be a best response to any $s_{-i} \in S_{-i}$ .

##### Beliefs
A belief of a player $i$ is a possible profile of opponent’s strategies $s_{-i} \in S_{-i}$ .
We can define a best-response correspondance $BR:S_{-i} \rightarrow \mathit{p}(S_{i})$ that associates every $s_{-i}$ to a subset of $S_{i}$ which contains the best responses to $s_{-i}$ .

##### Nash equilibrium
>[!definition] Nash Equilibrium
>In a n-player game $G = \{S_{1},\dots,S_{n} \,;\,u_{1},\dots,u_{n}\}$, strategies $(s_{1}^*,\dots,s_{n}^*)$ are a Nash equilibrium if, for any $i$, $s_{i}^*$ is the best response of player $i$ to $(s_{1},\dots,s_{i-1},s_{i+1},\dots,s_{n})$ .

This yields from the fact that every player is supposed to be playing their best response.
A nash equilibrium basically occurs when, in the chosen strategy, each player is not incentivized to change their personal choice.

![[Pasted image 20241010114153.png]]

Here (M,R) is a Nash Equilibrirum because when B chooses R, A still prefers M and when A chooses M, B still prefers R.
The result of Iterated Elimination of Strictly Dominated Strategies is a Nash Equilibrium, if there exists any.

##### Weak Dominance
Weak dominance is a relaxation of Strict dominance. We can write it as:

- $u_{i}(s_{i}',s_{-i}) \geq u_{i}(s_{i},s_{-i})$ for every $s_{-i}$
- $u_{i}(s_{i}',s_{-i}) > u_{i}(s_{i},s_{-i})$ for some $s_{-i}$

Without the second condition we only say that $s_{i}'$ dominates $s_{i}$ .

>[!lemma] Lemma
>If every player has a dominant strategy $s_{i}^*$ then a Nash Equilibrium exists and it is $(s_{1}^*,\dots,s_{n}^*)$.

##### Pareto efficiency Vs Nash Equilibrium
- Pareto efficiency: There is no way someone can improve their situation without someone else getting worse.
- Nash Equilibrium: No way to get a better outcome for some player with a unilateral change.

Since we assume players are only drive by egoism, we get Pareto Inefficient Nash Equilibria. We try to estimate how much being egotistical is costing us. Let’s define a Social Costs for this aim:

- Overall welfare: $K(s) = \sum_{j}-u(s_{j})$
- Minmax fairness: $K(s) = max_{j} -u(s_{j})$

The Cost of Anarchy is the ratio between the worst (socially) NE and the best Pareto efficient solution.

$A = \frac{K(s^*)}{min K(s)}$

We can also consider the best (socially) NE and we speak of Price of Stability. 

