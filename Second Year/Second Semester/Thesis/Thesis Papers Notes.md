# 1) Expressivity of Neural Networks with random weights and learned biases
Under revision
## Main Idea
Theoretical guarantees that FFNN and RNN are universal learners have already been provided in the first years of the 1900s. This study aims to asses the viability and possible limits of bias-learning, a learning paradigm where only biases are trained. This could prove the necessity of also training biases or, conversely, motivate deeper investigation on the amount of work usually shouldered by learning biases in traditional training paradigms. They study the worst case scenario of randomly initialized and frozen weights and provide theoretical guarantees of universal approximation for both FFNNs and RNNs with high probability.
## Related work
Other studies have already attemped bias-learning, following a first meta-learning process where both weights and biases were learned. This new article instead tries to avoid even the first meta-learning part by using randomly initialized weights.

This new approach is closer to mask learning, particularly the Strong Lottery Ticket Hypothesis. SLTH is a conjecture that states that the perfect parameters for a NN can be found as a sub-network of a bigger NN, basically eliminating some of the weights. This idea has been extended to eliminating whole units instead of just weights, which is called SLTH over units. Even if the approach is different (we don’t want to find the Lottery Winning subnetwork but optimize biases), some of the proofs are similar or identical to SLTH ones. Futhermore, here a new proof of SLTH over units for single layer FFNN is presented as well as the first ever proof of SLTH over units for RNNs.

## Demonstrations
First of all we show the theoretical results for FF networks:

### FFNN theoretical results

>[!definition] Suitable activation
>An activation function $\phi$ is a suitable one for what we will discuss later if, when used, has this property:
>
>$\forall h:U\rightarrow \mathbb{R}^{l}$ and for any $\epsilon>0$, $\exists \,n\in\mathbb{N}$ and parameters $\theta$ such that $sup_{x\in U}||h(x)-y_{n}(x,\theta)||\leq \epsilon$ where $U$ is compact and the norm is the 1-norm.
>

>[!definition] $\gamma$-parameter bounding activation
>A suitable activation is defined as $\gamma$-parameter bounding if it allows for universal approximation even when each parameter is bounded by $\gamma$

The ReLu and Heaviside step function are $\gamma$-parameter bounding activations for any $\gamma>0$.

>[!definition] Bias-learning activation
>$\phi$ is said to be a bias-learning activation if it is $\gamma$-parameter bounding, continuous and if  $\exists \,\tau \in \mathbb{R}$ such that $\forall x<\tau \rightarrow \phi(x)=0$.

Obviously, ReLu is a bias-learning activation.

>[!theorem] Universal Approximation for 1 layer FFNN
>Assume $\phi$ to be a $\gamma$-bias-learning activation and $h:U\rightarrow\mathbb{R}^{l}$ continuous if $U$ is compact.
>Then, $\forall \epsilon >0, \delta \in \,(0,1)$ there exists a hidden layer dimension $m$ such that a 1 layer NN with hidden layer of that dimension and weights sampled from a uniform distribution over $[-R,+R]$ (with $\gamma<R<\infty$) approximates $h$ with error less than $\epsilon$ with probability $1-\delta$.

If input and output dimensions are equal, this result works also for 1 hidden layer ResNets.
The idea for the proof of this theorem lies in the fact that, thanks to the Universal Approximation theorem, we know of the existance of a 1 layer NN that approximates the function we want with arbitrary precision. Then we can build a much bigger network with randomly sampled parameters which have non-zero probability of being very (arbitrarily) close to the parameters of the original NN. Then, using bias learning we can pick only those parameters that very close to the original ones and we end up approximating the first network which, in turn, approximates the function we want. 
### RNN theoretical results
The model treated here is described by:

$r_t = -\alpha r_{t-1} + \beta \phi(W r_{t-1} + B x_{t-1} + b), \quad \hat{y}_t = C r_t,$

$\alpha$ and $\beta$ define the time scale of the system. If $\alpha=0, \beta=1$ we get the original formulation of an RNN, alternatively we can set them to approximate continuous time dynamics with Euler’s method. To make sure that we are working with functions that operate on compact sets we must require that we start in a point that is part of an invariant set for the dynamic system, which means that the dynamics will never move us outside of that set. Futhermore, we want the dynamical system to be well defined on a slightly larger set wrt the one actually considered.

We also need to generalize the concept of the $L_{\infty}$ norm to one over finite trajectories as:

$\sup_{z_0 \in U_z, \mathbf{x}_t \in U_x} \sum_{t=1}^{T} || y_t(z_0,\mathbf{x}_t) - \hat{y}_t(r_0, \mathbf{x}_t)||$

>[!theorem] Universal approximation for RNNs
>Considering the previously reported RNN with $\phi$ being a $\gamma$-bias-learning activation and all parameters uniformly sampled from the interval $[-R,R]$, we can find a hidden layer dimension, a bias vector and a continuous hidden-state initial condition map such that the RNN approximates with arbitrary probability a chosen dynamical system with the above properties to an arbitrary level of error.

The idea of the proof is the same as the previous theorem.
## Experiments
- Bias-learned classification of Fashion MNIST by 1 layer FFNN: This was already present in the literature but was validated here as well. Fully trained networks for this task were compared with ones where only biases were trained and weights were randomly initialized and frozen, either from a uniform distribution in $[-0.1,0.1]$ or a gaussian with standard deviation $\frac{1}{\sqrt{d}}$. All paradigms were enough to learn the task and the most gain is obtained for number of trained parameters under 5000. For very low amount of trained parameters, bias learning works better than fully training. In image B a network with random weights is trained only through biases on 7 different tasks, producing 7 bias vectors. Notably, what is matched between bias-trained and fully-trained is the architecture so that the number of trained parameters in the fully trained models is 3 orders of magnitude higher than the bias-trained case. In the bottom image, bias vectors are clustered and reported for each task with their Task Variance value, which indicates in which tasks they were most starkingly active. The correlation between the task variance and the values of the activations are reported under the image, values over 0.3 show medium correlation, over 0.5 quite high.

![[Pasted image 20250213163101.png]]

- Relationship between bias learning and mask learning: In this part the comparison is made between mask learning (which entails learning sigmoid thresholds that get steeper and steeper and are substituted by step functions at test time) with bias learning. Bias learning generally performed slightly better, which was expected considering the more continuous set of values it can take. Correlation between ON units in the two models are searched: what an ON unit for mask learning is is obvious while for bias learning we define it as a unit that has a Task Variance over some threshold. Setting the threshold to 0 all units are considered, while with 0.1 an only around 6000 of the 10000 units are. Mask learning is able to solve the task with around 4500 units and generally uses units with lower variance. Anyways a moderate correlation between the ON units in the two cases is found.

![[Pasted image 20250213174630.png]]

- Bias learning autonomous dynamical systems with RNNs: First attempts at simulating a sine wave are successful. Then some observations about the Jacobian matrix show us something I haven’t understood. It appears bias learning is only able to control the derivatives in some way so we want to assess the effect of the properties of the initial random distribution. If the weights are initialized from a normal distribution $\mathcal{N}(0,\frac{g^{2}}{N})$ (with $g$ being the gain) then the value of the gain is very important. In particular, when training the model to produce the Van der Pool osciallator, any value of gain lower than 1 is ineffective, while for fully trained networks it makes no difference.

![[Pasted image 20250213180648.png]]

- Bias learning non-autonomous dynamical systems with RNNs: In this case RNNs were given the beggining of a dynamical system and had to complete the trajectory. The chosen dynamical system is a 1D view of the Lorenz attractor and the time step was set to be $\tau=27$, which was the half-width half-max of the observed autocorrelation in the chosen direction. As it can be seen in Image B, both the bias trained and fully trained models learned the task but have problems (Image C) when being fed their own output, demonstrating the compounding of the small errors in prediction.

![[Pasted image 20250213181410.png]]

- Bias learning motor controls with RNNs: A center-out reaching task usually used for primates is trained using an RNN. Constraints about near-zero velocity and force near the end of the movement were placed using regularization terms in the loss. The network was given a point on a fixed circle and had tu generate the trajectory. Though using aroung 10 times the number of epochs wrt to a fully trained RNN, the bias learning is able to have good perfomance on the task, while also generalizing to unseen points in the fixed circle.

![[Pasted image 20250213182408.png]]
## Conclusions
Key insights gained:

- There are certain activation functions that enable universal approximation results when weights are drawn uniformly from an hyper cube with any edge length. Non differentiable points in the activation function might be important to have and there could be a link between edge lenght and network scaling.
- We show task exclusive clusters of units in multi task learning.
- Importantly, even if theoretically similar, it is demonstrated that solutions found with bias learning and mask learning are different.

Future research might go in the direction of:

- Studying mathematical guarantees for non finite trajectories, maybe some that end up in a stationary distribution.
- Maybe the fact that bias learning and mask learning find different solutions is due to our imposition that the steepness of the sigmoid functions had to increase over time.
- A better grasp of the scaling of bias-learning error is needed.
- Study different initializations for the random parameters that can help produce better results.
# 2) Task representations in neural networks trained to perform many cognitive tasks
Guangyu Robert Yang, Madhura R. Joglekar, H. Francis Song, William T. Newsome and Xiao-Jing Wang
## Main idea
Studies in cognitive sciences and zero-shot learning in humans via natural language instructions hint at the possibility of a compositional representation of tasks in the brains. Brains (or NNs) that learn many tasks might be learning simple tasks and using them compositionally when asked to perform more complex tasks. These are just suppositions and practical measurements are very difficult to accomplish. Animals for example have a hard time learning a wide range of tasks at the same time, while measurements on human brains don’t have the required resolution to address questions at the single neuron level. For this reason RNNs are trained instead to simulate those systems.
## Experiments
- Training neural networks for many cognitive tasks simultaneously: Here 20 tasks are taught to a RNN simultaneously, meaning that samples from different tasks were given one after the other with no specific order. The network had as input a fixation value, indicating whether to respond or not, a stimulus in the form of 2 variables on two circular spaces, and a series of 20 neurons only one of which is ON at any time, signaling the task to be perfomed. As output the model produces a fixation value and another circular variable.
- Functional clusters encode subset of tasks in networks: To see if neurons specialize to solve subsets of tasks Task Variance is computer for each neuron across the 20 tasks and clustering is computed. 15 clusters are found and the number is obtained by maximizing the ratio of intercluster to intracluster distances. By severing the clusters one by one while monitoring behaviour on the 20 tasks we can make educated guesses on what each cluster is specialized for. More information is gained by checking also across epoch variance, showing that one cluster is related to response generation since it is active during the last epoch.

![[Pasted image 20250214115741.png]]

![[Pasted image 20250214115803.png]]

- Assess cluster formation across range of models: Different architectures, activations, weight initialization and regularization are combined. The emergence of clusters is closely related to the used activation function (measurable clusters with ReLu or Softplus, no clusters with Tanh or ReTanh) while architecture, regularization and initialization have no effect on the number of clusters.
- Relationships between neural representations of pairs of tasks: The idea here is to study the different representations of tasks in pair. This is done using a new metric, the Fractional Task Variance, which is defined as: $FTV_{i}(A,B)=\frac{TV_{i}(A)-TV_{i}(B)}{TV_{i}(A)+TV_{i}(B)}$ and indicates the $FTV$ of unit $i$ between tasks $A$ and $B$. $FTV$ has a range of $[-1,1]$ and the extremes indicate that unit $i$ is mostly active in task $A$ or $B$, respectively. From the distributions of $FTV$s of units that are active in both tasks, over all task couples, 5 task relations emerge.

![[Pasted image 20250214125727.png]]

- Dissection of a network performing a decision-making task: Two tasks, Ctx DM 1 and Ctx DM 2 are attempted, each meaning decision making with input 1 or 2 context. 3 Clusters emerge: 1, 2 and 12. From the Fractional Task Variance we see that clusters 1 and 2 are specific to the first or the second task, respectively while cluster 12 is present in both. By selectively severing them we understand that clusters 1 and 2 are necessary for the input of the contexts while 12 is tasked with the decision making. This can also be seen by checking the weights between the input and the neurons in each cluster, which shows us that inputs from mod 1 were sent to cluster 1 and likewise for 2, while the output was connected with all clusters, but mostly 12.

![[Pasted image 20250215110237.png]]
![[Pasted image 20250215110303.png]]

- Compositional representation of tasks: They compute vectors representing tasks by ???. Then relations between these vectors are discovered, such as that between a task and its delayed version the difference between the vectors is almost the same, indicating some kind of additive compositionality. This is also observed in other cases, such as that of a task and its Anti version. However, matching tasks such as DMS and DMC didn’t show these properties. Further studies show that new task learning is much faster when a network is pretrained on similar tasks (it has learned the underlying clusters).

![[Pasted image 20250215120408.png]]

- Continual task training alters neural representation: Sequential learning techniques that consist in preserving units that are important for the past tasks are used to sequentially (instead of simultaneously like before) learn many tasks. Interestingly, the $FTV$s of tasks such as Ctx DM 1 and 2 are different from when the tasks are learned simultaneously. The form of the $FTV$ in the sequential case is consistent with empirical measurements on primates performing those tasks and suggest that, to avoid forgetting past tasks, animals don’t always learn the optimal representation of the task.

![[Pasted image 20250215123233.png]]
## Conclusions

This paper achieved:

- Emergence of clusters of units specialized for subtasks when using realistic activation functions.
- Found properties of compositionality which is critical for cognitive flexibility and proved that tasks can be instructed by using this compositionality.
- Many tasks were learned sequentially, mimicking what happens in animals. The $FTV$ representation appear more mixed, consistently with empirical data.

Future work could study:

- Giving the task indication only at the first step, making the network have to remember it.
- Make the network understand which task it should perform on its own.
# 3. Flexible multitask computation in recurrent networks utilizes shared dynamical motifs
## Main idea
The paper is very similar to the one before this one, with the additional study of how the subtasks are codified as dynamical motifs, such as attractors, decision boundaries and rotations.
## Architecture
The architecture is the same RNN as paper 2. but with only 15 tasks available (rule vector is 15 in length) and stimulus 1 and 2 being both 2 length vectors indicating $(A\cos \theta_{1},A\sin \theta_{1})$ and $(A\cos \theta_{2},A\sin \theta_{2})$ respectively. The outputs are one value for the fixation, one for $\cos \theta$ and one for $\sin \theta$, where $\theta$ is the angle of the response. The network is described by:

$\tau \frac{d\mathbf{h}}{dt} = -\mathbf{h(t)} + \sigma(W_{rec}\mathbf{h}(t)+W_{in}\mathbf{u}(t)+\mathbf{b}_{in}+\mathbf{\xi}(t))$

$\mathbf{z}(t)=W_{out}\mathbf{h}(t)+\mathbf{b}_{out}$

$\sigma(\mathbf{h})=\ln(1+e^{\mathbf{h}})$

This approach studies the underlying dynamical motifs learned by studying the fixed points of the first equation when changing inputs.
## Experiments

- Single task networks: Networks are trained to performe only one task. The MemoryPro Task for example consists in responding in the same direction as the stimulus after a delay period. 4 time periods are individuated during the work of the network: Context, Stimulus, Memory, Response. The context is equal to the memory. The study shows a central fixed point in between a circle of other fixed points in the direction perpendicular to the reading during the Context section. During the Stimulus section, trajectories moved from the center to a stimulus-dependent fixed point in the circle around the center. During the Memory section, the fixed points seen in the Context section re-emerged, fixing the response. Finally, in the Response section, a new Ring attractor (not anymore perpendicular to the Response direction) emerged and previous fixed points were projected onto the new Ring attractor.

![[Pasted image 20250216162822.png]]

- Two task networks: Networks are trained on 2 interleaved tasks, namely MemoryPro and MemoryAnti (like the first but should respond in the opposite direction). Fixed point analysis highlighted the difference in the Stimulus section: a new unstable fixed point appeared, in between two opposite fixed points for any input. The system evolved away from the unstable fixed point moving itself in the two opposite directions based on which task was needed (Pro or Anti). The rest of the sections were the same as before. Some attempts with bigger networks were done to assess whether the reuse of fixed points was due to low computational ability but the same observations were obtained.
- 15 task networks: A modified version of Yang’s Task Variance is defined and used, together with hierarchical clusetering to find periods of tasks which reuse the same motifs. The analysis is done at the period level instead of task level because inside of a single task the motifs change from period to period.
- Shared stimulus period dynamics in 15 task networks: Two couples of tasks are considered, one couple which has same stimulus period dynamics and one which doesn’t (ReactCategoryPro/Anti and DelayAnti/ReactPro). Results show that, between tasks with similar motifs, similar intial conditions produce similar dynamics, while this is not true for tasks with different dynamical motifs.
- Dynamical motifs result in modular lesion effects: Specific unit clusters related to dynamical motifs were lesions one at a time and performance on task periods that used that dynamical motif were assessed. As expected, task periods that didn’t use the motif were not affected and viceversa.

![[Pasted image 20250216180647.png]]

- Fast learning of novel tasks by reusing dynamical motifs: A task using only dynamical motifs already present in other tasks is left out of the training. Then the model trained on the other 14 tasks is taught the last remaining task and learning speed is greatly improved. The experiment is repeated by only using 2 tasks that contained the necessary motifs (instead of all 14 tasks) and the results were similar, while when the pretraining was done on tasks that didn’t include the right dynamical motifs, learning was slow and harder. When having to learn a new dynamical motif for an unseen task period, experiments showed that the learned motifs are not always the same, but change depending on which other motifs are already learned. Models trained on only 1 task were compared with models pre-trained on 14 tasks and only later on the remaining one. Tasks with shared dynamical motifs benefited from the pre-training, while tasks with unique dynamical motifs were better trained alone.
## Conclusions
Observations:

- RNNs learn specialized computational building block, called dynamical motifs, which are reused.
- Even though RNNs are fully connected, it was found that lesioning specific unit clusters didn’t impact the performance of others.
- The process of sharing motifs is not a byproduct of limited resources but an intrinsic property.

Possible future studies:

- Learning paradigm similar to pre-training where we have a slow training used to learn many motifs and a fast training where we learn new tasks by repurposing the learned motifs.
- Further study on bifurcations, composition and computation
# 4. Natural language instructions induce compositional generalization in networks of neurons
## Main Idea 
Humans are able to perform zero-shot learning by using Natural Language to explain a task and, once learned, are able to explain the task to someone else. This work tries to reproduce this on RNNs. The idea is to train a LLM to be able to process a Natural Language input into a format that the RNN can use to get information on how the task works, then the LLM can also read the RNN and produce an explanation of the task by looking at the units.
## Architecture
Sensorymotor RNNs are trained on a set of 50 interrelated tasks. Information about which task to perform can be either given in Natural Language or in non-linguistic manner. In the second case, it can be either given as one of 50 orthogonal vectors (in SIMPLENET) or a combination of 10 orthogonal structure vectors (in STRUCTURENET) each representing dimensions in the task set. The first approach doesn’t capture relations between tasks, while the second does. The models prompted by the LLM are called LANGUAGEMODELNET and obtain 20 unique instruction formulations. To create the instruction GPT2 (normal and large), BERT, SBERT, CLIP and BoW are used. 
## Experiments

- Learning of all tasks: All instructed models are able to learn every task over 95% except GPTNET, for which we relax the requirement to 85%. SBERTNET(L) and SBERTNET obtain 97% and 94% accuracy, respectively.
- Genergalization to novel tasks: Models are trained on 45 tasks and then tested on the 5 held out tasks. The baseline in zero-shot learning performance is given by SIMPLENET, which achieves 39%, thanks to common patterns between unseen tasks and pre-trained ones. GPT (57%), BoWNET(64%) and GPTNET(XL) (68%) follow. Finally, using only 4% of the parameters of GPTXL, CLIPNET achieves 68% average accuracy. SBERTNET gets 79% and SBERTNET (L) gets 83%. Finally, STRUCTURENET (which knows the structure of the task from the start) achieves only slightly better, at 88%.

![[Pasted image 20250217105008.png]]

   To prove that the best models achieve that performance by using the semantic information of the embeddings, first the models are given the sensory inputs for one task but the description of another: the best models suffered the most.
   When holding out all tasks from the same familiy, the results showed that the models could generalize anyways. When allowing fine tuning on the held out tasks, models performed even better, achieving STRUCTURENET performance. Using linear combinations of rules in SIMPLENET increased performance but LANGUAGEMODELNETs still outperformed in this setting. Adding non-linear layers before giving the embedding to the network didn’t help. These observations tell us that the quality of the LM embeddings are given by the extent to which the embedding models were trained on sentences. Finally, the difficulty to explain a task is shown to have an impact on performance for that task. LANGUAGEMODELNETs have a steep drop in performance on tasks related to conditional clauses or deductive reasoning. The drop is more prominent than in STRUCTURENET, showing that there is an additional barrier in the language part. 

![[Pasted image 20250217110401.png]]

- Shared structure in language and sensorymotor networks: All observations about clustering done in previous papers are also found here. Furthermore, the geometry of RNN internal representations of tasks and rule input vectors are studied. Models trained on DMMod1,DMMod2,AntiDMMod2 and tested on AntiDMMod1 are reported in the graphs. STRUCTURENET and SBERTNET(L) obtain similar representations. The graphs show the 3 principal components of the network activities and input rule vectors. SBERTNET (L) learned compositionality.

![[Pasted image 20250217112818.png]]

   Cross-Conditional Generalization performance measures the ability of a linear decoder to differentiate between task representations. It is basically a measure of how much the embeddings have learned abstract task axis to place tasks on. Bad models have low scores across embedding models and jump at the end because that’s what gets the feedback during training. Good models already learn semantic relations during the embedding part.

![[Pasted image 20250217113535.png]]

- Semanting modulation of single-unit tuning properties: Some units in the networks are found to have specific roles for tasks. For example in the “Go” task family, units 42 indicates direction selectivity which shifts by $\pi$ between Pro and Anti tasks. Also, during "Matching" family tasks, unit 14 indicates the response based on the distance between the inputs, based on if the task is matching or non matching.

![[Pasted image 20250217115516.png]]

- Linguistic communications between networks: An output channel is added, which is trained to produce instructions based on the state of the network units. Its performance is tested by giving the instructions to a partner model and make it perfom the task. Copying the embedding is bad and doesn’t have the expected transferability. By passing through natural langugage we can get highly transferrable instructions.
## Conclusions
Achieved:

- Language Models are able to translate Natural Language instructions to embeddings capable of teaching RNNs new, unseen tasks.
- Language Models are able to read RNN activations to produce an explanation of the task it is performing.
- There are some units that condify specific information which is fundamental for tasks. These units are able to switch behaviour based on semantic information obtained from the task embeddings.
# 5.  The role of population structure in computations through neural dynamics
## Main idea
There are two main paradigms to study how RNNs learn cognitive tasks:

- Determining the computational role of recurrent units and sorting them into functional populations based on their responses.
- Studying the dynamics of the whole RNN in terms of low-dimensional trajectories of activity.

Two different studies found contraddicting results to the question of whether or not functional populations existed in certain real brain sections. Raposo et al. found no evidence of non-random population structures in posterior parietal cortex while Hirokawa et al. found evidence of it in prefrontal cortex. Hirokawa’s conjecture is that in order to see functional populations emerge in higher cortical areas more complex tasks should be employed.

The question that emerges is: Are there some tasks that require functional populations or can any task be learned with random population structure?

The results of the experiments showed that most tasks can be learned with a random pupulation structure but situations that required reconfiguration of input-output (specific tasks or multitasking settings) were show to require the emergence of functional populations.
## Experiments
First, each neuron is represented as a point in a selectivity space. This space has different dimensions depending on the task and the coordinates of a point representing a neuron are the coefficients of the linear fits between the neuron’s firing and task specific variables such as context, stimulus, etc.
These points are then compared with a null distribution represented by a multivariate gaussian with same covariance as the real points. Using the ePAIRS test we can easily see which tasks show random (similar to the multivariate gaussian) behaviour and which have developed functional groups.

Next, the same study is applied in the connectivity space. When training simple tasks, the recurrent connectivity matrix usually ends up being of low rank, which helps in having a low dimensional connectivity space. Analogous results to before are obtained.

The two previous approaches demonstrate correlation but not causation. To prove the latter, we start by creating, for each task, new low rank networks with connectivities randomly sampled from a multivariate gaussian with same mean and covariance obtained by the real networks trained on those tasks. Networks for tasks which we believed not to need any non-random structure still had near-perfect performance, while the others had bad performances. In the second case, the need of these non-random structures in connectivity influences the emergence of non-random functional groups in selectivity.

SOMETHING ABOUT LATENT DYNAMICS

First, the latent dynamics for random population networks is studied. For these types of networks, the connectivity is fully characterized by a series of covariances between connectivity parameters which can be interpreted as the overlap between them. A mean-field analysis shows that the latent low-dimensional dynamics can be reduced to a latent circuit where **internal variables** $k_{r}$ intergrate external inputs $u_{s}$ and interact with each other via **effective couplings** which depend on the overlaps and on activity dependant gain factors.
With this method it is shown that networks for tasks for which random structure is sufficient are the ones that can be implemented by a fixed effective circuit. If the latent circuit is fixed and the population structure is random, the dynamics is strongly constrained and can’t solve more complex tasks.

Networks developing subpopulations can give more freedom to the possible dynamics of the latent circuit. First, networks trained on tasks that showed population formation were represented in connectivity space and clustered with a Gaussian mixture clustering algorithm. Then, each population is assigned a series of covariances between the connectivity vectors of the points that belong to the cluster. Finally we can modify the creation of models with same characteristics we’ve done before by creating them by first sampling which population a neuron is in and then sampling its connectivity parameters using the covariances of that populations. By repeating this with different numbers of fitted populations and comparing performance with the original fully trained network, we can determine the lowest number of populations possible.
Tho show why networks which have subpopulations of neurons allow for more complex dynamics we once again use a mean field analysis and find out that they can still be represented by effective circuits where internal variables integrate inputs and interact with one another through effective couplings. What changes this time is the form of the effective couplings, before they were defined by the overlaps and a global gain parameter, while now each population has its own overlaps and its specific gain value. The general overlaps are then obtained as sum of populations specific overlaps weighted by their gains. The tasks observed to have multiple subpopulations show very different overlaps between context inputs and input selection vectors in the 2 subpopulations, this completely changes the effective circuit based on the context and allows to integrate only the needed information.
## Conclusions
