### **Study Plan**

#### **Day 1: Introduction and Hypersphere Learners**

1. Understand the concept of hypersphere learners and how they classify data.
2. Study the hypersphere poisoning attack, including single and double attack strategies.
3. Review the mathematics behind centroid displacement and finite-horizon attacks.
4. Explore defenses like the "Nearest out" rule and their application.

#### **Day 2: Poisoning Attacks**

1. Learn about poisoning attacks, including class-agnostic and class-specific attacks.
2. Examine real-world examples like outsourcing and pretrained attacks.
3. Understand the various trigger designs and their impact on models.
4. Study defenses such as NeuronInspect, STRIP, ABS, NIC, and Neural Cleanse.

#### **Day 3: Evasion Attacks**

1. Familiarize yourself with white-box attacks, including FGSM, PGD, Deep Fool, and Carlini&Wagner.
2. Study black-box attacks, such as gradient estimation, Zoo, and boundary attacks.
3. Review advanced strategies like Hop Skip Jump and substitute model attacks.
4. Explore defenses against evasion attacks, including detection methods, gradient masking, and robust optimization.

#### **Day 4: Network Intrusion Detection and Privacy**

1. Understand the basics of Network Intrusion Detection Systems (NIDS) and their classification.
2. Review datasets like NSL-KDD and CSE-CIC-IDS2018.
3. Explore ML-based anomaly detection techniques, including one-class SVMs, autoencoders, GANs, and variational autoencoders.
4. Study differential privacy, k-anonymity, and defenses like randomized mechanisms.

#### **Day 5: Review and Practice**

1. Revisit key concepts and mathematical formulations.
2. Work on applying theoretical knowledge to practical scenarios.
3. Test your understanding with the questions provided below.

---

### **Test Questions**

### **Hypersphere Learners**

1. **What is a hypersphere learner, and how does it classify data as normal or abnormal?**  
    A hypersphere learner is a machine learning algorithm that classifies data points as normal or abnormal based on whether they fall inside or outside a learned hypersphere. The center and radius of the hypersphere are determined during training.
    
2. **Explain the concept of centroid displacement in hypersphere poisoning attacks.**  
    Centroid displacement occurs when an attacker inserts tampered points into the training data to shift the hypersphere's centroid toward a target point xAx^AxA. This reduces the distance between xAx^AxA and the centroid, potentially causing xAx^AxA to be misclassified as normal.
    
3. **How does a finite-horizon attack differ from other poisoning strategies?**  
    A finite-horizon attack assumes that the model uses a data-dropping mechanism, replacing older points with new ones during retraining. The attack strategy must account for which points will be dropped and how new points influence the centroid, making it more constrained compared to general poisoning attacks.
    

---

### **Poisoning Attacks**

4. **Distinguish between class-agnostic and class-specific poisoning attacks.**
    
    - **Class-agnostic attacks**: Every input stamped with the trigger is classified as the attacker’s target class, regardless of its original class.
    - **Class-specific attacks**: Only inputs with the trigger **and** belonging to a specific original class are classified as the attacker’s target class.
5. **What is the purpose of Neural Cleanse in defending against poisoning attacks?**  
    Neural Cleanse identifies potential triggers by finding the minimal perturbation needed to force misclassification to a specific target class. Once triggers are identified, it applies filtering, pruning, or unlearning techniques to remove their effects.
    
6. **Describe how STRIP detects adversarial inputs.**  
    STRIP works by superimposing unrelated images onto the input and observing the model’s output entropy. Low entropy for a specific class suggests adversarial tampering, as normal inputs produce more variable outputs.
    

---

### **Evasion Attacks**

7. **Compare FGSM and PGD attacks in terms of their methodology and impact.**
    
    - **FGSM (Fast Gradient Sign Method)**: Generates adversarial examples with a single step of perturbation in the gradient direction. It is fast but less precise.
    - **PGD (Projected Gradient Descent)**: Iteratively applies small FGSM steps, fine-tuning the adversarial perturbation. It is more effective but slower than FGSM.
8. **What is the main advantage of Deep Fool attacks over other evasion strategies?**  
    Deep Fool minimizes the perturbation required to misclassify an input, resulting in adversarial examples that are visually indistinguishable from the original inputs.
    
9. **How does the Hop Skip Jump attack improve upon the boundary attack?**  
    The Hop Skip Jump attack uses binary search to efficiently locate decision boundaries and gradient estimation to reduce the number of queries needed, making it faster and more query-efficient than the boundary attack.
    

---

### **Defenses Against Adversarial Attacks**

10. **What are the limitations of gradient masking techniques?**  
    Gradient masking techniques can give a false sense of security because attackers can bypass them using methods like approximating gradients or employing alternative attacks like black-box methods.
    
11. **How does adversarial training enhance model robustness?**  
    Adversarial training involves augmenting the training dataset with adversarial examples and training the model to classify them correctly. This helps the model learn to handle adversarial inputs, improving robustness.
    
12. **Explain the role of regularization in preventing adversarial attacks.**  
    Regularization methods, such as Deep Contractive Networks or Parseval Networks, constrain model behavior to reduce sensitivity to small input perturbations. This helps limit the model’s ability to overfit adversarial noise.
    

---

### **Network Intrusion Detection**

13. **What are the primary differences between signature-based and anomaly-based intrusion detection systems?**
    
    - **Signature-based systems**: Detect attacks by matching traffic against known attack patterns. They are effective for known attacks but fail with new ones.
    - **Anomaly-based systems**: Identify deviations from normal traffic patterns, making them better for detecting unknown attacks but prone to false alarms.
14. **How can GANs be used in anomaly detection?**  
    GANs can generate normal data distributions using a bidirectional architecture. Anomaly scores are computed using reconstruction error and discriminator outputs to identify inputs that deviate significantly from normal behavior.
    

---

### **Privacy**

15. **Define differential privacy and explain its significance in protecting sensitive data.**  
    Differential privacy ensures that the output of a randomized mechanism does not change significantly if a single individual’s data is added or removed. This protects individuals' privacy by preventing their data from being inferred through repeated queries.
    
16. **How does the Laplace mechanism ensure $\beta$-differential privacy?**  
    The Laplace mechanism adds noise drawn from a Laplace distribution scaled to the sensitivity of the function (Δ\DeltaΔ) divided by the privacy budget (β\betaβ). This ensures that query responses are indistinguishable within an eβe^\betaeβ factor, preserving privacy.