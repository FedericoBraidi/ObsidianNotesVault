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
### Object detection
- SOTA models like YOLOv5-small/large and EfficientDet-v0/8 are benchmarked on videos from the Roadside dataset, containing people and cars.
- All models detect less objects than ground truth. All have high fluctuation that is non existant in ground truth. More complex models (EfficientDet) have less fluctuation and better overall performance but use significantly more computing resources.

![[Pasted image 20241121173644.png]]
### Face detection
- Three widely used models trained on face detection on images are benchmarked on videos from the LSTN dataset.
- We find again all the characteristics we noted before. 

![[Pasted image 20241121173931.png]]

## Control for external factors
- We try to control for three possible causes of these fluctuations: motion in the images might blur the objects, lossy compression can modify pixels, environmental lighting conditions such as fast flickerings might degrade the inputs.
- To account for motion, they set up static settings with dolls and various 3D objects. The scene is illuminated in fluorescent lighting and observed from different POVs with diverse cameras by model and make. This also shows that fluctuations are not camera specific.
- To eliminate problems with lossy compression, the images are directly fetched from the camera instead of a compressed video stream. The graphs show a reduction of the flucutation, so they set up a hp test on the frame wise difference of the counts in the cases of compression and no-compression, with a mean of 0 as a null hypothesis (compression has no effect). The null hp is rejected with 99% confidence.

![[Pasted image 20241121175958.png]]

- Two metrics are then proposed to quantify the fluctuations across a group of frames: $F_{2}=\frac{||tp(i)-tp(i+1)||}{mean(gt(i),gt(i+1))}$  and  $F_{10}=\frac{||max(tp(i),\dots,tp(i+9))-min(tp(i),\dots,tp(i+9))||}{mean(tp(i),\dots,tp(i+9))}$       where $tp(i)$ is the true-positive object detection count in fram $i$ and $gt(i)$ is the ground truth. These metrics show that video compression is deteriorating the image, even if it isn’t the only culprit. 
- To eliminate the effect of flickering lights, anti-flicker lights are installed.
- Below we have the difference in detection when eliminating all three problems.

![[Pasted image 20241121180852.png]]
![[Pasted image 20241121181143.png]]

- Another hypothesis test allows us to say with 99% certainty that light flickering is deteriorating the performance of the DNN. $F_{2}$ and $F_{10}$ scores agree. However fluctuations are still present.
## Discovering the adversary
- All previous analysis leads us to believe that the real reason (or at least another one) for the fluctuations might be internal to the camera.
- The culprit is to be found in the very function of a video camera. Since it is meant to capture videos rather than photos, even if the scene is static, the camera supposes changes between consecutive frames. Furthermore, we know that a camera has access to tweak some of its parameters, to adapt to the environment it is recording. These parameters can be divided into AUTO (the user can only set a maximum) and NAUTO (if he wants, the user sets a specific value) and our hypothesis is that the change in AUTO parameters is the cause of the fluctuations.
- In particular they highlight two parameters namely, shutter speed and gain. Shutter speed is inversely linked to exposure time, a high shutter speed records nitid images which are good on their own but unsettling when played at video speed. Gain gives a measure of the amplification given to the input, high gain can do better in low light scenarios but can also introduce much more noise.
- A first look into our intuition is gained by limiting the max exposure time to two different values and see the fluctuation reaction. Lower maximum exposure time decreases fluctuations as if the potential change in exposure value from a frame to the next is one of their causes.

![[Pasted image 20241121183908.png]]

- A repeated t-test is used to reject the null hypothesis that limiting the max exposure time has no effect on fluctuations with a 99% confidence. $F_{2}$ and $F_{10}$ metrics agree.
## Implications and proposals
- Once demonstrated that SOTA models trained on image datasets (i.e. COCO, VOC) are not to be directly used on video tasks without thinking, we can try to propose some solutions to adapt the pretrained models.
- A first approach is to exploit transfer learning to train Yolov5 using a set of proprietary videos which contain objects from the car/person supercategories taken at different places and in different conditions. The dataset is composed of 34K consecutive frames of these files.
- The first 23 layers of a COCO trained Yolov5 are frozen and only the final detect module is updated, $lr=0.01$, $weight\_decay=0.0005$, $epochs=50$, $batch\_size=32$. The difference in performance on both static videos created for the previous sections and Roadside videos are:

![[Pasted image 20241121190506.png]]

- The training evidently had its effect. For the static and controlled videos, the null hypothesis that transfer-learning made no difference is rejected with 99% confidence and the impact on the performance is estimated as detecting on average an additional 2.32 objects (14.3% more). AUTO parameters were left unchanged.
- For unmodified videos in the Roadside dataset the previous null hypothesis is rejected with 99% confidence and the boost in performance is estimated to be 1 more detection on average (9,6% more). Also, the fluctuations are noticeably reduced according to $F_{2}$ and $F_{10}$.  
- We test how these improvements propagate to another video-specific task: Object Tracking. For this, MOT SORT tracker is used to track objects in contiguous frames by assigning them the same counter. If an object is lost, the tracker dies and another one is initiated whenever it is detected again. The quality of the object detection is correlated with the quality of the object tracking which we can measure by the number of total trackers created during the video. They try this on a video with ground truth 29 trackers and get 157 trackers when using the original Yolov5 model, and 94 (40% improvement) when using the new one. Trackers are manually checked to be true-positives.
- More improvements can be obtained by calibrating the softmax confidence scores, for example by averaging predictions obtained by various models, plat scaling or isotronic regression. Also, the confidence threshold for filtering out the less confident predictions.

## Conclusions
- In this work it is shown that automatically apllying image trained DNN on video tasks leads to fluctuations in accuracy. Sistematically, external factors are accounted for making fluctuations reduced but still present. Then, the impact of AUTO parameters is studied and found as another reason for them. A transfer-learning approach is proposed and applied, producing a model which is significantly more performant and less affected by the fluctuations, even when not accounting for external factors.  
