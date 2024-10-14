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

