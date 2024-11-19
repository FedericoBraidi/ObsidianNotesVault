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
##### Constitution
Given a set of rational preferences $R(A)$, a constitution maps $\geq_{i}=(\geq_{1},\dots,\geq_{n})$ to a unique social preference $f(\geq_{i})$.
With the notation $\geq | \,Y= \,\geq \cap \,(YxY)$ we intend to restric $\geq$ to only subset $Y$ of $A$.
With that said, let’s see some definitions related to constitutions:

- Indipendence of Irrelevant Alternatives (IIA): given two preference profiles $\geq_{i},\geq_{i}'$ and $\forall \,a,b \in A$:
		$$
		\begin{align*}
        \forall \,&i, \succeq_i \mid \{a,b\} = \succeq'_i \mid \{a,b\} \\
        \text{implies}\, &f(\succeq_i) \mid \{a,b\} = f(\succeq'_i) \mid \{a,b\}
        \end{align*}
        $$
    Which basically means that if every player agrees on the relative placement of two options for two different profiles, the constitutions should also agree and ignore any other option.
- Pareto efficiency: a constitution is Pareto efficient if $\forall \,\text{profiles}\, (\geq_{(i)})$ and $\forall \,a,b \in A$:
	$\forall \,i, a\geq_{i}b \quad \text{implies} \quad a\geq b, \text{where} \geq=f(\geq_{(i)})$
	Which means that if everyone prefers $a$ over $b$ then the constitution should also prefer that way.
- Dictatorship: a constitution is a dictatorship if $\exists \,i$ such that:
	$a \geq_{i} b\quad\text{implies}\quad a\geq b, \text{where} \geq =f(\geq_{(i)})$
	So the constitution always mimics the preference of $i$.
- Monotonic: a constitution is monotonic if the fact that someone ranks something better that it had ranked it before doesn’t make it worse in the constitution’s ranking.
- Non-imposition: a constitution satisfies non-impostition if all rational preferences can be outputs.

>[!theorem] Arrow’s theorem
>The existance of a constitution that is non-dictatorship, monotonic and satisfies non-imposition and IIA is impossible.
>In other words, if a constitution is Pareto Efficient and satisfies IIA then it is a dictatorship.
##### Democracies and Elections
By democracy we mean an electoral system where the candidate with the majority of the votes is elected. When there are only two candidates this is easy and it works, when the candidates increase the situation is a bit more compelx.
There can be cases where $A>B,B>C,C>A$ , so what should we choose?
These situations are called Condorcet Cycles, they can also coexist with a winner (i.e. $D>A,B,C$) 
These problems also arise outside of elections:

- Scheduling: $A,B,C$ as users or packages to schedule and the preferences as ways to schedule them.
- Optimization: $A,B,C$ as possible solutions to an optimization problem and voters as possible goal functions.

Let’s see some possible variations of electoral systems to solve the problems:

- Plurality voting: Everyone ranks the candidates and the one with most “number one” preferences wins.
	![[Pasted image 20241016120523.png]]
	Here $A$ wins, but most people prefer $B$ over it, and $C$ as well.
- Two Phase Runoff: We first take the two most voted and the only vote between those two.
	![[Pasted image 20241016120704.png]]
	$A$ and $B$ go to the ballot and $B$ wins, however $C$ is the Condercet winner but does’t even make it to the ballot.
- Borda count: Everyone gives $M$ points to the best candidate, $M-1$ to the second best and so on. Then the one with most points wins. There are problems in this as well. Plus in the case where one candidate retires the order might be completely reversed.
- Approval voting: The voter can give multiple preferences.
- Instant Run-off: We ask each voter for its order of preference, we run subsequient round eliminating every time the one with the least top preferences. This can lead to an increasing consensus resulting in a worse performance.

More problems arise in some cases where, to get a suboptimal solution someone is encouraged to vote differently or against its preference.

>[!definition] Strategy-proof constitutions 
>A constitution is strategy proof if for any profile $\geq_{(i)}$ and a certain preference $\geq_{j}'$:
>$f(\geq_{(i)})\geq_{j}f(\geq_{j}',\geq_{-j})$
>Which means that no one has any incentive on cheating.

>[!theorem] Gibberard-Satterthwaite theorem
>Any strategy-proof constitution that doesn’t forbid anyone to win is a dictatorship.

The best proposed system is Majority Rule, which is defined as:

$a \geq b \iff |\{i: a \geq_{i}b\}|\geq|i:b \geq_{i}a|$

It is Pareto efficient, satisfies IIA and is not a dictatorship. It is not a constitution, because of the existance of Condorcet cycles.
##### Cournot duopoly
Cournot, in 1838, anticipated the concept of Nash Equilibrium for a special case of a duopoly.
Take a certain product that has a duopoly, two firms 1 and 2 produce quantities $q_{1}$ and $q_{2}$ ($Q=q_{1}+q_{2}$) of it, respectively. The cost to produce it is $C(q)=cq$ and the price it is sold at is $P(q)=(a-Q)h[a-Q]$ with $a>q$.
If $1$ and $2$ choose the prices independently can we predict the optimal production? Is there a Nash Equilibrium?

Both firms just need to choose $q_{1,2}$ once and have a space of possibilities that is capped by $a$ since any value higher than it doesn’t make sense, $S_{i}=[0,a[$.
The payoff is just the profit: $u_{i}(q_{i},q_{j})=q_{i}[P(q_{i}+q_{j})-c]=q_{i}(a-q_{i}-q_{j}-c)$

Is there a Nash Equilibrium? This would mean that:
$q_{i}^{*}=argmax_{q_{i}}u_{i}(q_{i},q_{j}^{*})$    and also   $q_{i}^{*}=argmax_{q_{i}} q_{i}(a-q_{i}-q_{j}^{*}-c)$ for $i=1,2$.
The solutions for these are:
$q_{1}^{*}=q_{2}^{*}=\frac{a-c}{3}$     as well as    $u_{1}^{*}=u_{2}^{*}=\frac{(a-c)^{2}}{9}$.

In the case of a monopoly, the only company would produce $\frac{a-c}{2}$ and profit $\frac{(a-c)^{2}}{4}$. In the case of a duopoly they could just divide this production and split the profits, gaining more than in the duopoly case, but this is not a Nash Equilibrium as any of the two is incentivized to produce more.
##### Bertrand duopoly
Bertran argued Cournot’s model saying that companies choose the prices and not the quantities. In this new formulation, strategies are prices.
We can assume that people buy quantity $q_{i}=a-p_{i}$ from the firm with the lower price and $0$ from the other. The NE of this game is $p_{1}^{*}=p_{2}^{*}=c$.
This is not the best outcome either, because the two firms could chose to up the price up to $\frac{a+c}{2}$ and share the market. However this is not a NE either, since they have the incentive to lower the price and get all the market.

We should note that the solution of price = cost is not good since it brings 0 profit.
##### Hotelling model
Take two vendors of the same ice-creams on a $1 \,km$ road. People distribution is uniform and each person buys from the closes vendor. Think of 101 positions $0,1,\dots,100$ .
Suppose one vendor is at 22 and the other at 35, the one at 22 has incentive to move at 36. The only NE is if both are at 50 (political tendency towards middle).
##### The tragedy of commons
This is a way to showcase why when people have access to a common good, they tend to misuse it. The original example is with a set of farmers who have goats that all eat the same green land in the village. The land has a value per goat that goes down with the number of goats total and the NE is an equipartition of the goats.
A single farmer, however, is incentivized to get more goats because the loss in the value per goat of the land is mitigated by the fact that also all other farmers tank it.
This leads each person to have a optimal solution by pushing more than society’s best solution and making everyone else worse by doing so.
##### Selfish routing
Let’s say there are two paths to go from $A$ to $B$. One path has a cost that is linear in the amount of the packet that goes through it $cost = x$ and the other one has fixed cost 1.
The optimal solution (Pareto Efficient) is sending $\frac{1}{2}$ of the packet top ($cost=\frac{1}{2}$) and $\frac{1}{2}$ of the packet down ($cost=1$) with an average cost of $cost=\frac{3}{4}$.
This solution is not a NE though, everyone in bottom lane is incentivized to deviate and go to the top lane. The price of Anarchy is $\frac{4}{3}$.
In other cases where the cost of the top path is exponential we could get an even better performance (for high exponents cost goes to 0) but the cost of Anarchy goes infinite.
##### Mixed strategies
Mixed strategies come into play when a game seems not to have a Nash Equilibrium.
Imagine the Even&Odds game explained with this table:

![[Pasted image 20241022170842.png]]

In this case there is no NE. Let’s try to expand the game with another option:

![[Pasted image 20241022170822.png]]

Here the $(\frac{1}{2},\frac{1}{2})$ strategy looks like a Nash Equilibrium.
To formalize this we define a probability distribution $p$ over a non-empy discrete set $A$ as $p:A\rightarrow[0,1]$ such that $\sum_{x \in A}p(x)=1$.
A mixed strategy is then defined as one where strategies $S_{i}$ are chosen with probability $m_{i}$.
In this case the payoff is just calculated by weighting the payoffs of the strategies with the same coefficients.
So in general the Even&Odds game can be extended by introducing the possibility of choosing a number $q \in [0,1]$ (for player Odd) and a number $r \in [0,1]$ (for player Even) which are the respective probabilities of said players choosing 0.
We can extend the concept of strong or weak domination to $m_{i}$ in an analogous way to before.
It is then possible to prove that if $m_{i}'$ strictly dominates $m_{i}$ we can limit our search to pure strategies of the opponents, indipendently to ours.
To expand the concept of Nash Equilibrium in the case of mixed strategies we can define it as a situation in which: $u_{i}(m)\geq u_{i}(m_{i}',m_{-i}) \quad\forall\,m_{i}'$
Now the IESDS technique can be expanded to using a strictly dominant mixed strategies.

>[!theorem] NE and mixed strategies
>Given a game $G$ and a joint mixed strategy $m$, the following are equivalent:
>- $m$ is a Nash Equilibrium
>- For each $i$:
>	$u_{i}(m)=u_{i}(s_{i},m_{-i})$ for every $s_{i} \in support(m_{i})$
>	$u_{i}(m)\geq u_{i}(s_{i},m_{-i})$ for every $s_{i} \notin support(m_{i})$

##### Nash Theorem
>[!theorem] Nash Theorem
>Every game with finite strategies $S_{i}$ has at least one Nash Equilibrium, possibly involving mixed strategies.

This results from the fact that we can write player moves as mixed strategies. In the case of a 2 strategy game for 2 players we have a function for player $A$ with parameter $q$ and a function for player $B$ with parameter $r$. This creates two “Best Response” functions, one for each player, that both depend on $q$ and $r$ together, because they incorporate the beleifs. Since they are two functions in the $q,r$ space on two closed intervals ($[0,1]$) we have, by Weierstrass, that they meet in at least one point.
##### Fictitious play
In these kinds of games, regrets play a role into next decisions. 
Such behaviour arises because player $i$ thinks the strategy of $-i$ is fixed and answers to that fixed play (what he has already seen). This is good if the strategy of the opponents is indeed fixed but bad otherwise.
With this type of strategy we are not guaranteed to finish in a Nash Equilibrium. However this happens for some relevant cases such as Potential Games, Games that are solved by IESDS and others.
##### Potential Games
In the context of these games we define Potentials:

>[!definition] Exact Potential
>Given a game $G$ with joint strategies $S$, function $\Omega:S\rightarrow \mathbb{R}$ is an exact potential if:
>$\Omega(s_{i}',s_{-i})-\Omega(s_{i},s_{-i})=u_{i}(s_{i}',s_{-i})-u_{i}(s_{i},s_{-i})=\Delta u_{i}$

>[!definition] Weighted Potential
> $\Omega:S\rightarrow \mathbb{R}$ is an weighted potential with weight vector $w=\{w_{i}>0\}$ if:
> $\Omega(s_{i}',s_{-i})-\Omega(s_{i},s_{-i})=w_{i}\Delta u_{i}$

>[!definition] Ordinal Potential
>  $\Omega:S\rightarrow \mathbb{R}$ is an ordinal potential if:
>  $\Omega(s_{i}',s_{-i})>\Omega(s_{i},s_{-i})\iff u_{i}(s_{i}',s_{-i})>u_{i}(s_{i},s_{-i})$

These kinds of games have many properties:

- If a game has an ordinal potential $\Omega$ then the game $G=\{S_{1},\dots,S_{n};u_{1},\dots,u_{n}\}$ has the same NE as the game $G'=\{S_{1},\dots,S_{n};\Omega,\dots,\Omega\}$. This means that all players want to maximize the potential and a multiplayer game transforms into a single goal optimization.

>[!theorem] Potential Games Theorem
>Every finite ordinal potential game has a Nash Equilibrium and it can be found deterministically.

This follows from the property above since each player plays to increase its $u$ function but at the same time also increases $\Omega$.

There are particular subsets of potential games, such as:

- Congestion games: games which involve the choice of the least congested resource. For each potential game, there is one congestion game with same potential function.
- Coordination games: games where players are required to act together. They give higher payoff to players when they make same (coordination) or opposite (anti-coordination) moves. Coordination games are not exactly potential games, but every potential game can be written as the sum of a coordination game and a Dummy game (a game where the payoff of player $i$ only depends on the moves of other players $u_{i}(s_{i}',s_{-i})=u_{i}(s_{i},s_{-i})$. 
##### NE complexity
Unfortunately, even if we know that a NE always exists, it is generally complex to find.
It has been demonstrated that the problem of finding the NE is PPAD-complete (Polynomial Parity Arguments on Directed graphs). The class of PPAD problems is computationally between P and NP, so the fact that the NE problem is in this category means that it is computationally hard, unless P=NP.

The PPAD class contains problems that are equivalent to the End-of-Line problem: Take a directed graph with one unbalanced node. There must be another unbalanced node. Find it.
This has a solution but finding it without traversing all the graph is not easy.

![[Pasted image 20241024161316.png]]

The NE problem is the same as finding a specific point of the BestResponse function which can be demonstrated to be equivalent to finding the End-of-Line in a directed graph.
In case we can’t find a good way to find a NE we can also relax the requirements by allowing arbitrarily small improvements.
##### Dynamic games
Contrarily to how static games work, dynamic games imply that the players move one after the other. Complete information still means that every player knows the payoff function and knows that everybody else knows. The concept of information can now be expressed in two ways: 

- Perfect Information: Every player can make a decision with full awereness. 
- Imperfect Information: Some of the decisions are simultaneous.

Instead of representing these games with a table of the payoffs, we know use trees bacause we need to unfold the time dimension. We number the nodes in the tree following BFS.

![[Pasted image 20241107144341.png]]

In this example of a battle of sexes-like game, B being at $x_{1}$ or $x_{2}$ shows the difference in information it has when having to decide himself. We say the info he has is a singleton $\{x_{1}\}$ if it knows in which node it is. If B has no knowledge of the choice of A, its info is not a singleton, but something like $\{x_{1},x_{2}\}$.

If the game has perfect info, all information sets for all players are singletons and there are no “Nature” moves. With imperfect information there are either (or both) information sets with multiple nodes, or choices of Nature. In the second setting, players need to form beliefs.

Strategies are now updated to be a list of responses to all possible plays of the game, like in an algorithm.
What happens if the strategy of a person is a mixed strategy and not a deterministic one? We should decide the random part at the beginning and then move deterministically. To try  to avoid this we use Behavioral strategies which specify, for each information set, a different probability distribution over the actions.

It can be proven that Behavioural strategies are analogous to Mixed strategies if there is Perfect recall. I.e. every player never forgets the info it has learned.

Actually we can always represent the trees as tables, like we used to do. This is done by defining the strategies of the second player as behaviourla strategies and checking all possible cases:

![[Pasted image 20241107164024.png]]
##### Dynamic Nash Equilibrium
The above representation of dynamic games is useful in trying to find Nash equilibriums, which are inherently static. We can identify 3 in the above example:

- (R,rr)
- (R,rs)
- (S,ss)

Given a joint profile of behavioural strategies, an equilibrium path contains the nodes that are traversed by the profile.
##### Solution of dynamic games with perfect/imperfect information
It can be demonstrated that any such game has a backwards induction method to solving it, which is unique if all end payoffs are different. This is true because the last choice is deterministic, so the second to last is as well, and so on.

The same can be said with imperfect information by imagining a game where two players play, then two other players play knowing the first two moves but not each other’s.
##### Subgames
We define a subgame as a game that contains a certain node of the tree of the original game, together with all of its successors.
##### Subgame-perfect Nash Equilibrium (SPE)
A Nash Equilibrium is Subgame-perfect if the strategies chosen give a NE in every subgame. It is provable that every finite extensive form game has a Subgame-perfect NE.
For finite horizon games with perfect information, SPE is found with backwards induction. This can be extended to other types of games if we account for Credibility of Threats.
##### Maximin and Minimax
In a two player game with players $i$ and $-i$ we can define two values:

- Maximin: the maximum of payoff obtainable by player $i$ when player $-i$ does the worst move for $i$ ($i$ moves before $-i$). This can be written as:
	$w_{i}=max_{s_{i} \in S_{i}} min_{s_{-i} \in S_{-i}} u_{i}(s_{i},s_{-i})$
- Minimax: the minimum guaranteed payoff of player $i$ in case it moves after player $-i$. This can be written as:
	$z_{i}=min_{s_{-i}\in S_{-i}}max_{s_{i} \in S_{i}} u_{i}(s_{i},s_{-i})$

Some things to note are:

- For every player $i$ the $maxmin_{i} \leq minmax_{i}$
- If a joint strategy $s$ is a Nash Equilibrium, then for every player $i$ $minmax_{i} \leq u_{i}(s)$
##### Zero-sum games
Zero-sum games are those games that, for each joint strategy $s$, have $u_{i}(s)=-u_{-i}(s)$. These games are a special case of the more general class of Competitive/Adversarial games where the utilities of the two players are in some way inversely correlated.

Zero-sum games are more simple to study and have nicer properties. For example we have the MinMax Theorem which states:

>[!theorem] Minmax Theorem
>Given a Zero-sum game $G$ with finitely many strategies:
>- $G$ has Nash Equilibriums if and only if $maxmin_{i}=minmax_{i}$ for every $i$.
>- All NEs yield the same payoff ($maxmin_{i}$).
>- NEs have the form $(s_{i}^{*},s_{-i}^{*})$ where $s_{i}^{*}$ is a security strategy ($maxmin$ move)

Additionally, since the game is Zero-sum, it is enough to check $minmax=maxmin$ for one of the players.
Another cool property of these games is that $minmax_{i}=-maxmin_{-i}$ and viceversa. The amount of $minmax_{i}=maxmin_{i}$ is called the value of the game. A NE is called a saddle point.
##### Mixed security strategies
Consider a two player game as before. Also consider that player $-i$ is playing the mixed strategy which is worst for player $i$, then $i$ responds with the mixed strategy that has best return and gets the mixed security payoff, written as: 

$max_{m_{i}\in \Delta S_{i}}min_{m_{-i}\in \Delta S_{-i}}u_{i}(m_{i},m_{-i})$

This is in the case that $i$ moves first. In the other case, we have the minimax which is the lowest payoff obtainable when playing the best move for one-self:

$min_{m_{-i}\in \Delta S_{-i}}max_{m_{i}\in\Delta S_{i}}u_{i}(m_{i},m_{-i})$

In these cases minimax and maximin always exist and are equal.
The MinMax Theorem can be extended to mixed strategies as well.
##### Stackelberg games
Sequential games where one (leader) always moves first and another (follower) always second. The outcome achieved with backwards induction is called Stackelberg Equilibrium.

In this setups the leader always has a payoff which is greater or equal to that of the NE, while the follower has always a payoff greater or equal than the minimax.
In Zero-sum cases, the leader having first-move advantage means that the follower is worse right at the start. This is true even if the follower has more information.
##### Time inconsistencies
Suppose a player has a certain amount of a resource $K=1$. The player has to allocate this resource to $N$ steps. For this example we take $N=3$. We define a term $\delta$ called discount, such that the total payoff is calculated as:

$v(x_{1},x_{2},x_{3})=u(x_{1})+\delta u(x_{2})+\delta^{2}u(x_{3})$

The the problem is:

$max \, v, \,\,\, such \,\,that \,\,x_{1}+x_{2}+x_{3}=1$

Let’s make an example with $u(x)=\log(1+x)$. By taking the derivative of $v(1-x_{2}-x_{3},x_{2},x_{3})$ and setting it to 0 we get formulas for $x_{i}$. After doing this we can calculate, based on the value of $\delta$, the optimal solution to the problem.
In this case (exponential discount) it can be verified that the choice is consistent, since after step one, the subproblem that starts from time step 2 and has resource $K-x_{1}$ produces the same values for $x_{2}$ and $x_{3}$. 

It is not always like this, for example in the case of constant discount, at step 2 you don’t have the incentive to still split in the same way as you did in step one because now $x_{2}$ is not discounted anymore. This is a fight between player of step 1 and the same player at step 2. 
A fully rational player of step 1 will know that himself at step 2 will not want to split the remaining resource with step 3 and keep it to himself. Knowing this (with backwards induction), player 1 uses up all the resource himself.

All this is to motivate the use of only exponential discounts because other versions lead to time inconsistencies.
##### Multi stage games
Normal form games describe situations where players play simultaneously, extensive form games add the time dimension and payoffs are only given at the end nodes. 
Many real life cases have intermediate steps with partial payoffs.

We define multi stage games as a finite sequence of normal form stage games (complete but imperfect information games, simultaneous). The final payoff of a multi stage is determined by the results of the stage games that compose it.

For example we can model a simple multistage game as being composed of two stage games with same players but different action sets. Each game gives a partial payoff and the total payoff is the discounted sum of the partials.
An example of this is the Prisoner-Revenge game, where the first round is a Prisoner Dilemma. Then, when they’re out of jail they can choose to either join a Gang or remain a Loner. If they both join a gang they fight and have negative payoff each, if they both are loners, they never meet again, 0 payoff each, lastly if one joins and the other doesn’t, the one who joins has slightly negative payoff and the other highly negative.

A specific strategy specifies a move for the first game and a set of moves for the second, each conditional on the first move of the enemy.
As we have seen, a SPE is a joint strategy that ensures that a NE is played in every subgame. If we define $s_{j}^{*}$ a NE for the j-th stage, then there exists a SPE whose equilibrium path is $s_{1}^{*},s_{2}^{*},s_{3}^{*},\dots,s_{T}^{*}$ .

So two SPE in the Prisoner-Revernge game are:

- Playing F in the first game and then Gang no matter what
- Playing F in the first game and then Loner no matter what

As we can see we’re just playing the stages independently with no strategic connection, is there an alternative with strategic connection? We need to do backwards induction and start from the end of the game.

Note that any NE $s^{*}$ of a multistage game $G_{1},G_{2},\dots,G_{T}$ requires that a NE is played in game $G_{T}$.
Furthermore note that if all stage games $G_{i}$ have unique NEs, then the multistage game has a unique SPE.

These imply that if the $T$-th stage has more than one NE, then SPEs for the whole game exist where a NE is not played at every game. This is possible thanks to the discounted nature of total payoff.

For example, the strategy “I play mum, then if opponent also does, I play loner, otherwise gang” is a SPE if the discount factor is high enough.
In stage 1 $u_{1}(M,s_{2})=4+0\delta$ and $u_{2}(F,s_{2})=5-3\delta$ so $M$ is the right move (best response to $s_{2}$ if $\delta \geq \frac{1}{3}$)
For the second player this is $u_{2}(s_{1},m)=-1+0\delta$ and $u_{2}(s_{1},f)=1-3\delta$, so the play is fine for player 2 as well, if $\delta \geq \frac{2}{3}$

Ok, but what happens in the case of games with more than two stages? Because here we could only deviate in the first stage since the second was also the last one. Maybe, in cases with more stages one can deviate in one stage and then get back on track in another.

As we will see, this is not the case, because of the One-stage deviation principle. Let’s define a strategy $s_{i}$ as optimal if it can’t be beaten by changing an arbitrary amount of choices inside of it. Let’s also define a strategy $s_{i}$ as one-stage unimprovable if it can’t be beaten by any strategy that deviates from it by only one stage’s decision.

If we say that our strategy is not optimal for some information set $h_{i}$, we are implying:
$u_{i}(s_{i}',h_{i})> u_{i}(s_{i},h_{i})$. Denying this, instead, can be the definition of optimal/one-stage unimprovable based on the constraints placed on $s_{i}'$.

Clearly, being optimal also means being one-stage unimprovable, but the inverse is also true.
This can be proven by contradiction. Assume $s_{i}$ is one-stage unimprovable but not optimal, then $s_{i}'$ exists such that it is better and deviates in 2 or more stages. Let’s take the last of the differentiations and study the subgame that starts at that point. Since the single games are independent, if I deviate there and get an improvement, it means that I would have gotten an improvement anyways by deviating in that way, even if not deviating prior to that. This way, from a better strategy that deviates $N$ times we get a better strategy that deviates only once, which is impossible because the original strategy is one-stage unimprovable.
##### Repeated Games
A repeated game is a multi-stage game where the same static game $G$ is played $T$ times and the final payoff is the discounted version of the single payoffs.
For example a two-stage Prisoner’s Dilemma:

![[Pasted image 20241119112515.png]]

As we can see the right strategy is to play (F,F) twice.
From this we can see that if game $G$ has only one NE $s^*$ then the SPE for the whole game is to play $s^{*}$ in every game.
In fact, these types of games are not very interesting. In games with multiple NE one can deviate from playing NE at each stage and get better results. 

![[Pasted image 20241119125637.png]]

In this game, for example the players play (H,H) which is a NE only if at the first round they played (M,M) whichi is not a NE. Otherwise, they play (F,F) which is still a NE. In this setting playing (M,M)$\rightarrow$(H,H) is best, so we deviate from playing always a NE. These type of games encourage a type of cooperation.
We can see this even better in an extended game such as this:

![[Pasted image 20241119130940.png]]

In the case seen before, one can threaten the other that, if they both play M, then they can both play H and get 7 otherwise if the adversary doesn’t play M, F will be played at the second round and the adversary will be penalized. This threat doesn’t hold because playing F is against the interest of both players. 
If we add strategies that encourage one play to retaliate, such as P and Q, cooperation is much more stable.
##### Infinitely repeated games
Infinitely repeated games are represented as $G(\infty,\delta)$ with $\delta<1$ and can’t be solved through backwards induction because there is no “last stage”.
In these games, surprisingly, there are SPE where not even one played strategy is a NE of the stage game.
Let’s take the infinite version of the repeated Prisoner’s Dilemma.
For example one of these is if both player play M as the first move and then continue playing M if all past games ended in (M,M) and plays F otherwise, this is called the Grim Trigger strategy.
This strategy is a NE because the Grim Trigger is the best response to itself. This is because, if Bob thinks Al is playing this strategy he knows that whenever the outcome is not (M,M), Al is gonna play F forever, so his best response is also to play F if the outcome is not (M,M).
We just need to calculate the best first move for Bob which depends on $\delta$. Doing the calculations tells us that M is the best move for $\delta \geq\frac{1}{4}$.

This is a SPE because it is a NE in every subgame, if they play (M,M) all the time every subgame is the same as the total game (so the GrimTrigger is a NE), if they deviate once for every subgame after the deviation they play (F,F) which is a NE for the Prisoner’s Dilemma.
##### Friedman Theorem
First of all we defina feasible payoff of a game as any linear combination of the pure payoffs with normalized weights.

>[!theorem] Friedman Theorem
>Let $G$ be a finite static game of complete info. Let $(e_{1},e_{2},\dots,e_{n})$ be the payoffs from a NE of $G$. Let now $(x_{1},x_{2},\dots,x_{n})$ be feasible payoffs such that $\forall j,x_{j}>e_{j}$.
>If $\delta$ is close enough to 1, $G(\infty,\delta)$ has a SPE with payoffs $x_{j}$.

The Grim Trigger strategy doesn’t always work for all games and is not always the best. For example another strategy arises from the idea that keeping a grudge forever might not be the best decision. The strategy that employs this idea is called Tit for Tat, in a game where there are two strategies (cooperate or defect) player $i$ at step $t$ plays whatever player $-i$ has played at step $t-1$. This punishes immediately deviations but also forgives fast (has only 1 step memory).

Tit for Tat is good but has some evidently bad matchups, such as another Tit for Tat player shifted by 1 move: this triggers a Death Spiral.
Another possibility is the Tit for two Tats where the first defection is forgiven and only the second is punished. This avoids Death Spiral but is bad against aggressive players.
##### Reputation
Let’s imagine a game with two players $A$ and $B$. $A$ can either trust $B$ or not, if it doesn’t, they both get 0, if it does, then $B$ can cooperate to make both get 1 or defect to get 2 and make $A$ get -1. From backwards induction we know that $B$ will defect so $A$ plays NotTrust.

What if the game is repeated? We can build a Grim Trigger by just making $A$ play Trust if all previous outcomes were (T,C) and NotTrust forever otherwise. The best response for $B$ is to always play C as long as $\delta \geq \frac{1}{2}$.

We can extend this with a Guarantor, which (if called) increases the probability that $A$ chooses to Trust $B$. $G$ requests to be given a guarantee of 2, then if $B$ defects, it keeps it, otherwise it only keeps 0.1 of it. Then the game becomes:

![[Pasted image 20241119191921.png]]

In this case if the increase in probability is good enough, $B$ is even more incentivized to Cooperate. But why doesn’t $G$ just keep the 2? If you do a single round there is no incentive not to keep it. If the game is repeated and $\delta \geq 0.95$ cooperation is better in the long run, since $G$ also builds the trust of $B$.
