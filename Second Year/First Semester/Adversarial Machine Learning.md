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
#### Poinsoning
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
##### Defenses against poisoning attacks
Techniques to avoid poisoning of a ML model can be divided into two groups:

- Offline inspection: The system is analyzed and cleaned before the deployment, either by cleaning the input data and retraining (if we have access to it) or by just cleaning the model (if we don’t have access to the data).
- Online inspection: After deployment the model is tested for performance.
###### NeuronInspect
For each class we create a heatmap that shows which pixels the model looks at when it ends up classificating an input into that category.
The heamaps produced for tagged input for the target class are less sparse, most smooth and persistend across different input images.
###### STRIP defense
Superimpose other images onto inputs during runtime. A low entropy of a specific class is suspicious because it means the model doesn’t care about the actual image.
###### ABS defense
Entails scanning the enire model and controlling abnormalities such as strangely high activations of a specific neuron for a target label.
###### NIC defense
Inspect the distribution of the activations at different layers of the DNN to detect abnormalities. You might do this by clustering vectors of weights at a specific layer across inputs.
###### Neural cleanse
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
###### SentiNet
This approach works particularly well on small triggers. We take an image and classify it, then we segment it and classify the subimages. If the image was triggered then we should be able to get the real label by classifying the subimage that cuts out the trigger. Then we can isolate the trigger by getting the saliency map (heatmap as in NeuronInspect) of the whole image and subtracting the saliency map for the image classified in the secondary class.
Now we have a potential trigger and we can apply it to other images to see if they get missclassified to the target class as well. Analyze the decision boundary when applying the trigger and when applying a random gaussian noise to the same portion of the image.





