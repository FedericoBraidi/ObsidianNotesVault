#### Attack to an hypersphere learner
An hypersphere learner is a machine learning algorithm that tries to classify points as $\textit{normal}$ or $\textit{abnormal}$ based on wether they fall inside or outside a learned hypersphere.
##### Hypersphere poisoning attack
This attack consists in modifying the learnt hypersphere by controlling the training set. The vulnerability was discovered when these types of ML algorithms were used to identify abnormal emails or network packets.
To keep itself up to date, the learner is trained with batches of new points and each batch is first tested under the current model before being used as input to train it one step.

The attack is a Targeted Causative Integrity attack. The goal of the attacker is to induce the algorithm to missclassify as $\textit{normal}$ a specific attack point $x^{A}$ that he/she wants to use later (Targeted Integrity). We can assume that, prior to the tampering, the model correctly classified $x^{A}$ as $\textit{abnormal}$. To do so, multiple points in the training set need to be tampered with which makes the attack a Repeated Causative attack.

Let’s first make a couple of assumptions:

- Omniscient attacker: It knows everything about the learner, its representation, the way certain training points will affect it, etc.
- Strong capabilities: The attacker can control every point used in retraining but not those the algorithm has already seen, which include the one he wants to change the classification of.

So the attack strategy is to insert points that are still inside the hypersphere (those outside are automatically discarded) and move the centroid towards our point $x^{A}$.

![[Pasted image 20241014193423.png]]

To do this the best way is to put training points on the intersection between the hypersphere and the line that connects the center with the point we want to get to.
###### Single attack per retraining
This happens when we use $M=1$ modified point per retraining.
We define $N$ as the size of the original dataset (before retraining).
At retraining number $t$ a single attack data vector $\textbf{a}$ moves the centroid from $c^{(t)}$ to

$c^{(t+1)}= \frac{N+t}{N+t+1}c^{(t)}+\frac{1}{N+t+1}\textbf{a}$

Which yields a displacement of 

$\rho(t)=\frac{1}{N+t+1}||c^{(t)}-\textbf{a}||$

Which means that, since we set the point at the maximum distance $R$ , the obtained displacement is:

$\rho_{max}(t)=\frac{R}{N+t+1}$
###### Double attack per retraining
We use now two attack points $\textbf{a}_{1}^{(t)}$ and $\textbf{a}_{2}^{(t)}$ at each step, so that our attack is:

$\textbf{a}=\frac{1}{2}\sum_{p=1}^{2} \textbf{a}_{p}^{(t)}$

With this we can get a maximum displacement of

$\rho_{max}(t)=\frac{MR}{N+tM+M}$
###### Double attack Vs Double Single Attack
Indeed doing 2 single attacks yields a maximum displacement of 

$\rho_{max}(t)=\frac{R}{N+1} + \frac{R}{N+2}$ 

Which is greater than the double attack, this can be proven for any multiple attack.
After $T$ steps a single point attack will have produced a displacement of

$\rho_{tot}(T)=R \ln(\frac{T+N}{N})$
###### Attacks with a number cap
We’ve seen that the best way us to only add one malign point at each retraining but this is not always possible. Some systems try to defend against these types of attacks by setting caps on number of retrainings and number of malign points inserted (which might also not be constant across retrainings).

We define:

- $M_{t}$ : the number of attack points in retraining $t$. We know that $\sum_{i=0}^{T}M_{i}=M'$.
- $\mu_{t}=\sum_{i=0}^{t} M_{i}$ : total number of training points up to retraining $t$. We know that $\mu_{0}=N$.

Thanks to these definitions, our second constraint is: $\mu_{T}=N+M'$
The displacement at step 1, starting with centroid $c_{0}$ and placing $M_{1}$ points on the connecting line to the target point at distance $R$ is:

$\Delta(1)=\frac{M_{1}}{N+M_{1}}=\frac{M_{1}}{\mu_{1}}$

And we can see that the same goes for other displacements:

$\Delta(2)= \frac{M_{2}}{N+M_{2}}=\frac{M_{2}}{\mu_{2}}$

So that the cumulative displacement after $T$ retrainings is:

$D(\mu)= T-\sum_{t=1}^{T}\frac{\mu_{t}-1}{\mu_{t}}$

So to decide on the best way to distribute our attack points we need to solve

$\mu^{*}\in argmax_{\mu}D(\mu)$ 

under the constraints that $\mu_{0}^{*}\leq \mu_{1}^{*}\leq \dots \leq \mu_{T}^{*}$ and the other assumptions on $\mu_{i}$.
The solution to the relaxed problem where $\mu_{i}$ is not required to be an integer is:

$$
M_{t}^{*}=
\begin{cases}
N  \quad\quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad t=0 \\
N(\frac{M'+N}{N})^{\frac{t-1}{T}}((\frac{M'+N}{N})^{\frac{1}{T}}-1) \quad \, \, \, \, t=1,\dots, T
\end{cases}
$$
###### Finite-Horizon Attacks
So far we have seen attacks on a hypersphere learner that doesn’t discard any point that is inside of the current hypersphere so that we cannot modify the data it has already seen. There are also Data Dropping learners that substitute each new point with one that has already been seen. The centroid is updated as:

$c^{(t+1)}=c^{(t)}+(x^{(t)}-x_{old})$

These algorithms differ in the way they choose the point to substitute:

- Oldest out: The point with the oldest timestamp is removed.
- Random out: A random point is removed.
- Nearest out: The closest point to the new one is removed.
- Average out: The center of mass of the points is removed and the new centroid is
			$c^{(t+1)}=c^{(t)}+\frac{1}{N}(x^{(t)}-c^{(t)})$

The defense that makes the most sense on the method we used just now is the Nearest out rule. We need to find the attack that gets us closer to $x^{A}$ at each retraining.
For each point $x_{old}$ find the best attack as:

$\textbf{a}=argmin_{x}||c^{(t)}-\frac{x_{old}}{N}+\frac{x}{N}-x^{A}||$

under the constraints that $||x-x_{j}||<||x-x_{i}|| \quad \forall i \neq j$ and $||x-c^{(t)}||< R$.

All these results can be extended to arbitrarily complex decision boundaries using kernels, however we can only design base points which will then be embedded in the feature space so the problems have additional constraints that make them difficult to solve.

