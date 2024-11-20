# Why is video analytics accuracy fluctuating, and what can we do about it?
## Introduction
- The introduction of IoT, edge computing and high-bandwidth networks have favoured the adoption of video analytic systems in various applications such as entertainment, security, healthcare, automotive, retail, etc.
- The sector is estimated to grow from the 5 billion dollars of 2020 to 21 billions in 2027.
- Videos are usually thought of as a sequence of images (frames) and the SOTA models employed for video analytics tasks are often the same models that have been trained on datasets of single images, slightly tweaked.
- The videos considered in this paper come from two well-known benchmarks, containing persons (LSTN dataset) and cars (Roadway dataset). The videos are mostly static so for tasks such as object and face detection, the ground truth doesn’t change much (if at all) between frames.
- A high degree of fluctuation is observed, this wasn’t expected and it propagates to more complex tasks (such as person recognition or object following) which use them as a starting point.
- These fluctuations are not particular to a specific model or make of cameras but are general.
- A study on the possible causes of these fluctuations is performed by carefully trying to eliminate one by one all possible causes, external or internal. Externally, lighting, video compression and motion are considered, while internally camera parameter settings are checked. Finally, it is discovered that the problem are the dynamic camera parameter changes that are made automatically to produce a pleasing video.
- In this case, we are the adversary. For us to be able to see the video better, we produce inputs that worsen the performance of the models.- Ways to mitigate these effects are explored.
## Key contributions
- They demonstrate that popular DNN models, trained on images and reused for face or object identification tasks on videos show large fluctuations in their results for consecutive frames, which look the same for a human and have the same ground truth.
- They investigate the source of these fluctuations, demonstrating the existance of an “unintentional adversary”.
- They investigate the implications of these fluctuations. They also propose a method for alleviating these effects and, following it, introduce a slightly retrained version of a previously affected model, which gains significantly in accuracy.
## Motivation
