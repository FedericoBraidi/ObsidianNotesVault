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
##### Probability Tree
The elements of $\mathcal{F}$ can be arranged in a tree-like structure in various ways. In these kinds of representations:

- Nodes represent the events (answers to a question about the outcome). We associate to it the probability of that outcome.
- Edges represent a question that further splits the possibilities such that : $\bigcup_{k} E_{k}=E$. We associate to it the probability of the child outcomes given the parent.
- At each depth the nodes make up a partition of $\Omega$.

##### Average Information (Shannon Entropy)
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

##### Kullback-Liebler Divergence
This introduces relative entropy. Take two random variables $X_{1}$ and $X_{2}$ with values in the same space and probabilities $p$ and $q$. The K-L Divergence is defined as:

$D_{KL}[X_{1}||X_{2}]=D_{KL}(p||q)=\sum_{i} p_{i}\log(\frac{p_{i}}{q_{i}})$

And it satisfies Gibbs’ inequality:

$D_{KL}(p||q)\geq 0$

With equality only if $p=q$.

The Kullback-Liebler Divergence can be thought of as a distance in the space of probability distributions, or a value for the difference between two distributions. It is not a proper distance because it is not simmetric.
It is useful to note that the $D_{KL}$ is convex with respect to both its arguments. The proof of this is found using Jensen’s inequality and is in the course notes.

We can write the Shannon Entropy in terms of $D_{KL}$ using an auxiliary random variable with probability uniform $u$.

$H[X] = H(p) = - \sum_{i} p_i \log p_i = - \sum_{i} p_i \log \left( p_i u_i \cdot \frac{1}{u_i} \right)$$= -D_{\text{KL}}(p \,||\, u) - \sum_{i} p_i \log u_i = \log N - D_{\text{KL}}(p \,||\, u)$

Since we have alrerady seen that $D_{KL}$ is convex, we can now demonstrate the convexity of $H$ .

$H(\lambda p + (1 - \lambda) q) = \log N - D_{\text{KL}}(\lambda p + (1 - \lambda) q \,||\, u)\geq$

$\log N - \lambda D_{\text{KL}}(p \,||\, u) + (1 - \lambda) D_{\text{KL}}(q \,||\, u)= \lambda H(p) + (1 - \lambda) H(q)$

##### Joint entropy
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
##### Mutual Information
The mutual information between $X$ and $Y$ is defined as:

	$I[X:Y]=H[X]+H[Y]-H[X,Y]=H[Y ]-   H[Y |X] = H[X] − H[X|Y]$

We can write:

$H[X] = H[X | Y] + I[X : Y], H[Y] = H[Y | X] + I[X : Y]$

So, the mutual information $I[X : Y]$ can be interpreted as the information that $X$ provides about $Y$, and vice versa. The total information needed to specify $X$ is given by the information provided by $Y$, $I[X : Y]$, plus the residual information $H[X | Y]$, and similarly for $Y$.

The mutual information can also be written as the Kullback-Leibler divergence between the joint distribution and the product of the marginals:

$I[X : Y] = D_{\text{KL}}(p_{i,j} \,||\, p_i q_j) \geq 0$

With equality only if they are independent.

#### Information Compression
Information theory was initially developed for telecommunications. In this field, when a communication happens, a message needs to be sent from a $sender$ to a $receiver$ through a $channel$. Before sending the message it should be transformed into a format that is suitable to be sent, this process is called $encoding$, later the $receiver$ retrieves the message through a process called $decoding$. 
The $encoding$ part needs to satisfy two requirements:

- Allow faithful reconstruction.
- Make the message as short as possible (compatibly with point 1).

We need to transform a message into a string of bits and, since we want the function that maps a message to its representation to be injective (for reconstruction), in a string of bits of length $K$ we can only represent $2^{K}$ different messages. If we want to be able to encode a set of messages $A_{X}$ with dimension $|A_{X}|$ we need:

$K \geq \log_{2 }|A_{X}|$

Otherwise we need to get into 2 other paradigms:

- Lossy coding: We tolerate that 2 or more points are mapped to the same encoding but we demand that it happens infrequently.
- Lossless coding: In this case we allow codes of variable length instead of fixed length codes.
##### Lossy Coding

An example of lossy coding is the transmission of the outcome of the toss of an unfair coin with $P(H)=\frac{9}{10}$. In this case we need 1 bit to trasmit the outcome so that the “bit content” of the random variable is $H_{0}[X]=1$ but at the same time, its Shannon Entropy is $H[X]=-\frac{1}{10}\log(\frac{1}{10}) -\frac{9}{10}\log(\frac{9}{10})=0.47$, which suggets us that a more compressed way to convey the information exists.
Since we cannot use less than a bit, this better way emerges when we want to send more outcomes at once. For example the random variable $X^{(3)}$ is the one associated with the possible outcomes of 3 tosses and has 8 possible values. The bottom 4 possible values have a combined probability of occurring which is of $\frac{28}{1000}$.
By only discarding these rare cases we can reduce the number of needed bits from 3 to 2.

In this case it is useful to define an error probability which is just the probability of the reconstructed output not to be the original input:

$P_{e}=P[X' \neq X]$

In our case it is exactly $\frac{28}{1000}=2.8\%$.
We can define this more generally as:

>[!definition] Essential bit content
>Given a random variable X and a tolerated error $\delta$, we define a subset of all possible value of X $S_{\delta}$ as:
>
>$S_{\delta}=argmin_{|S|}(S \subset A_{X} \,|\,P(X \in S) > 1-\delta  )$
>
>Which is the smallest subset of the possible outcomes that collectively have more than $1-\delta$ probability of happening.
>That being said, the essential bit content is the number of bits needed for encoding the information of the outcome of the random variable if we tolerate an error probability of $\delta$. This is calculated as:
>
>$H_{\delta}[X]=\log(S_{\delta}[X])$

It has been proven that when encoding a long sequence of outcomes $X^{(n)}$ for $n \rightarrow \infty$,
the amount of bits needed tends to $n\cdot H[X]$, where $H[X]$ is the Shannon Entropy, even when $\delta$ is arbitrarily close to 1.
This is expressed in the following theorem.

>[!theorem] Shannon’s source coding theorem
>$\forall \delta > 0 , \forall \beta>0,\exists \,n_{0}$ such that $\forall n>n_{0},\,\,|H_{\delta}(X^{(n)})-nH|<n\beta$

This is a result of the fact that, for large n, the probability of a sequence to be a typical sequence (i.e. a sequence that exactly respresents the probability distribution of the random variable) tends to 1. 
##### Lossless coding
Generally for lossy coding, very high number of trials are needed to achieve both efficient compression and low error probability. This comes from the fact that lossy coding uses fixed-length strings, while using variable length ones can help up immensely.
In lossless coding, the mapping function maps values to  $B^{+}=B^{1} \,\cup\,B^{2}\,\cup\,B^{3}\,,\dots$
For example, in the case of the coin toss, we can map $h\rightarrow 0\in B^{1}$ and $t\rightarrow 101\in B^{3}$.
We want symbols to satisfy the following properties:

- They should be invertible (unique).
- Decoding should be easy.
- Their length should be minimal. $L[E,X]=\sum_{i}p_{i}l_{i}$ should be minimized.

The first requirement is not trivial, the encoding we proposed before is unique in the fact that the 0 between the two ones cannot be misread as the 0 that represents heads. Other simpler encodings do not have this property, for example using 1 and 2 bit strings on a random variable with 4 values (0,1,00,11).

For the second requirement, we can solve it by imposing that no sequence is a prefix of another sequence. This yields that we know immediately upon receiving the last bit of a sequence, that the sequence has ended. For example, the encoding proposed before would not be easy (immediate) to decode if $h\rightarrow 1$ instead of 0.
Codes where no codeword is a prefix to another, are called prefix codes.

Prefix codes are in a 1 to 1 correspondance with complete binary trees. Take for example a random variable $X\in\{0,1,2,3\}$ with $p_{X}=\{\frac{1}{2},\frac{1}{4},\frac{1}{8},\frac{1}{8}\}$, the associated tree is the following image:

![[Pasted image 20241226115058.png]]

By just following assigning a 0 or 1 to any branch after a split, we actually create a prefix code. The average length of the prefix code is the average length of the tree, which is: $L[E,X]=\sum_{i}p_{i}l_{i}=\frac{1}{2}\cdot1+\frac{1}{4}\cdot2+\frac{2}{8}\cdot3=1.75=H[X]$. Here the equality holds beacuse the probabilities are powers of 2.
For more complex cases, the Shannon Entropy is not exactly equal to the achieved average length, but it is very close.
##### Kraft Inequality
Unique decodability imposes a constraint (upper bound) on the length of strings created with the lossless method. The inequality is:

$\sum_{i}2^{-l_{i}}\leq 1$

Where $i$’s are the end nodes, and $l_{i}$ is the length of the message that encodes event $i$.

A lower bound to the length is given by Shannon entropy. Defining a new random variable with probabilities $q=\frac{2^{-l_{j}}}{\sum_{j}2^{-l_{j}}}$, the Kullback-Lieber divergence between $p$ and $q$ (and its property of being non negative) yields: $\sum_{i}p_{i}l_{i}\geq H[X]+\log{\frac{1}{x}}$.
Where the equality is only present for complete codes that saturate the Kraft inequality or for probabilities which are negative powers of two.

During the derivation, however, we find an interesting formula:

$L(E,X)=D_{KL}(p|q)+H[X]+\log \frac{1}{z}$

where $z$ is the partition function.
This suggests that, to minimize length, we should minimize the KL divergence to get to the condition $l_{i}\approx -\log p_{i}$.
If we take $l_{i}=\lceil-\log p_{i}\rceil$, then we can demonstrate that these lengths satisfy Kraft’s inequality and yield a code for which:

>[!theorem] Shannon’s theorem for symbol codes
>$H[X]\leq L(X,E)\leq H[X]+1$

#### Noisy Channels
Usually, communications use noisy channels, which are channels that don’t transmit the message perfectly. Let’s say that, in the example of the transmission of a 1 bit code for the outcome of a coin toss, when receiving 1 we have a 1-$f$ probability that the outcome was $heads$ and an $f$ probability that it was $tails$. The error probability is $P_{e}=f$.

The schema of a noisy channel is the following:

![[Pasted image 20241226170310.png]]

The sender takes the message $X$ and encodes it with the deterministic algorithm $E : A_{X}\rightarrow B^{K}$. Then, the encoded message $Z=E(X)$ is transmitted on the noisy channel. Because of this, the received message $Y$ is not exactly $Z$ but only related to it by a joint probability function $p_{ZY}(z,y)$. Which means that, if the message is $z$ then one receives message $y$ with probability $P_{Y}(y|z)=\frac{p_{ZY}(z,y)}{P_{Z}(z)}$. Finally, the receiver decodes the message with the deterministic decoder $D:B^{K}\rightarrow A_{X}$ to get $X'$.
##### Error correcting codes
To cope with random flips introduced by noisy channels, we can add a certain degree of redundancy. For example we can encode the information of the outcome of a single coin toss into 3 bits instead of 1, by repeating 3 times the 0 or 1. This way, even if a bit flips we can still have a good approximation of the original value by counting the majority of the received value. Of course this is not perfect because a 100 could be a 0 with first bit flipped or a 1 with last 2 bits flipped. Furthermore, there is a trade-off between a low error probability (achieved by increasing the redundancy) and a high message transmission (how many bits are needed to transmit over how many bits we use).
Another way to exploit redundancy to combat noise is to add parity check bits.

One such example is if we want to encode a variable $X$ that has $2^{K}$ possible outcomes and we need additional bits for redundancy. We take codewords of length $n$ and choose $2^{K}$ of those. Then we create and encoding $E: A_{X}\rightarrow S \subset A_{Z}^{(n)}$ which yields a rate of information transmission of $R=\frac{K}{n}$. Later, the receiver gets a message $y$ and uses a decoder to infer the original codeword $z$. After that, using $E$, the value of $x$ is obtained using $E^{-1}$. The error probability is the probability of miss-decoding the message and is:

$P_{e}^{ bayes}=P(D(Y)\neq Z)$

Let’s make an example: The noisy typewriter. In this example we have a typewriter which has noise embedded into it, so that it maps a keystroke to either itself, the following letter or the preceding one with equal probabilities.
Obviously, in this setting the decoding is not unique since 3 different inputs might give a specific output, but we can restrict the usable codewords to one every 3 letters so that it becomes unique.

This reasoning can be extended to long block codes in the limit $n \rightarrow \infty$ and we get:

>[!theorem] Shannon’s noisy channel coding theorem
>Given a noisy channel and large enough $n$, there exists a block code $(K=nR,n)$ with capacity $R<C$.

This happens because, in the limit for $n\rightarrow \infty$, sequences of size $nR$ become typical and if $R<C$, they are also essentially non-confusable. 
#### Continuous random variables
##### Differential entropy
To move from discrete random variables to continuous ones, we just need to make minor adjustments to the formalism, namely substituting probabilities with probability densities and sums with integrals.

We define a new entropy as Differential Shannon entropy:

$h[X]=-\int f_{X}(x)\log(f_{X}(x)) \, dx$

where $f_{X}(x)$ is the probability density function of $X$. This IS NOT the equivalent of Shannon’s entropy for continuous variables. Instead, it can be demonstrated that the relation between the two is:

$H_{\epsilon}[X]=h[X]+\log \frac{1}{\epsilon}+O(\epsilon)$

Where $\epsilon$ is the grade of granularity we divide the continuous variable in. Higher is the resolution, higher is the entropy since the information content of knowing exactly the distribution up to a very small resolution is high.
The formula we found can also be useful to calculate entropies of variables with common distributions.

The notion of noisy channels can also be extended to the continuous case, one such example is the gaussian channel which adds gaussian white noise to the signals.
#### Time series
##### Markov Chains
Let’s now consider a process which is a collection of random variables.
We say that the process is stationary if the joint distribution of a window of $n$ measurements is equal to the joint distribution of any other widow of size $n$.
Furthermore we say that the process is a Markov process if 

$p_{X_{n}|X_{n-1}\dots X_{1}}=p_{X_{n}|X_{n-1}}$

If the series of random variables is discrete and Markov, then it is a Markov chain, which means that

$P_{X_{1}\dots X_{n}}=P_{X_{n}|X_{n-1}}\dots P_{X_{2}|X_{1}}P_{X_{1}}$

A markov chain is time-invariant if $P_{X_{n}|X_{n-1}}$ doesn’t depend on $n$.
By defining 

$P_{kl}=Prob(X_{n}=x_{k}|X_{n-1}=x_{l})$

We can define the concept of stationary distributions. A distribution $\textbf{π}$ is said to be stationary if it is invariant under the chain, which means that:

$\pi_{k}=\sum_{l}P_{kl}\pi_{l}$

A stationary distribution is particularly important for a Markov chain because it is the distribution that the chain tends to when $n \rightarrow \infty$.
This happens if the Markov chain is irreducible (in a finite number of step any state can be reached starting from any state) and aperiodic (to go from a state to itself, paths are not all of length multiple of a integer > 1). With these properties, the chain is ergodic. A chain is time-reversible if, once equilibrium is reached, detailed balance holds (fluxes from any state to any state are balanced).
#### Monte Carlo
With inference, we are usually dealing with posterior distriubtions in the form:

$f_{\Theta,Z|Y=y}(\theta,z|y)$

where y is the observed data, z are latent variables and $\theta$ latent parameters.
When trying to extract information on the latent parts of the process, we integrate the posterior multiplied by different functions, but this is usually not trivial because of the curse of dimensionality.

The Monte Carlo method is a different approach to solving these integrals, based on sampling.
The idea is to substitute the integral with a sum over sampled points, since the points are sampled uniformly over a desired region, then the error scales with $\frac{1}{\sqrt{n}}$ independently of the dimension of the integral, which is good for us.

The sampling from a distribution can be done in various ways, one of these is Inverse CDF sampling. This only works if the CDF of the probability distribution we want to sample from is invertible. In that case, we just sample a random number between 0 and 1 and calculate the inverse of the CDF at the sampled value.

Another method, which we can use even if the CDF is not invertible or not known, is the Rejection Sampling Method. The idea is to sample a random variable $X$ which has probability distribution $f_{X}(x)$ and to do that we define an interval that contains the support or most of the support for the $f_{X}(x)$ function ($P(X\notin I)\ll 1$, with $I=[a,b]$). To sample from $f$ we first sample two values $Z_{1}$ and $Z_{2}$ from the uniform distribution in $[0,1]$. The first value is used to define $Y=a+Z_{1}b$, which is uniformly distributed in $I$. The second value is used to decide whether to accept or reject the sampled value, according to the following rule:

- Accepted if $Z_{2}\leq \frac{f_{X}(y)}{M}$ 
- Rejected if $Z_{2}> \frac{f_{X}(y)}{M}$

where $M\leq max_{x}f_{X}(x)$. This obviously samples from $f$ because values are accepted or rejected proportionally to $f$.

Unfortunately, in high dimentions, this method is not very effective since the acceptance probability shrinks very fast while increasing dimension.

This can be derived from the acceptance rules (suppose $I=[-k\sigma,k\sigma]$ large enough):

- In dimension 1: $M=max f_{X}(x)=\frac{1}{\sqrt{ 2\pi \sigma^{2} }}$ so that $P(acc)=\frac{1}{|a-b|M}$
- In higher dimensions: $M=max f_{X}(x)=\frac{1}{(2\pi \sigma^{2})^{d/2}}$ so that $P(acc)=\frac{1}{|a-b|^{d}M}$
##### Markov Chain Monte Carlo
The idea behind this method is to craft a Markov chain which has as a stationary distribution, the posterior we want to sample from. This way, we can just run the chain until equilibrium and then take any following sample from the chain as a sample from the posterior distribution.
##### Metropolis algorithm
The Metropolis Algorithm is used to create a Markov Chain which has an arbitrary distribution as stationary.
The method consists in using a symmertric (and with 0 diagonal) matrix $T$ to generate candidate transitions (it contains the probabilities of the jumps from a state to another). After that, the jump is accepted with probability $A_{ij}=min\{1,\frac{\pi_{i}}{\pi_{j}}\}$.

The rejection probability of a jump starting at $j$ is:

$R=1-\sum_{k}A_{kj}T_{kj}$

Which implies that the whole process is a Markov Chain with transition rates:

$P_{ij}=T_{ij}A_{ij}+R_{j}\delta_{ij}$

Which satisfies the detailed balance for distribution $\pi$.
This can easily be extended to infinite spaces.

This method guarantees to find a Markov Chain that converges to the desired distribution however there are two aspects that need our attention:

- Convergence time: how long before reaching equilibrium?
- Autocorrelation: Data is not extracted IID, it is correlated.
##### Spectrum of a markov chain
It can be demonstrated that convergence time and autocorrelation are related to the spectrum of the markov chain.

For example convergence time is $r=-\log|\lambda_{2}|$ and the spectral gap is defined as $1-|\lambda_{2}|$. The autocorrelation depends on the spectral gap in the sense that if we define an integrated autocorrelation $A_{g}$, then it depends on $1-\lambda_{k}$.
##### Gibbs sampling
The Metropolis algorithm can be modified to allow for proposed distributions that are not simmetric. In this case we have,

$P(x',x)=T(x',x)A(x',x)+R(x)\delta(x-x')$

with new acceptance probability given by:

$A(x',x)=min\{1,\frac{\pi(x')T(x,x')}{\pi(x)T(x',x)}\}$

This satisfies detailed balance.
A special case of Metropolis-Hastings is the Gibbs sampling method. It can be used when we are able to sample a specific variable by fixing the other variables. In this setting, we can just make the jumps of the variables one at a time. With $d$ variables $x_{1},\dots,x_{d}$ we define $\textbf{x}_{<k}=\{x_{1},\dots,x_{k-1}\}$ and $\textbf{x}_{>k}=\{x_{k+1},\dots,x_{d}\}$.

We first make the jump just for variable 1:

$T(x_{1}'|\textbf{x})=T(x_{1}'|x_{1}\textbf{x}_{>1})=P(x_{1}'|\textbf{x}_{>1})$

Then it continues with the second variable, knowing that the first has already been updated.
It can be demonstrated that the acceptance rate in this case is 1.
##### Hamiltonian Monte Carlo
Gibbs sampling is very strong but also has strong assumptions, such as that of knowing analitically all conditional probabilities. When these conditions are not met, we need to use standard Metropolis-Hastings algorithms but these do not scale well to higher dimensions because of diminishing acceptance rates.

Hamiltonian Monte Carlo methods aim at striking a balance by only requiring the knowledge of the joint distribution (likelihood times prior, like in MH) and its derivatives with respect to the parameters. Knowing these things, it can produce jump proposals with high acceptance rates.

When dealing with Hamiltonian dynamics, we use the 1st order ODEs:

$\dot{x_{i}}=\partial_{p_{i}}H(\textbf{x},\textbf{p})$ 
$\dot{p_{i}}=\partial_{x_{i}}H(\textbf{x},\textbf{p})$

If we define the Hamiltonian flow as:

$x_{t}=\phi_{t}^{x}(\textbf{x}_{0},\textbf{p}_{0})$
$p_{t}=\phi_{t}^{p}(\textbf{x}_{0},\textbf{p}_{0})$

Which are the trajectories obtained by solving the ODEs with starting point $x_{0}$ and $p_{0}$, we get some useful properties:

$Vol(\phi_{t}(A))=Vol(A)$

It should also be time-reversal invariant, so that if we flip the momenta, run the solution and re-reverse the momenta, the trajectory is the same as the original.
Furthermore, we need to check for energy preservation.

One easy way to integrate the ODEs is the Euler method, which is not exactly volume-preserving tho. We can modify it to make it volume-preserving but then it isn’t time invertible.
Another method that has both properties is the leapfrog, where you first do a half step for the momenta, then a full step for the positions and then another half step for the momenta.

The idea is to use Hamiltonian mechanics to construct the Metropolis proposal. We introduce a momentum parameter for every existing parameter, all momenta should be independant from one another and from the original parameters. We take them as distributed as standard normals. Sampling from the following distribution equals sampling $x$ from the $\pi(x)$ distribution:

$\Pi(x,p)=\pi(x)\cdot(2\pi)^{-n/2}e^{-\frac{1}{2}|p^{2}|}$

So the aim is to sample from $\Pi$. We create the potential $U(x)=-\log(\pi(x))$ and we can rewrite the distribution as:

$\Pi(x,p)=\frac{e^{-H(x,p)}}{Z}$

To sample from $\Pi$ we use a proposal found by:

- Sampling $\tilde{p}$ from its marginal probability (a normal).
- Mapping $x,\tilde{p}$ into $x',p'$ which are the next step of a leapfrom algorithm.
- Invert the momentum ($p'=-p'$).
- Extract a random number $r$ and accept if $r<min\{1,\frac{\Pi(x',p')}{\Pi(x,\tilde{p})}\}$.

