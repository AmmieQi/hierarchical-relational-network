## [Hierarchical relational network for group activity recognition and retrieval. Mostafa S. Ibrahim, Greg Mori.  European Conference on Computer Vision 2018](https://github.com/mostafa-saad/hierarchical-relational-network/blob/master/files/Ibrahim18-eccv-Hierarchical%20-Relational-Networks.pdf)



Update: Our paper got accepted at ECCV 2018 :)
Code after ECCV conference will be available. So busy with a dozen of things.

# Extra Dataset note
The Collective Activity Dataset (CAD) consists of 44 videos and five labels used as both person action and  group activity (crossing, walking, waiting, talking, and queueing). The majority activity in the scene defines the group activity. We followed the same data split, temporal window and implementation details as [25], including AlexNet features (due to the short time; faster to train/extract features).

We used a single relational layer with a simple graph: each 3 consecutive persons (spatially in horizontal dimension) are grouped as a clique. The layer maps a person of size 4096 to 128 and final person representations are concatenated. Our 9 time-steps model's performance is 84.2% vs. 81.5% from [25]. We did not compare with [3] as it uses much stronger features (VGG16) and extra annotations (pairwise interaction).  Note that CAD has very simple "relations", in that the scene label is the label of the majority (often entirety) of the people in a scene.  However, this result demonstrates that our relational layer is able to capture inter-person information to improve classification results. 






## Contents
0. [Abstract](abstract)
0. [Model](#model)
0. [Dataset](#dataset)
0. [Experiments](#experiments)
0. [Experiments - CAD](#experiments-cad)
0. [Installation](#installation)
0. [License and Citation](#license-and-citation)
0. [Poster and Powerpoint](#poster-and-powerpoint)



## Abstract
Modeling structured relationships between people in a scene is an important step toward visual understanding. We present a Hierarchical Relational Network that computes relational representations of people, given graph structures describing potential interactions. Each relational layer is fed individual person representations and a potential relationship graph.  Relational representations of each person are created based on their connections in this particular graph. We demonstrate the efficacy of this model by applying it in both supervised and unsupervised learning paradigms. First, given a video sequence of people doing a collective activity, the relational scene representation is utilized for multi-person activity recognition. Second, we propose a Relational Autoencoder model for unsupervised learning of features for action and scene retrieval. Finally, a Denoising Autoencoder variant is presented to infer missing people in the scene from their context. Empirical results demonstrate that this approach learns relational feature representations that can effectively discriminate person and group activity classes.

## Model
<img src="https://github.com/mostafa-saad/deep-activity-rec/blob/master/img/fig1.png" alt="Figure 1" height="400" >

**Figure 1**: High level figure for group activity recognition via a hierarchical model. Each person in a scene is modeled using a temporal model that captures his/her dynamics, these models are integrated into a higher-level model that captures scene-level activity.

<img src="https://github.com/mostafa-saad/deep-activity-rec/blob/master/img/fig2-b.png" alt="Figure 2" height="400" >

**Figure 2**: Detailed figure for the model. Given tracklets of K-players, we feed each tracklet in a CNN, followed by a person LSTM layer to represent each player's action. We then pool over all people's temporal features in the scene. The output of the pooling layer is feed to the second LSTM network to identify the whole teams activity.

<img src="https://github.com/mostafa-saad/deep-activity-rec/blob/master/img/fig3.jpg" alt="Figure 3" height="400" >

**Figure 3**: Previous basic mode drops spatial information. In updated model, 2-group pooling to capture spatial arrangements of players.

## Dataset
### [Download Link](http://vml.cs.sfu.ca/wp-content/uploads/volleyballdataset/volleyball.zip)

**UPDATE 1**: many people asked for extracted trajectories. In fact, as in our code, we generate them on the fly using Dlib Tracker. I extrated and saved them to disk (I did few verifications). Hopefully this helps more. [Download](https://drive.google.com/file/d/0B_rSt5dGmwYBQkh2WFNKTjBSeWM/view?usp=sharing).

**UPDATE 2**: My College, Jiawei (Eric) He, Recently trained 2 Faster-RCNN detectors using the training detections. One detector just detects the person. The other one detects the action of the person. Each row has format: [Image name # of detections x y w h confidence category (for each detection)]. Multiple scenarios such data can be useful and cut your time. I did few verifications over them. Notice, these data are not used in our models. They are provided to help :). [Download](https://drive.google.com/file/d/0B_rSt5dGmwYBQXVqLUNKd3FUdVE/view?usp=sharing).


We collected a new dataset using publicly available **YouTube volleyball** videos. We annotated **4830 frames** that were handpicked from **55 videos** with 9 player action labels and 8 team activity labels. 

<img src="https://github.com/mostafa-saad/deep-activity-rec/blob/master/img/dataset1.jpg" alt="Figure 3" height="400" >

**Figure 3**: A frame labeled as Left Spike and bounding boxes around each team players is annotated in the dataset.


<img src="https://github.com/mostafa-saad/deep-activity-rec/blob/master/img/dataset2.jpg" alt="Figure 4" height="400" >

**Figure 4**: For each visible player, an action label is annotaed.

We used 3493 frames for training, and the remaining 1337 frames for testing. The train-test split of is performed at video level, rather than at frame level so that it makes the evaluation of models more convincing. The list of action and activity labels and related statistics are tabulated in following tables:

|Group Activity Class|No. of Instances|
|---|---|
|Right set|644|
|Right spike|623|
|Right pass|801|
|Right winpoint|295|
|Left winpoint|367|
|Left pass|826|
|Left spike|642|
|Left set|633|


|Action Classes|No. of Instances|
|---|---|
|Waiting|3601|
|Setting|1332|
|Digging|2333|
|Falling|1241||
|Spiking|1216|
|Blocking|2458|
|Jumping|341|
|Moving|5121|
|Standing|38696|

**Further information**:
* The dataset contains 55 videos. Each video has a folder for it with unique IDs (0, 1...54)
 * **Train Videos**: 1 3 6 7 10 13 15 16 18 22 23 31 32 36 38 39 40 41 42 48 50 52 53 54
 * **Validation Videos**: 0 2 8 12 17 19 24 26 27 28 30 33 46 49 51
 * **Test Videos**: 4 5 9 11 14 20 21 25 29 34 35 37 43 44 45 47
* Inside each video directory, a set of directories corresponds to annotated frames (e.g. volleyball/39/29885)
  * Video 39, frame ID 29885
* Each frame directory has 41 images (20 images before target frame, **target frame**, 20 frames after target frame)
  * E.g. for frame ID: 29885 => Window = {29865, 29866.....29885, 29886....29905}
  * Scences change quite rapidly in volleyball, hence frames beyond that window shouldn't represent belong to target frame most of time.
  * In our work, we used 5 before and 4 after frames.
* Each video directory has annotations.txt file that contains selected frames annotations.
* Each annotation line in format: {Frame ID} {Frame Activity Class} {Player Annotation}  {Player Annotation} ...
  * Player Annotation corresponds to a tight bounding box surrounds each player
* Each {Player Annotation} in format: {Action Class} X Y W H
* Videos with resolution of 1920x1080 are: 2 37 38 39 40 41 44 45 (8 in total). All others are 1280x720.

## Experiments

<img src="https://github.com/mostafa-saad/deep-activity-rec/blob/master/img/table-ac.png" alt="Figure 5" height="300" >

**Table 1**: Comparison of the team activity recognition performance of baselines against our model evaluated on the Volleyball Dataset. Experiments are using 2 group styles with max pool strategy. Last 3 entries comparison against Improved Dense Trajectories approach.

## Installation
* There are 2 internal projects: a simple one for sake of validation and other one, the real pipeline.
* Download and Install [Dlib library](http://dlib.net/).
* Download and Install [Caffe-LSTM library](https://github.com/junhyukoh/caffe-lstm). 
  * Assume your download disk path is `$lstm_path`
* `cd $lstm_path/examples`
* `git clone https://github.com/mostafa-saad/deep-activity-rec.git`
* Open makefile at examples/deep-activity-rec
  * `Update` path locations of variables CAFFE_LSTM_DIR and DLIB_DIR
  * `Update` the INCS_DIRS and LIBS_DIRS (based on your environment)
* Open examples/deep-activity-rec/ibrahim16-cvpr-simple/script-simple.sh
  * Update path variable for CAFFE
* `cd examples/deep-activity-rec`
* Compile code: `make all`
* `cd ../..`
* Run: `examples/deep-activity-rec/ibrahim16-cvpr-simple/script-simple.sh`
 * Make sure top console lines don't complain about "NOT exist directory".
 * You may validate overall console processing with file script-simple-expected-log.txt
 * If so, fix it, use script-clean.sh, run script-simple.sh.
* The code process is of multiple stages as outlined in the script file.
  * Processing should end with simple accuracy table, all of it being close to zeros.
  * The key is to check the console log and to make sure there are no errors found.
  * Otherwise, read the script and try to get the different phases and read logs to get the errors.
  * Every sub-directory under ibrahim16-cvpr-simple has 1 or more logs.
  * Directory p4-network2 should have the final model and accuracy table.
* If everything went alright, we can proceed with actual pipeline.
* Download the dataset to path deep-activity-rec/volleyball
 * Same directory structure as given deep-activity-rec/volleyball-simple
* Whatever steps/changes you did for ibrahim16-cvpr-simple, do it for ibrahim16-cvpr.
* Run: `examples/deep-activity-rec/ibrahim16-cvpr/script.sh`
* GPU/CPU note:
  * The script.sh has 2 heavy processing phases that needs CPU.
  * One can also run the following 2 scripts in parallel on CPU: script-p1-data.sh and script-p2-data-fuse.sh
  * Then Run on GPU following script: script-p1-train-p3-p4.sh
  * The main script runs all these scripts in the required order.

## License and Citation

Source code is released under the **BSD 2-Clause license**

In case using our extended dataset, please site the following 2 publications. Otherwise, cite a suitable subset of them:

    @inproceedings{msibrahiCVPR16deepactivity,
      author    = {Mostafa S. Ibrahim and Srikanth Muralidharan and Zhiwei Deng and Arash Vahdat and Greg Mori},
      title     = {A Hierarchical Deep Temporal Model for Group Activity Recognition.},
      booktitle = {2016 IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
      year      = {2016}
    }

    @inproceedings{msibrahiPAMI16deepactivity,
      author    = {Mostafa S. Ibrahim and Srikanth Muralidharan and Zhiwei Deng and Arash Vahdat and Greg Mori},
      title     = {Hierarchical Deep Temporal Models for Group Activity Recognition.},
      journal   = {arXiv preprint arXiv:1607.02643},
      year      = {2016}
    }

## Poster and Powerpoint
* You can find a presentation for the paper [here](https://docs.google.com/presentation/d/1iHMRCghn-dOYc2knvTj8Kp27RRojCsLzCbE8Ax5JCOs/edit?usp=sharing).
* You can find our CVPR 2016 poster [here](https://github.com/mostafa-saad/deep-activity-rec/blob/master/extra/poster.pdf).

<img src="https://github.com/mostafa-saad/deep-activity-rec/blob/master/extra/poster.jpg" alt="Poster" height="400" >

Mostafa on left and Srikanth on right while presenting the poster.
