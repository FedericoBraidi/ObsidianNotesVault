#### Entropy
Let’s first try to give a definition of information. When we deal with random events, the outcomes are, obviously, unknown beforehand. We can define information as the quantity of uncertainty we have before knowing the outcome, or the quantity of knowledge we gain by getting to know the outcome.

Let’s take an example of the toss of two independent coins. We identify 3 objects :

- Sample Space : $\Omega = \{H H, H T, T H, T T\}$, it is the set of possible results.
- Family of events : All the possible responses to a question about the outcome, i.e. all subsets of $\Omega$: $\mathcal{F}=\{\{H H\},\{HT\},\{TH\},\{TT\},\{HH,HT\},$$\{H H,TH\},\{HH,TT\},\{HT,TH\},\{HT,TT\},\{TH,TT\},\{H H,HT,TH\},$$\{HH,TH,TT\},\{HH,HT,TT\},\{HT,TH,TT\},\{HH,HT,TH,TT\}\}$
- A Probability function : Gives a probability to every element of $\mathcal{F}$ : 
	$P(\{H H\})=\frac{1}{4}$
	$P(\{H H,HT\})=\frac{1}{2}$
	$P(\{H H,HT,TT\})=\frac{3}{4}$
	$P(\{H H,HT,TH,TT\})=1$

Intuitively, an outcome like $\{H H\}$ gives us much more information (we know the exact output) than something like $\{H H,HT,TT\}$ . 
To formalize this intuition, we want to define a function $I:\mathcal{F}\rightarrow \mathbb{R}^+$ that gives us the amount of information associated to a given event. We want $I$ to be such that:

- Events with the same number of outcomes give the same information (i.e. $\{H H\}$ gives the same information than $\{H T\}$).
- Event with more outcomes give less information w.r.t. those with few outcomes (the event with 4 outcomes gives 0 information).
- The information given by two subsequent independent events is the same given by the combined event (i.e. having first the info that the first coin is $H$ and then the info that the second coin is $H$ is the same as having the info that we got 2 $H$).

Which can be translated to:

- $I$ should be a function of the probability of the events: $I=f \,\circ\,P$ .
- $f$ should be a monotonically decreasing function of $P$ .
- $f$ should be such that $f(P(A)P(B))=f(P(A))+f(P(B))$ .

It is trivial to note that a simple function that has these properties is the negative logarithmic function.

$I:\mathcal{F}\rightarrow\mathbb{R}^+\,,\,I(E)=-\alpha \log(P(E))$

The constant only specifies the unit of measurement for the information, since:

$\alpha=\frac{1}{\log(\beta)}\Rightarrow-\alpha \log(P)=\log_{\beta}(P)$

If we choose $\beta=2$ the unit is called bit, while with $\beta=e$ it is called nat.

Before we said that if we have independent events, the information given by the intersection of the events is the sum of the informations of the events singularly. If they aren’t independent, this does not hold, because part of the information given by one event is also already counted in the information given by the other.
We define a conditional version of the information function, simply by not using the original probability in the $\log$ but the conditional probability to the already seen event.

#### Probability Tree
The elements of $\mathcal{F}$ can be arranged in a tree-like structure in various ways. In these kinds of representations:

- Nodes represent the events (answers to a question about the outcome). We associate to it the probability of that outcome.
- Edges represent a question that further splits the possibilities such that : $\bigcup_{k} E_{k}=E$. We associate to it the probability of the child outcomes given the parent.
- At each depth the nodes make up a partition of $\Omega$.

#### Average Information (Shannon Entropy)
In the example of the two coins, we need two bits of information to specify a certain output, one to “ask” wether the first coin was head or tails, one for the second coin.

If we have a urn with 50% red balls, 25% green, 25% blue and we extract one, to get to the right outcome we could need only 1 bit (case of red ball, 50%) or 2 bit (other 2 cases, 25% each). On average we would need $1*\frac{1}{2}+2*\frac{1}{4}+1*\frac{1}{4}=1.5$ bits.
In a case with red (50%) and 4 more at 12.5% we would need 2 bits.
We can generalize this by defining an average information :

$\langle I \rangle =-\sum_{a\in\Omega} P(\{a\})\log(P(\{a\})) = H$

This quantity is called Shannon Entropy.
Let’s see some of its properties:

- Shannon Entropy is non-negative, it is zero only if there is one outcome with probability 1.
- $H[X]$ is maximized if $p_{X}(i)=\frac{1}{N} \,\forall\,i$
- $H[X]$ is concave with respect to $p$.
	A function $f$ is concave when:
	$f[\lambda x_{1}+(1-\lambda)x_{2}]\geq \lambda f[x_{1}] +(1-\lambda)f[x_{2}]$
	And, indeed:
	$H(\lambda p+(1-\lambda)q)\geq \lambda H(p) +(1-\lambda)H(q)$

#### Kullback-Liebler Divergence
This introduces relative entropy. Take two random variables $X_{1}$ and $X_{2}$ with values in the same space and probabilities $p$ and $q$. The K-L Divergence is defined as:

$D_{KL}[X_{1}||X_{2}]=D_{KL}(p||q)=\sum_{i} p_{i}\log(\frac{p_{i}}{q_{i}})$

And it satisfies Gibbs’ inequality:

$D_{KL}(p||q)\geq 0$

With equality only if $p=q$.

The Kullback-Liebler Divergence can be thought of as a distance in the space of probability distributions, or a value for the difference between two distributions. It is not a proper distance because it is not simmetric.
It is useful to note that the $D_{KL}$ is convex with respect to both its arguments. The proof of this is found using Jensen’s inequality and is in the course notes.

We can write the Shannon Entropy in terms of $D_{KL}$ .

$H[X] = H(p) = - \sum_{i} p_i \log p_i = - \sum_{i} p_i \log \left( p_i u_i \cdot \frac{1}{u_i} \right)$$= -D_{\text{KL}}(p \,||\, u) - \sum_{i} p_i \log u_i = \log N - D_{\text{KL}}(p \,||\, u)$

Since we have alrerady seen that $D_{KL}$ is convex, we can now demonstrate the convexity of $H$ .

$H(\lambda p + (1 - \lambda) q) = \log N - D_{\text{KL}}(\lambda p + (1 - \lambda) q \,||\, u)\geq$

$\log N - \lambda D_{\text{KL}}(p \,||\, u) + (1 - \lambda) D_{\text{KL}}(q \,||\, u)= \lambda H(p) + (1 - \lambda) H(q)$

#### Joint entropy
Given two random variable $X$ and $Y$ we can define their joint entropy as the Shannon Entropy of their joint probability distribution $p_{X,Y}(x,y)$. Defining $p_{i,j} =p_{X,Y}(x_{i},y_{j})$:
	$H[X,Y]=H(p_{X,Y})=-\sum_{i,j}p_{i,j}\log(p_{i,j})$

Consider now the marginal distributions $p_i \equiv p_X(x_i)$ and $q_j \equiv p_Y(y_j)$.

	$H[X] = - \sum_{i} p_i \log p_i, \quad H[Y] = - \sum_{i} q_i \log q_i$

Assume we obtain $X = x_i$ . As a result, $Y$ assumes values $y_j$ with the conditional probabilities:

	$p_{Y|X}(y_j | x_i) = \frac{p_{i,j}}{p_i}$

The conditional entropy of $Y$ given $X = x_i$ is expressed by:

	$H[Y | x_i] = - \sum_{j} \frac{p_{i,j}}{p_i} \log \frac{p_{i,j}}{p_i}$

On average over all possible results of $X$, the conditional entropy of $Y$ given $X$ is:

	$H[Y | X] = \sum_{i} p_i H[Y | x_i] = - \sum_{i} p_i \sum_{j} \frac{p_{i,j}}{p_i} \log \frac{p_{i,j}}{p_i}$
	
Then we obtain the following chain rule:
	$-\sum_{i,j} p_{i,j} \log p_{i,j} = - \sum_{i,j} p_{i,j} \log \left( p_i \cdot \frac{p_{i,j}}{p_i} \right) = - \sum_{i,j} p_{i,j} \log p_i - \sum_{i,j} p_{i,j} \log \frac{p_{i,j}}{p_i}$
	$= - \sum_{i} p_i \log p_i - \sum_{i,j} p_{i,j} \log \frac{p_{i,j}}{p_i} = - \sum_{i} p_i \log p_i - \sum_{i} p_i \sum_{j} \frac{p_{i,j}}{p_i} \log \frac{p_{i,j}}{p_i}$
	$= - \sum_{i} p_i \log p_i - \sum_{i} p_i H[Y | x_i] = H[X] + H[Y | X]$

Which can be written as:

	$H[X,Y]=H[X]+H[Y|X]$

#### Mutual Information
The mutual information between $X$ and $Y$ is defined as:

	$I[X:Y]=H[X]+H[Y]-H[X,Y]=H[Y ]-   H[Y |X] = H[X] − H[X|Y]$

We can write:

$H[X] = H[X | Y] + I[X : Y], H[Y] = H[Y | X] + I[X : Y]$

So, the mutual information $I[X : Y]$ can be interpreted as the information that $X$ provides about $Y$, and vice versa. The total information needed to specify $X$ is given by the information provided by $Y$, $I[X : Y]$, plus the residual information $H[X | Y]$, and similarly for $Y$.

The mutual information can also be written as the Kullback-Leibler divergence between the joint distribution and the product of the marginals:\

$I[X : Y] = D_{\text{KL}}(p_{i,j} \,||\, p_i q_j) \geq 0$

With equality only if they are independent.