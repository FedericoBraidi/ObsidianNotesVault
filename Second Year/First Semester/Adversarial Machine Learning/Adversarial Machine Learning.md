## Attack to an hypersphere learner
An hypersphere learner is a machine learning algorithm that tries to classify points as $\textit{normal}$ or $\textit{abnormal}$ based on wether they fall inside or outside a learned hypersphere.
### Hypersphere poisoning attack
This attack consists in modifying the learnt hypersphere by controlling the training set. The vulnerability was discovered when these types of ML algorithms were used to identify abnormal emails or network packets.
To keep itself up to date, the learner is trained with batches of new points and each batch is first tested under the current model before being used as input to train it one step.

The attack is a Targeted Causative Integrity attack. The goal of the attacker is to induce the algorithm to missclassify as $\textit{normal}$ a specific attack point $x^{A}$ that he/she wants to use later (Targeted Integrity). We can assume that, prior to the tampering, the model correctly classified $x^{A}$ as $\textit{abnormal}$. To do so, multiple points in the training set need to be tampered with which makes the attack a Repeated Causative attack.

Let’s first make a couple of assumptions:

- Omniscient attacker: It knows everything about the learner, its representation, the way certain training points will affect it, etc.
- Strong capabilities: The attacker can control every point used in retraining but not those the algorithm has already seen, which include the one he wants to change the classification of.

So the attack strategy is to insert points that are still inside the hypersphere (those outside are automatically discarded) and move the centroid towards our point $x^{A}$.

![[Pasted image 20241014193423.png]]

To do this the best way is to put training points on the intersection between the hypersphere and the line that connects the center with the point we want to get to.
#### Single attack per retraining
This happens when we use $M=1$ modified point per retraining.
We define $N$ as the size of the original dataset (before retraining).
At retraining number $t$ a single attack data vector $\textbf{a}$ moves the centroid from $c^{(t)}$ to

$c^{(t+1)}= \frac{N+t}{N+t+1}c^{(t)}+\frac{1}{N+t+1}\textbf{a}$

Which yields a displacement of 

$\rho(t)=\frac{1}{N+t+1}||c^{(t)}-\textbf{a}||$

Which means that, since we set the point at the maximum distance $R$ , the obtained displacement is:

$\rho_{max}(t)=\frac{R}{N+t+1}$
#### Double attack per retraining
We use now two attack points $\textbf{a}_{1}^{(t)}$ and $\textbf{a}_{2}^{(t)}$ at each step, so that our attack is:

$\textbf{a}=\frac{1}{2}\sum_{p=1}^{2} \textbf{a}_{p}^{(t)}$

With this we can get a maximum displacement of

$\rho_{max}(t)=\frac{MR}{N+tM+M}$
#### Double attack Vs Double Single Attack
Indeed doing 2 single attacks yields a maximum displacement of 

$\rho_{max}(t)=\frac{R}{N+1} + \frac{R}{N+2}$ 

Which is greater than the double attack, this can be proven for any multiple attack.
After $T$ steps a single point attack will have produced a displacement of

$\rho_{tot}(T)=R \ln(\frac{T+N}{N})$
#### Attacks with a number cap
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
#### Finite-Horizon Attacks
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
### Poinsoning
Poisoning AML attacks are attacks that tamper with the training process and aim at making the model missclassify trigger inputs. They can be thought of as Targeted attacks. Contrarily to what one might think, they are actually different to conventional data poinsoning attacks. The latter aim at generally degrading the performance of the model and can be seen as availability attacks while the former aim at degrading the performance only for the trigger inputs while mantaining a good performance elsewhere.
Poisoning attacks can be divided into:

- Class-agnostic attacks: Every input stamped with the trigger gets classified as the class targeted by the attacker.
- Class-specific attacks: Only inputs stamped with the trigger and from a specific class are classified as the class targeted by the attacker.

There are many ways a trigger can be chosen:

- An image blended semi-transparently with the trigger.
- Distributed or spread trigger on the image.
- Accessory as trigger (i.e. eyeglasses).
- Specific caratteristics.

Poisoning attacks of this kind can be carried out in different manners:

- Outsourcing attack: The user outsources the training of the model to a external company because of lack of computational resources, expertise, etc. This external company is malicious and tampers with the data to give itself a backdoor into the model.
- Pretrained attack: The attacker releases a model that has a backdoor in it, someone else uses it to re-train it on his dataset (Transfer Learning).
- Data collection attacks: The attacker inserts targets into input data in order to make the model classify every future input with the trigger in a certain way. One example exploits the resize function in python, since the images are usually resized as 224x224x3, one can hide another image into the only columns that will remain once resized. Some of these types of attacks are also Trojan attacks (you need access to the model for this), here you make certain neurons very sensitive to watermarks (trojan marks) so that when an input has them it is classified according to what the attacker wants.
#### Defenses against poisoning attacks
Techniques to avoid poisoning of a ML model can be divided into two groups:

- Offline inspection: The system is analyzed and cleaned before the deployment, either by cleaning the input data and retraining (if we have access to it) or by just cleaning the model (if we don’t have access to the data).
- Online inspection: After deployment the model is tested for performance.
##### NeuronInspect
For each class we create a heatmap that shows which pixels the model looks at when it ends up classificating an input into that category.
The heamaps produced for tagged input for the target class are less sparse, most smooth and persistend across different input images.
##### STRIP defense
Superimpose other images onto inputs during runtime. A low entropy of a specific class is suspicious because it means the model doesn’t care about the actual image.
##### ABS defense
Entails scanning the enire model and controlling abnormalities such as strangely high activations of a specific neuron for a target label.
##### NIC defense
Inspect the distribution of the activations at different layers of the DNN to detect abnormalities. You might do this by clustering vectors of weights at a specific layer across inputs.
##### Neural cleanse
This method aims at identifying the trigger and then proceeds with various approaches.
The idea to identify triggers is that there are specific directions in the feature space that, when moving along with them, make input jump classes.

![[Pasted image 20241021091027.png]]

We do this in 3 steps:

- Take a given label to be the target label and apply an optimization algorithm to get the smallest perturbation needed to make the model classify any input to the target class.
- Repeat point 1 for every output label in the model.
- Take the smallest of the potential triggers found in the previous steps.

Once we have found that a model has been backdoored we can proceed in 3 ways:

- Apply a filter that identifies adversarial inputs and eliminates them. We cannot check if the image contains the reversed trigger we got, because it is usually pretty different than the real trigger. Although we don’t have the same trigger, we have one that performs in the same way so that we can check the path an input takes with respect to what a triggered input does (we can create one with our reversed trigger).
- Model patching algorithm based on neuron pruning. Look for neurons that are very disruptive and have high output values to triggered inputs and set their output to 0 during inference.
- Model patching algorithm based on unlearning. Fine tune the model by taking a normal input, adding the reversed trigger but leaving the correct label. This way the model learns it should not missclassify inputs just because of the trigger.
##### SentiNet
This approach works particularly well on small triggers. We take an image and classify it, then we segment it and classify the subimages. If the image was triggered then we should be able to get the real label by classifying the subimage that cuts out the trigger. Then we can isolate the trigger by getting the saliency map (heatmap as in NeuronInspect) of the whole image and subtracting the saliency map for the image classified in the secondary class.
Now we have a potential trigger and we can apply it to other images to see if they get missclassified to the target class as well. Analyze the decision boundary when applying the trigger and when applying a random gaussian noise to the same portion of the image.
##### Spam Bayes Filter
This defense works well on specific causative availability attacks. The aim of these attacks is to make the false positive of the model so high it is unacceptable in the number of good interactions it blocks.
The idea is to define certain tokens which may appear in a random message and create a column vector of 0-1 values indicating which tokens are present.

The method is called SpamBayes because the probability of a token $i$ being part of a spam message is calculated using the Bayes theorem with uniform prior:

$P^{(s)}_{i}=P(spam|x_{i}=1)=\frac{P(x_{i }|spam)P(spam)}{P(x_{i}=1|spam)P(spam)+P(x_{i}=1|ham)P(ham)}$

Which come out to be

$P_{i}^{(s)}=\frac{\frac{n_{i}^{(s)}}{N^{(s)}}}{\frac{n_{i}^{(s)}}{N^{(s)}}+\frac{n_{i}^{(h)}}{N^{(h)}}}=\frac{n_{i}^{(s)}N^{(h)}}{n_{i}^{(s)}N^{(h)}+n_{i}^{(h)}N^{(s)}}$

To avoid extreme values we use smoothing with:

$q_{i}=\frac{s}{s+n_{i}}x+\frac{n_{i}}{s+n_{i}}P^{(s)}_{i}$

Then, for every incoming message $\hat{X}$, the following are calculated:

$S(\hat{x})=1-\chi^{2}_{2D}[-2(\log q)^{T}\hat{x}]$

and $H(\hat{x})$ analogously ($1-q$ instead of $q$).
Then, using these, $I$ is calculated as:

$I(\hat{x})=\frac{1}{2}[S(\hat{x})+1-H(\hat{x})]$

Which is confronted with some thresholds and used to categorize the message.
There are multiple ways to attack these kinds of defenses:

- Indiscriminate attacks: forces the user to disable the filter because of a high false positive rate.
- Targeted attacks: Sends specific good messages to spam.

In the first type of attack the hacker uses the fact that usually the filter is trained on every incoming message. Thus it sends a message $a$, which the model is trained on, and then a message $x$ of spam which needs to be missclassified. We want to find which tokens to punt in $a$ so that $I(\hat{x})$ is the highest it can be.
The optimal attack consists in sending $a$ with all tokens, so that it will be classified as spam and all tokens will increase their weight to the spam category. The tokens are many and this attacks is unfeasible, we use a Dictionary attack. This restricts the tokens used to those most often used in the target’s emails (for language, misspelling, colloquialism etc.)

We can instead take the approach of a Targeted attack. In this case we want the model to missclassify a specific message of which we have partial knowledge. We send an attack message with the tokens we know will be in the targeted one and abfuscate with other spam tokens. This increases the score for the legitimate tokens and will lead to missclassification of the target.

Reject on Negative Impact (RONI) is a method to defend against Causative attacks. This measures the empirical effect that every training message has had on the classifier. We clone the classifier and do inference on a set of correctly labelled messages with a model before training on the current message and after. If by training on the current message we get a significantly worse performance then we discard the point.
##### PCA Detector
Let’s imagine working with a network of nodes that communicate between one another. Anomalous volumes of flow of communications are related to misconfigurations, hardware problems, DDos attacks, etc. They can also be legitimate, in which case they are called flash crowds.

We want a model capable of detecting unusually high origin-destination flows between nodes.

The number of one-directional flows is $Q=V^{2}$ where $V$ is the number of nodes. We also define the amount of traffic which passed through the $q_{th}$ flow during $t_{th}$ timestep as $Q_{t,q}$ .
All the $Q_{t,q}$ are summarized in a matrix $\mathbf{Q}$ ($NT\,$x$\,Q$ in dimension).

We don’t directly observe $\mathbf{Q}$ but a link traffic matrix that contains the time series of all the links and is obtained by multiplying $\mathbf{Q}$ with a routing matrix $\mathbf{R}$ which is constructed to point out, for each flow, what are the interested nodes. $\mathbf{X}=\mathbf{Q}\mathbf{R}^{T}$.

If we take a row of this matrix we get, for a specific timestep, the measurements that each link present in the network is experiencing. Generally, even though these are high in dimensionality ($D$, number of existing links), most of the variability is explained by a much lower number of dimensions $K$.

To find these, we use PCA and we get a principal subspace spanned by the first $k$ components, that we call $V$. The other components form $W$.
$\dot{P}=VV^{T}$ represents the normal traffic, the one explained by the principal components.
$\ddot{P}=WW^{T}$ represents the anomalous traffic.

The classifier developed in this way checks for high $\ddot{P}$ with a threshold:

$$
f(x^{(t)})=
\begin{cases}
"+"   \quad\quad \quad \quad \quad ||\ddot{P}(x^{(t)}- \hat{c})||^{2}>Q^{-1}(1-\beta) \\
"-" \quad \quad \quad \quad \quad otherwise
\end{cases}
$$
Where $Q$ is the Q-function and + indicates the presence of an anomaly. $\beta$ is a parameter.
This model needs to be retrained frequently due to intrinsic drift.

Due to the periodic need to retrain the model, some attack schemes emerge. The idea is to poison the model when it retrains by adding traffic along the OD flow that we want to attack later, so that it won’t be discovered.
This can be carried out in 3 different ways depending of the amound of information we have about the system:

- Uninformed attack: We don’t know the current network traffic. At each step we decide with a Bernoulli variable wether we attack or not.
- Locally informed attack: We know the traffic on the flow we want to attack. At each step we inject traffic only if the current traffic is already high enough and we inject a quantity proportional to what is already there.
- Omnipotent attack: We know info about every flow and can attack every flow. We need to find how much to send on every flow at every timestep to maximize the amount of distance from the previous solutions that is allowed by the PCA. The PCA is calculated on the matrix $X+A$ and then the parameter $\beta$ is the one that is trained to correctly predict the threshold.

The problem in the third case is complex. To relax it let’s just inject into the links of the flow we want to attack. Now the problem is:

$\max_{A}{||(\hat{X}+A)R_{q}||_{2}}$

Such that $||A||_{1}<\theta$, the fixed amount of the attack, and $A_{t,q'}\geq 0 \;\forall\; t,q'$. This problem can instead be solved with the right methods.

A more intelligent version of the attack is to use the fact that the model is retrained periodically, say every week, and poison at every retraining, every time more.

$\theta^{(t)}=k\theta(t-1)$
### Evasion
Evasion attacks are those that work by engineering a special input expressely for it to be missclassified by the model, either to a specific (targeted attack) or random (untargeted attack) label. 
#### White-Box Attacks
##### FGSM Attacks
Fast gradient sign method attacks work by inserting an adversarial image which is expecially crafted to maximize the loss and make the model missclassify it. It is a White box attack because a knowledge of the weights, structure and loss function of the model are needed.
The image is created as:

$x_{adv}=x+\epsilon \,\cdot\,sign(\nabla_{x}\mathcal{L}(C(x,w),y))$
##### PGD Attacks
Projected gradient descent attacks are just a step versison of FGSM attacks, in the sense that the perturbation is added a bit at a time:

$x_{adv}^{n}=x^{n-1}+\gamma \,\cdot\,sign(\nabla_{x}\mathcal{L}(C(x,w),y))$

Both FGSM and PGD can be designed to make the model classify the adversarial image to a specific label rather than just to a different label to the right one.
##### Deep Fool Attacks
This approach aims at creating an adversarial image that gets missclassified but with the least amount of noise possible, so that the image is indistinguishable from the original.
The idea is to check the parameter space in which the image gets converted to be classified. In this space, the decision on the label is taken using linear or non linear decision boundaries. This method works by determining the closest decision boundary and pushing the image over it. In the case of non linear DB then the process is iterative and at each step the boundaries are linearized around the point.
##### JSM Attacks
Jacobian-based Saliency Map Attacks are aimed at controlling the $L_{0}$ norm by iteratively changing one pixel at a time according to a saliency map for the target label.
##### Carlini&Wagner Attack
The idea is to create an adversarial image which is as similar as possible to the original but that gets classified to a different category. We want:

$minimize \,D(x,x+\delta)$
$such \,that \, C(x+\delta)=t\neq C(x)$

The second line is a strong requirement so C&W relax it like this:

$f(x+\delta)\leq 0$

This is not equivalent but gives a nice approximation.
The used distance $D$ can be $L_{\infty}$, $L_{2}$ or $L_{0}$ and there are also many versions of $f$, the best is:

$f(x')=(max_{i\neq t}(Z(x')_{i})-Z(x')_{t})^{+}$

This is the difference in logits between the target class and its closest class.
##### stAdv Attack
Spatial transformation attacks propose not to modify the intensity of the pixels of the image but rather to just move them spatially inside of it.
We create a distorsion field $f$ that moves pixels to their new locations to create the adversarial image. The goal is to find the image that achieves the missclassification with the minimum displacement.

$f^{*}=argmin_{f} \;\mathcal{L}_{{adv}}(x,f)+\tau \mathcal{L}_{flow}(f)$

The first term helps with missclassification, the second with the preservation of the image shape.
#### Black-Box Attacks
Black-box adversarial attacks can be classified into two categories:

- Query-based attacks: The adversary queries the model and creates the adversarial inputs with the information received from the queries. These attacks are further divided based on the type of information that is given by the model to the attacker into:
	- Score-based attacks: If the queries result in the probability distribution that the model gives to the labels.
	- Decision-based attacks: If the queries result in the predicted label from the model.
- Transfer-based attacks: The adversary trains its own substitute model(s) and produces the adversarial inputs for it(them), then transfers them to the original model, these attacks are also called zero queries attacks.
##### Gradient estimation attack
It is a score-based attack which achieves performances in line with white-box attacks and also faces well against adversarial defenses.
This method approximates the gradient using queries and uses the estimated gradient to produce adversarial examples.
The gradient is estimated with the method of finite differences. The output of the model is a vector of the size of the number of classes, then we can calculate its gradient as:

$\nabla_{x}g(x)$= $\text{FD}_x\big(g(\mathbf{x}), \delta\big) = \begin{bmatrix} \frac{g(\mathbf{x} + \delta \mathbf{e}_1) - g(\mathbf{x} - \delta \mathbf{e}_1)}{2\delta} \\ \vdots \\ \frac{g(\mathbf{x} + \delta \mathbf{e}_d) - g(\mathbf{x} - \delta \mathbf{e}_d)}{2\delta} \end{bmatrix}$

Where $e_{i}$ is a vector of the normal base (i.e. 0 everywhere except for position $i$).

With the gradient estimation method we can simulate a FGSM attack. In particular imagine a cross-entropy loss:
$l_{f}(x, y) = -\sum_{j=1}^{|\mathcal{Y}|} 1[j = y] \log p_j^f(x) = -\log p_y^f(x)$

The gradient of the loss function $l_{f}(x, y)$ with respect to the input $x$ is:

$\nabla_x \ell_f(x, y) = -\frac{\nabla_x p_y^f(x)}{p_y^f(x)}$

So, from the formulas of the FGSM attack, the untargeted adversarial example generated is:

$x_{adv}=x+\epsilon \,\cdot\,sign(\frac{FD_{x}(p_{y}^{f}(x),\delta)}{p_{y}^{f}(x)})$

While the one targeted to label $T$ is:

$x_{adv}=x-\epsilon \,\cdot\,sign(\frac{FD_{x}(p_{T}^{f}(x),\delta)}{p_{T}^{f}(x)})$

Another attack that we can simulate is the Carlini-Wagner attack. It uses a loss function based on the logits:

$l(x,y)=max(\phi(x+\delta)_{y}-max\{\phi(x+\delta)_{i}: i \neq j\},-k)$

Which means that the adversarial examples for the non targeted attack are:

$x_{adv}=x+\epsilon\,\cdot\,sign(FD_{x}(\phi(x)_{y'}-\phi(x)_{y},\delta))$

With $y'$ being the next closes label. For the targeted attack on label $T$

$x_{adv}=x-\epsilon\,\cdot\,sign(FD_{x}(max(\phi(x)_{i}\,:\,i\neq T)-\phi(x)_{T},\delta))$

Finally, for the PGD attack equivalent the update rule is given by:

$x_{\text{adv}}^{t+1} = x_{\text{adv}}^t + \alpha \cdot \text{sign} \left( \frac{\text{FD} \left( \nabla_{x_{\text{adv}}^t} p_y^f(x_{\text{adv}}^t), \delta \right)}{p_y^f(x_{\text{adv}}^t)} \right)$
##### Zoo Attack
Zoo (Zeroth order optimization) attack is somewhat of a score-based black box version of the Carlini Wagner attack. Similarly to that, it solves an optimization problem, in this case:

$minimize ||x-x_{0}||_{2}^{2}+c\,\cdot\,(max_{y'\neq T}\log F(x)_{y'}-\log F(x)_{T})^{+}$

Under the condition that $x \in [0,1]$. The confidence score of one class dominates the one of the other class but the logarithms mitigate this dominance mantaining the order. 
An Adam optimizer of Newton Method is used to solve the problem and the calculation of the Hessian is needed:

$h=\nabla^{2}_{x}f(x)\approx \frac{f(x+h)+f(x-h)-2f(x)}{h^{2}}$
##### Boundary attack
This is a decision-based black box attack. It is more useful as often there is no access to internal logits, however convergence is slow (high number of queries).
It works by choosing an existing image with its relative label and creating another image with random gaussian noise which is either classified as the target label (targeted attack) or just not as the label of the original (non targeted attack).
We add noise (actually denoising) to the created image to move it orthogonally along the fixed distance sphere around the original. This step in the feature space is constrained and values of pixels in the range [0,1] are checked. Then a step in the direction of similarity with the original image is made. Finally, the model is queried with the new adversarial image and we check that it is still missclassified in the way we want, otherwise we wipe this iteration and adjust.
The first step (orthogonal) is of length $\delta$ which is dynamically changed in such a way to always have 50% of the movements be adversarial.
The second step (towards the original) is of length $\epsilon$ which is kept such that the success rate of both steps together is at least 25%.
The attack stops when we reach a max of iterations or when $\epsilon$ reaches zero.
##### Hop Skip Jump attack
This is an evolution of the boundary attack, it is still a decision-based black box attack but 
requires much less queries than its predecessor.
Its success depends on the different way to move closer to the original image:

![[Pasted image 20241119234549.png]]

We start at a random point $\tilde{x}_{t}$, then binary search to the original ($x^{*}$) and find the boundary point $x_{t}$, then we calculate the gradient direction at $x_{t}$ and move $\tilde{x}_{t}\rightarrow\tilde{x}_{t+1}$ on the line given by the gradient. Then we just repeat this process until we get to the closes adversarial image $x^{*}$ to the original.
##### Substitute model attack
This is a transfer-based attack which doesn’t require any querying on the target model.
The results of this attacks are seen when transferring adversarial example intra or inter models.
For example, this is the intra-transfer for DNNs:

![[Pasted image 20241119235552.png]]

These are the same for SVM, DT and kNN:

![[Pasted image 20241119235622.png]]

And these are the stats for the inter-model transfer:

![[Pasted image 20241119235651.png]]
##### Ensamble of Local Models attack
This attack is a transfer based attack. It generates from the notion that non-targeted adversarial images transfer well between models of the same type, but targeted ones rarely transfer with the same target label. 
The idea is to train a targeted adversarial image that is simultaneously working on various models with the same label. This should ensure that, when transferred, it mantains the same label as well.
To do this we need to solve the following optimization problem:

$\arg\min_{\mathbf{x}^\star} -\log \left( \left( \sum_{i=1}^k \alpha_i J_i(\mathbf{x}^\star) \right) \cdot \mathbf{1}_{y^\star} \right) + \lambda d(\mathbf{x}, \mathbf{x}^\star)$
#### Defense against Evasion attacks
Defense techniques agains evasion attacks can be divided into three categories:

- Adversarial example detection: Design a way to distinguish clean from adversarial examples.
- Gradient masking/obfuscation: Design a way to hide the gradient, this works on black box score-based attacks for example.
- Robust optimization: Design a way to make the model robust against adversarial examples.
##### Adversarial Example Detection
Defense mechanisms of this category work as a wall between the user and the model. If they see an adversarial example they don’t send it to the model altogether.
Based on the information at play, the threats can be modeled in three ways:

- Zero-knowledge adversary: The adversary has access to the classifier F but doesn’t know that the detection model D is being used.
- Perfect-knowledge adversary: The adversary has access to the classifier F, knows that the detection model D is being used, and has full access to it.
- Limited-knowledge adversaty: The adversary has access to the classifier F, knows that the detection model D is being used but has no access to its parameters and/or training set.

These methods can be further divided into:

- Auxiliary detection model
- Statistical methods
- Prediction consistency methods

Their limitation is that they don’t necessarily work well on samples created by unknown adversarial attacks.
###### Auxiliary detection model
An auxiliary detection model is trained on normal and adversarial attack with a binary classification task. The adversarial examples are crafted from a large variety of attacks to make it robust against many of them. This model needs to have low false-negative and false-positive probabilities.

Some examples being used are:

- Training a NN on binary classification of inputs (achieved 99%)
- Training a CNN using the hidden representations of the model’s CNN (over 80% for mosta attacks)
- Training a model with k+1 labels, the last being reserved for adversarial examples (performed well against JSMA but not against FGSM)
- Training one or more AEs (Detectors) on clean samples and another DAE (Denoising AE, Reformer) on clean samples with added gaussian noise. Then when working, the Detectors check for abnormally high reconstruction error and reject those samples as adversarial. The others are passed through a DAE so that even lightly modified adversarial examples are cleaned (worked well agains all attacks)
###### Statistical detection methods
One employed application is using PCA on input images. It was found that clean images place uniform coefficients on the eigenvalues, while adversarial place higher coefficients on later PCs. This is used to identify them.
###### Prediction Consistency methods
One famous such technique is Feature squeezing, it employs various tactics, such as:

- Color depth reduction: Grey scale images have $2^{8}=256$ intensity values for each pixel, while RGB $2^{24}=16mil$. Only allowing $2$ values of intensity per pixel invalidates all grey pixels and only keeps white and black, getting rid of added grey noise.
- Spatial smoothing: It is a family of techniques where each pixel of the image is modified based on its neighbours (local smoothing) or the whole set of other pixels (non-local smoothing).

After producing these images for each input, the model compares them and rejects the sample if their classification difference is higher than a Threshold.

Another method of this category is to randomize the classifier with Dropout during inference dropping 50% of the neurons. If the prediction of this modified model on samples is significantly different to that of the unmodified model, we flag the input at adversarial.

Unfortunately (or fortunately), C&W showed how to bypass all working Adversarial example detection methods.
##### Gradient masking/obfuscation
These methods aim at hiding the real gradient from the attacker so that it is more difficult form him/her to create adversarial example. However, they don’t avoid that adversarial inputs are created altogether.
They can be grouped into:

- Exploding/Vanishing gradients 
- Shattered gradients 
- Stochastic/randomized gradients 
###### Exploding/Vanishing Gradients
One such example is Defensive Distillation. This approach applies knowledge distillation in NNs. It worked well at first but then C&W showed that adversarial examples can be resilient to this defense.
Knowledge distillation is the process of transferring knowledge from a pretrained big NN to a smaller one, while retaining roughly the same accuracy.
One way to do this is training, as usual, the big model with hard ground truths (one-hot encoded labels) and take the softmax output to use as label when training the small model. The probability distribution contains much more information about the classification of the image with respect to the one-hot encoding.

It has been shown that a good way is to train both the big model and the distilled one with high temperature (softmax spreads out the probability) and then at test time, lower the temperature to 1. This way the logits are multiplied by T during inference and the probability is closer to 1 for the true label. This method makes the distilled model less sensitive to small variations and therefore more resilient to adversarial inputs.

We define robustness as the expected value of $\Delta_{adv}(X,F)$:

$\rho_{adv}(F)=\mathbb{E}_{X \sim P}[\Delta_{adv}(X,F)]$

Where $\Delta_{adv}(X,F)$ is defined as:

$\Delta_{adv}(X,F)=min\{||\delta||:F(X+\delta)\neq F(X)\}$

So the minimum displacement needed to missclassify an input. Empirically it is calculated as:

$\rho_{adv}(F)\approx \frac{1}{M}\sum_{i=1}^{M}min||\delta_{i}||$

Up to some point, the robustness of the distilled model is demonstrated to be increasing with training temperature.

Another way to implement exploding or vanishing gradients is via a Defense-GAN.
A GAN is trained to model the clean input distribution, then during inference it is used to eliminate adversarial noise and reconstruct the original image. The fact that we add a GAN before classification makes the whole model a very Deep NN, so that we get vanishing gradient.
###### Shattered Gradients
One method is the shattered gradients method which disrupts the flow of information from the inputs to the outputs so that the adversary can’t reconstruct the gradients to craft adversarial examples.
For instance we can preprocess the inputs with a non-differentiable preprocessor $g$, then the target for the classification is $f(g(x))$ which is not differentiable in terms of $x$, making the attacks fail.

Another approach is Thermometer encoding which discretizes the intensity of the input pixels and transforms them into vectors, breaking the calculations of the gradients.

You can also apply image transformations to break the calculation of the gradients and/or detect adversarial examples.
###### Stochastic Randomized Gradients
These methods introduce randomization to reduce the effect of attacks.

For example, one approach is to apply random resizing of the inputs and random padding. For every input the prediction is obtained as the average of the predictions for 30 randomized versions of it.

Another of these defenses is Stochastic Activation Pruning. It works similarly to Dropout but removes the activations of neurons with high activations and normalizes the outputs for the next layer.

The Random Self-Ensemble method uses random noise layers that make the calculation of the gradients much more difficult and also trains a group of models and chooses a random one at each inference request.

The last method is called Fortified Networks. It applies denoising autoencoders to hidden layers in a DNN. This has proven more beneficial than denoising the input.

Unfortunately, C&W demonstrated that even Gradient Masking and Obfuscation approaches don’t work and give a false sense of security.
##### Robust Optimization
Robust optimization methods aim to evaluate and improve the robustness of the target classifier to adversarial attacks, they change the way the parameters are learned in order to minimize the missclassification of adversarial examples.

They can be divided into three categories:

- Adversarial training
- Regularization methods
- Certified defense
###### Adversarial training
The idea here is to augment the training dataset with a new input for each of the real ones, constructed with an adversarial method known. Then we train the model with the correctly classified adversarial examples so that the model learns what to do with them.

You can either train from the beginning with the clean and adversarial examples or train on the clean inputs and fine-tune on the adversarial ones. Finally, you can even train using only adversarial examples.

One more advanced method is Ensamble adversarial training where you feed create FGSM adversarial examples of an input for $N$ models. Then you train the final model with the clean example and the $N$ adversarial ones. This is much more efficient and scalable, however we have an accuracy-robustness tradeoff for which robustness decreases accuracy on clean samples.

Another method is TRADES (TRade-off inspired Adversarial DEfense via Surrogate loss minimization). In this, we train a model with the loss:

$min_{x}\mathbb{E}\{\mathcal{L}(F(x),y)+ max_{||x-x^{'}||\leq \epsilon}\frac{\mathcal{L}(F(x),F'(x))}{\lambda}\}$

The first term is the classic loss, the second term minimizes the prediction difference between clean and adversarial samples. The term $\lambda$ is used to balance the importance of the two.
###### Regularization methods
Usually regularization is used to make the model generalize better, these methods employ this idea.

One such method is Deep Contractive Network, where at each layer a penalty to the gradients is applied, that makes the last layers less susceptible to small variations in the input.

Another method is Parseval Networks, where at each layer, we want the output to be Lipschitz continuous, with a constrained Lipschitz constant.

$||f_{k}(x,\theta_{k})-f_{k}(x+\delta,\theta_{k})||\leq L_{k}||\delta||$
###### Certified Defense
Certified models verify the robustness of a trained model with respect to a metric/criterion.

Two commonly used metrics for verifying model robustness are:
- Lower bound of the minimal perturbation.
- Upper bound of the adversarial loss.

In the Lower Bound of the Minimal Perturbation we mean the radius $r(x,F)$ of the maximum ball around input $x$ that only has correctly classified samples inside. Saying that the model has a trained lower boud certificate $C(x,F)$ means that $C(x,f)\leq r(x,F)$.

With Upper bound of the adversarial loss we mean the highest loss found for a sample in a ball of radius $\epsilon$ from $x$. A trainable certificate $U(x,F)$ ensures that $U(x,F)\geq\mathcal{L}_{adv}(F(x),y)$.

For example, it was demonstrated that for a 1 layer NN on MNIST no attack with $\epsilon \leq 0.1$ could produce more than 35% test error.
Another application was done on a model trained on ImageNet which applied Gaussian Smoothing to input images to overpower specific adversarial noise with generalized random noise.
### Network Intrusion Detection
Network Intrusion Detection Systems (NIDS) are systems deployed in networks to analyze traffic and discover malicious attacks in real time. They should:

- Detect both known and unknown attacks.
- Suggest if an adaptation is needed.
- Be fast.
- Present the results in a comprehensible way.
- Minimize false negatives and positives.

These systems can be either deployed to scan one host or a whole network.
They can be divided into:

- Signature-based systems: They compare new traffic with a database of known attacks. The database must be updated continuously and the detection of new attacks is difficult.
- Anomaly-based systems: They draw a statistic of normal usage of the system and anything deviating too much from it is considered an anomaly. They can detect new attacks but might have high false alarms rates.

There are many datasets containing normal network traffic and adversarial attacks, used to train ML models to perform Network Intrusion Detection.
They contain connection messages saved in PCAP format.

The most famous of these datasets is NSL-KDD which contains 150000 PCAP files with 40 features each. These files are labelled and attacks are divided into 4 classes:

- DoS
- Probing: trying to get unauthorized info on the network.
- U2R: trying to get unauthorized root access.
- R2L: trying to get unauthorized remote access.

The split is 125k-25k for train-test and in the test set there are attacks not present in the training.

Another dataset is the CSE-CIC-IDS2018. It is much more recent and contains both PCAP files and system logs for the machines. Each PCAP file contains 80 features and there are 7 types of attacks:

- Brute force: submit many times to guess password.
- Heartbleed attack: scan for vulnerable applications and use the info to get data from the server.
- Botnet attack: malwares requesting screenshots or keyloggings periodically.
- DoS
- DDoS
- Web attacks: SQL injection, unrestricted file upload etc.
- Infiltration attack: send applications via email attachment which scan the network.

Anomaly detection models can be trained in supervised, unsupervised and semi-supervised manner.
#### ML Anomaly Detecion models
##### One class SVM
Train only on normal data to learn a decision boundary distribution and at inference time just check whether the input is in the decision boundary or not
##### Autoencoders
Train only on normal data an autoencoder. At inference calculate the reconstruction error and compare it to a thershold, if its under the threshold then label as normal, adversarial otherwise.
##### Variational Autoencoders
A variational autoencoder is trained on normal data and inference can be carried out in two ways. Either the same way as normal Autoencoders or by calculating the probability of seeing a certain input given the parameters learnt by the encoder part of the autoencoder.
##### GAN
A bidirection GAN is trained on normal examples. BiGAN works by giving a Discriminator both real examples as well as examples generated by the Generator. Together it is given the noise used to generate the generated example and the noise which is the result of giving the real sample to an encoder. At inference, given an input, its noise representation is calculated with the encoder, then it is fed to the generator to generate the generated version. The anomaly score is calculated as a weighted sum of the reconstruction error between real and generated and the cross entropy for the predictions given by the Discriminator for the two samples. 
#### Privacy
Formalization and study of privacy has advanced with the introduction of the concept of differential privacy.

In the mid-1990s the Massachusetts Group Insurance Commission released private
health records of state employees, showing data of individual hospital visits, for
the purpose of fostering health research. They cancelled every information they thought could lead to indentifying the people but left in birthdate, gender and zipcode which allowed a cross reference with voter data to unanonymize the files.

We define a database as having k-anonimity if each identifying information on each individual is indistinguishable from at least other k-1 individuals. To do so Personal Info can be either calcelled or aggregated to higher levels of discretization (i.e. age<30).

Errors of de-anonimization costed companies like AOL and Netflix a lot.

It is not even needed to include account information because connections alone can map to different socials and allow to identify people.

Secret info can be inferred by setting up different ad campaigns targeting specific values for the info and seeing which one is delivered to the target.

We say that a pair of databases are neighbours if they differe by just one entry.

A randomized mechanism is a function that returns aggregated information about a database which is randomized each time.

>[!definition] $\beta$-differential privacy
>A randomized mechanism $M$ provides $\beta$-differential privacy if, for all neighbouring databases $\mathbb{D}^{1}$ and $\mathbb{D}^{2}$ and all subsets of responses $T$ it satisfies:
>$Pr(M(\mathbb{D^{1}})\in T)\leq e^{\beta}Pr(M(\mathbb{D^{2}})\in  T)$
>

Which means that even if the attacker knows the whole database except the target and queries many times to compare the empirical distribution of the responses, the alternative distribution can still differe by $e^{\beta}$.

An attack to this is reconstructing the database using the knoledge of all entries except the target. We create all possibilities and then confront the behaviour of $M$ on them with the empirical behaviour on the original database. The most similar is taken as the real database.

A deterministic mechanism always responds in the same way when applied to a database.
If we define the sensitivity of it as $\Delta$ if, for every 2 neighbouring databases:

$||M(\mathbb{D}^{1})-M(\mathbb{D}^{2})||_{1}<\Delta$

Then, with $\beta>0$ and $\lambda \sim Laplace(0,\frac{\Delta}{\beta})$ we have that a deterministic mechanism $M(\mathbb{D})+\lambda$ preserved $\beta$-differential privacy.

