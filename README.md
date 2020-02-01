

# Benchmarks: Single-Object Visual Tracking

Better Viewed at local markdown readers like *Typora*.

## Dataset Overview 

|   Dataset   |                   Source                    |  Domain  |
| :---------: | :-----------------------------------------: | :------: |
|   OTB100    |         Exist Datasets(e.g. FERET)          |  People  |
|   VOT2018   |          Exist Datasets(e.g. OTB)           |  People  |
|   UAV123    |    (UAV+HD Camera; UAV+miniC; Simulator)    | Vehicles |
| TrackingNet |               Youtube(YT-BB)                | Diverse  |
|   NFS2017   |           Embedded Vision/Mobile            | High fps |
|    LaSOT    | Youtube (70 Categories defined by ImageNet) | Diverse  |
|   GOT-10k   | Youtube (retrived by semantic information)  | Diverse  |

|   Dataset   |                           Novelty                            |
| :---------: | :----------------------------------------------------------: |
|   OTB100    | The basic Performance evaluation (applied in other datasets) |
|   VOT2019   | Performance evaluation; reinitialization at tracking failures;Bounding box rotation |
|   UAV123    |          Aerial video dataset; UE4-based Simulator           |
| TrackingNet |     Dataset generate dense annotations from coarse ones      |
|   NFS2017   |     Dataset obtained from consumer devices, in high fps      |
|    LaSOT    | Large-scale dataset, Natural Language Description of every sequence |
|   GOT-10k   | Tremendous Diverse Classes; Annotation of Visible Ratios; Challenge Indicators |

|   Dataset   | Videos | Classes | Min frames | Mean frames | Median frames | Max frames | Total frames | frame rate |
| :---------: | :----: | :-----: | :--------: | :---------: | :-----------: | :--------: | :----------: | :--------: |
|   OTB100    |  100   |   22    |     71     |     590     |      393      |    3872    |     59K      |   30fps    |
|   VOT2019   |   60   |   30    |     41     |     356     |      293      |    1500    |     21K      |   30fps    |
|   UAV123    |  123   |    9    |    109     |     915     |      882      |    3085    |     113K     |   30fps    |
| TrackingNet | 30643  |   21    |     -      |     470     |       -       |     -      |    14.42M    |   30fps    |
|   NFS2017   |  100   |   33    |    169     |    3830     |     2448      |   20665    |     383K     |   240fps   |
|    LaSOT    |  1400  |   70    |    1000    |    2506     |     2053      |   11397    |    3.52M     |   30fps    |
|   GOT-10k   | 10000  |   563   |     4      |     150     |       -       |    1480    |     56K      |   10fps    |

## Evaluation Methodology: Precision, Accuracy, Robustness

### P -- Precision  

Calculating center position error by euclidean distance between result center and annotation center.

### Visible Ratio (GOT-10k)

Visible Ratio is a continuous labeling for object occlusion. A visible ratio is a percentage indicating the approximate proportion of an object that is visible.

### IoU->AOS+AUC: Intersection over Union -- Accuracy

$$
S=\frac{|r_t\cap r_0|}{|r_t\cup r_0|}
$$

S denotes Average Overlap Score.

Corresponding to the sampled overlap threshold, Area Under Curve can be generated by average.

### OPE+TRE+SRE -- Robustness

The basic factor to evaluate robustness of a tracker is the number of times the tracker failed.

One Pass Evaluation:  Test each tracker on each sequence once.

Spatial Robustness Evaluation: Adding 8 spatial shifts(4 center shifts, 4 corner shifts) and 4 scale variations to bounding boxes in 10% frames of entire sequence. Test each tracker on each sequence for 12 times.

Temporal Robustness Evaluation: Tracker running starts from different frames. Test each tracker on each sequence for 20 times.

### OPER+SRER -- Accuracy+Stability+Robustness

One Pass Evaluation with Restart: use ground truth to restart a tracker after its failure.

Spatial Robustness Evaluation with Restart: add spatial perturbation(noise).

### Reinitialization (VOT)

The idea is developed from OPE. If the tracker loses tracking for continuous 5 frames, it is restarted with ground truth. To reduce the bias after ground truth, the first 10 frames ,i.e. Burn in, is excluded from performance evaluation.

### Average per Frame (VOT): per-frame Accuracy+Robustness

$$
P_A=\frac{1}{N}\sum_1^Naccuracy(tracker_i,frame_t)
$$

$$
P_R=\frac{1}{N}\sum_1^N(robustness(tracker_i,frame_t))
$$



|   Dataset   |                  Evaluation Methodology                   |
| :---------: | :-------------------------------------------------------: |
|   OTB100    |    P;IoU->AOS+AUC; OPE+OPER; TRE+SRE+SRER; Vitual runs    |
|   VOT2019   | P;IoU->AOS+Re-initialized(N=5)+(Burnin=10); Per-frame A/R |
|   UAV123    |         P;IoU->AOS+AUC; OPE+OPER; SRE; Simulator          |
| TrackingNet |          P; IoU->AOS+AUC; OPE with normalization          |
|   NFS2017   | IoU->AOS+AUC; Improved-Accuracy/Low-fps-Accuracy(240->30) |
|    LaSOT    |                   P; IoU->AOS+AUC; OPE                    |
|   GOT-10k   |            P; Visible Ratio; IoU->AOS+AUC; OPE            |

### Challenges

Challenging tasks in tracking:

**Camera Motion**: Abrupt motion of the camera; **(CM)**

**In-Plane Rotation**: The target rotates in the image plane; **(IPR)** 

**Out-of-Plane Rotation**: The target rotates out of the image plane; **(OPR)** 

**Deformation**: Non-rigid object deformation; **(DEF)** 

**Fully Occluded**: The target is fully occluded; **(FOC)** 

**Partially Occluded**: The target is partially occluded; **(POC)** 

**Illumination Variation**: The illumination of the target changes significantly; **(IV)** 

**Out of View**: Some portion of the target leaves the camera field of view; **(OV)**

**Viewpoint Change**: Viewpoint affects target appearance significantly. **(VC)**

**Scale Variation**: The ratio of bounding box area is outside the range [0.5, 2] after 1s; **(SV)** 

**Motion Blur**: The target region is blurred due to the motion of target or camera; **(MB)**

**Aspect Ratio Change**: The ratio of bounding box aspect ration is outside the range [0.5, 2] after 1s; **(ARC)**

**Low Resolution**: At least one ground truth bounding box has less than 1000 pixels; **(LR)**

**Fast Motion**: The motion of the bounding box is larger than the size of the bounding box; **(FM)** 

**Background Clutter**: The background near the target has similar appearance as the target; **(BC)** 

**Similar Object**: There are objects of similar shape or same type near the target. **(SO)**

### Challenge Indicators (GOT-10k)

**Occlusion/Truncation**: 
$$
OcclusionIndicator=1-v
$$
where v denotes for labeled visible ratio.

**Scale Variation**:
$$
ScaleVariationIndicator=\max\{s_i/s_{i-T},s_{i-T}/s_i\}
$$
where
$$
s_i=\sqrt{w_i\times h_i}
$$
**Aspect Ratio Change**: 
$$
AspectRatioChangeIndicator=\max\{s_i/s_{i-T},s_{i-T}/s_i\}
$$
where
$$
s_i=h_i/w_i
$$
**Fast Motion**:
$$
d_i=\frac{\|p_i-p_{i-1}\|_2}{\sqrt{s_i\times s_{i-1}}}
$$
where p denotes the object center location and 
$$
s_i=\sqrt{w_i\times h_i}
$$
**Illumination Variation**: 
$$
u_i=\|c_i-c_{i-1}\|_1
$$
where c is average object color, RGB value normalized to [0, 1].

**Low Resolution**: 
$$
LowResolutionIndicator=s_i/s^{median}, s_i\leq s^{median}
$$
where
$$
s_i=\sqrt{w_i\times h_i}
$$


|      | OTB100 | VOT2019 | UAV123 | TrackingNet | NFS2017 | LaSOT | GOT-10k |
| ---- | ------ | ------- | ------ | ----------- | ------- | ----- | ------- |
| CM   |        | 1       | 1      | 1           |         | 1     |         |
| IPR  | 1      |         |        | 1           |         | 1     |         |
| OPR  | 1      |         |        | 1           |         |       |         |
| DEF  | 1      | 1       |        | 1           | 1       | 1     |         |
| FOC  | 1      | 1       | 1      | 1           | 1       | 1     | 1       |
| POC  | 1      | 1       | 1      | 1           | 1       | 1     | 1       |
| IV   | 1      | 1       | 1      | 1           | 1       | 1     | 1       |
| OV   | 1      |         | 1      | 1           | 1       | 1     |         |
| VC   |        |         | 1      |             | 1       | 1     |         |
| SV   | 1      | 1       | 1      | 1           | 1       | 1     | 1       |
| MB   | 1      | 1       |        | 1           |         | 1     |         |
| ARC  |        | 1       | 1      | 1           |         | 1     | 1       |
| LR   | 1      |         | 1      | 1           | 1       | 1     | 1       |
| FM   | 1      |         | 1      | 1           | 1       | 1     | 1       |
| BC   | 1      | 1       | 1      | 1           | 1       | 1     |         |
| SOB  |        |         | 1      | 1           |         |       |         |

