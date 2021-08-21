# How to use this repository

The intent behind this project is to train a Mask R-CNN model on the pothole dataset and then identify and 'paint' potholes on images. The input dataset is quite small and therefore the accuracy is not great. It could probably be significantly improved with more training data and perhaps some additional tuning of the model build. I only wanted to test the feasibility of this approach on the dataset and at first glance it looks like it will work. It provides a great way to get something up and going to start with.

***Note***: This repository was cloned from [michhar/maskrcnn-custom: Use VGG Image Annotator to label a custom dataset and train an instance segmentation model with Mask R-CNN implemented in Keras. (github.com)](https://github.com/michhar/maskrcnn-custom), which in turn is based on Based on: https://github.com/matterport/Mask_RCNN ("Training on Your Own Dataset" section)

Please also see the file README-Original.md, it contains the original base instructions on getting the process working and may be useful if something here seems confusing or incomplete.

High level overview of getting running
A. Setup your python environment, clone the repository and install the dependencies from the project
B. Setup the data folders with content
C. Label the images
D. Run the model training script
E. Score images 



A. Setup the python environment

1. I used both the Spyder IDE and VSCode and both worked fine. VSCode provided a little more flexibility with launching different processes but everything seems to work fine in both.

2. I used Pyenv to manage environments but conda should work fine too. Python 3.7.10 was used (this is to accommodate the existing requirements.txt installation requirements). 

Install python 3.7.10. It will probably work with other versions of python. I found this worked well with the tensorflow-gpu requirement if you are going to run this on a machine with a GPU (and CUDA).

```bash
 pyenv install 3.7.10
```
and create a virtualenv for the project. This creates an env called cnn-37 using python 3.7.10 installed above
```bash
pyenv virtualenv 3.7.10 cnn-37
```

Clone the repository to a local folder (will refer to this as repo-main) . 
```bash
git clone <tbd>
```

Install the requirements from inside the project folder  
```bash 
pip install -r requirements.txt 
```
 Once the installation of these packages is complete also install imgaug
```
bash pip install imgaug
```
  I don't know why this is not in the requirements.txt file and I did not want to alter it from the original. 


B. Setup the folders with data content

1. Clone the pothole sample dataset repository to a different location than the python project
```bash
git clone https://github.com/onepanelio/road-defects-detection
``` 

2. Create a folder called data in the python project root folder and inside that create three additional folders 'val', 'train', 'test'

It should look something like this 

```text
├── LICENSE
├── README-Original.md
├── README.md
├── custom.py
├── data
│   ├── test
│   ├── train
│   └── val
├── logs
├── mask_rcnn_coco.h5
├── media
│   ├── create_drop_label.png
│   ├── create_tag.png
│   ├── export_as_json.png
│   ├── pick_drop_label.png
│   └── select_tag.png
├── mrcnn
│   ├── LICENSE-mrcnn-code
│   ├── __init__.py
│   ├── config.py
│   ├── model.py
│   ├── parallel_model.py
│   ├── utils.py
│   └── visualize.py
└── requirements.txt
```

3. Split up the images from the pothole repository clone into the 3 data folders. I  selected 55 images for train, 18 for val(idate) and 17 for test, relfecting a rough split of 60/20/20 (train, validate, test). 

C. Label the images for training

1. Download the VIA image labelling tool [Visual Geometry Group - University of Oxford](https://www.robots.ox.ac.uk/~vgg/software/via/) from the downloads section. Version 2 was used for this project, version 3 is intended more for video work
2. Unzip via to a folder (suggest not inside the python project but it should not matter) and run the via.html file, it should open a browser and be running in the new window.
3. Expand the attributes section of the Via page and add an attribute called 'type' and make its Type (yes a bit confusing) 'dropdown'. Add an option 'water_pothole' to it. This will enable you to select water_pothole as the label from a drop down list as you step through images. The label 'water_pothole' is also used in the code, if you choose to use a different label it will have to be changed in the code as well (in custom.py).
4. Select Project -> Add local files and select all the .jpg image files from the train folder. 
5. Select the first image from the list of files and draw a poyline around the area of interest. The trick is to draw around the item and use the enter key to finish the region. Continue and draw regions around all areas of interest in the picture. When finished selecting regions in a picture press escape and then click on each one in turn and select the 'water_pothole' label.
6. Once all images in the train folder have been labelled select menu item Annotation -> Export annotations as json. Depending on how your browser handles downloads it may ask you for a location or simply save to your downloads folder
7. Rename the above saved annotation file as via_region_data.json and place it inside the data/train folder. This is also hard coded in the python code.
8. Repeat the above steps for the val folder images as well. This will result in another via_region_data.json file being saved in the data/val folder.

D. Running the training script

Follow the instuctions in the original README file, this should be straightforward to get it up and running

If it runs successfully it should populate the logs folder with additional folders labelled 'experiment_xxxx' and store the epoch training snapshots in it. Training can be resumed (see below) if for whatever reason it is stopped at a certain point and it uses these checkpoint files to restart.

Tensorboard also works with the training process. In VSCode you can launch Tensorboard from inside VSCode or launch it from the command line pointing the --logdir parameter at the log folder under the project. This makes it a little easier to see which step, or model epoch, has performed the best for later use.

E. Scoring test images

The capability to score a batch of images on the trained model was also added as a command line argument. See the changes to code section below.

Changes made to the original code.

1. Added the ability to infer on an 'imagespec' as a parameter, this makes it possible for you to choose a single model snapshot as the model and 'paint' a list of images with detected results. Due to the hassles of including an '\*' in a launch.json I used the combination of {} as a replacement of an asterisk. The code then replaces the {} with \* before doing a glob operation to get a list of input images.

2. Some changes to enable restarting of a training run. The original code suggests this is possible but there is a bug that meant it did not work. I added a --resume command line argument to which you pass the path of the xx.h5 snapshot 

3. Image augmentation is in the code but was not enabled. I tried it both on and off but did not see a change in the results. It was quick and easy to try out but did not seem to improve results. 

### Credits

* [michhar/maskrcnn-custom: Use VGG Image Annotator to label a custom dataset and train an instance segmentation model with Mask R-CNN implemented in Keras. (github.com)](https://github.com/michhar/maskrcnn-custom)


