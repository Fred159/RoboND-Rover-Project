## Search and Sample Return Project
This notebook contains the functions from the lesson and provides the scaffolding from Udacity. Here is the steps.

1. First,just run each of the cells in the notebook, examine the code and the results of each.
2. Run the simulator in "Training Mode" and record some data.
3. Change the data directory path (2 cells below) to be the directory where I saved data
4 Test out the functions provided on data
5. Write new functions (or modify existing ones) to report and map out detections of obstacles and rock samples (yellow rocks)
6. Populate the process_image() function with the appropriate steps/functions to go from a raw image to a worldmap.
7. Run the cell that calls process_image() using moviepy functions to create video output
8. Modify perception.py and decision.py to allow rover to navigate and map in autonomous mode

### Color Thresholding
1. Define the color thresholding function from the lesson and apply it to the warped image

2. For obstacles, invert color selection that used to detect ground pixels. Because obstacle's color is dark, it can be detected by using the color threshold. Navigatable pixel is bigger than the threshold, the obstacle pixel is under the threshold.

3. For rocks, a lower and upper boundary in color selection to be more specific about choosing colors. Rock's color is different from obstacle and navigatable area. Rock is yellow. So using the cv2.inrange function can detect rock perfectly.

（cv2 read image from BGR format~）

### Coordinate Transformations
Define the functions used to do coordinate transforms and apply them to an image. Functions referenced to Udacity's code.

1. 'rover_coords' is to convert image coordinate to rover coordinate.
2. 'to_polar_coords' is to convert pixel coordinate to radial coordinate in rover coordinate.
3. 'rotate_pix' is to convert rover's coordinate into world coordinate. Rotate the pixels coordinate same with world coordinate.
4. 'translate_pix' is to conver rover's coordinate into world coordinate. The difference between 'rotate_pix' is that 'translate_pix' mainly convert(translate) origin point of coordinate.
5. 'pix_to_world' is combine the 'rotate_pix' and 'translate_pix' function.
6. distance and angle of each pixel are calculated by using the atan2(y,x) function in 'to_polar_coords' function.
7. Finally, the rover's heading angle is decided by mean of angles of pixels.

### Read in saved data and ground truth map of the world
Using pandas read pre-saved log.csv file. With this step, algorithm can read matched image file and rover's state. Databucket class has below attributes.

images
xpos : x position
ypos : y position
yaw : vehicle yaw angle
count
worldmap
ground_truth
So algorithm can communication(read data) with pre-saved files.

### Process stored images
Define a pipeline to process input image.

1. Prspective transform(source and destination points define and using cv2.getPerspectiveTransform function to realize perspective.
2. Apply color threshold to extract different targets. Three targets are obstacle, rock, navigatable region.
3. Convert color threshold images into rover coordinate. This step is to extracting rover's heading angle. Actually , we don't have to convert rover's coordinate that like 'rover_coords' function defined.( y is in [-160,160]) The y axis also can be y= [0,320]. But in next algorith, it uses 'atan2' function to extract rover's angle. 'atan2''s range is in [-pi , pi] . It is very convinient to extact rover's angle.
4. By using 'to_polar_coords' , distance and angle's can be extracted. Here, 'atan2' function is used to extract angles. Distance is calculated by using pixels' position. Obstacle, rock, navigatable region are translate into polar coordinate.
5. Now, we know the distance and angle of obstacle,rock and navigatable region. But it is still in pixel space. So this step, we use the rover's position, pixel position, angle and scale, mapping data into world coordinate. Especially, scale is a main parameter. It decides if correct proportion is used.
6. Then we get the obstacle map in world coordinate, rock map in world coordinate and navigatable area in world coordinate.
7. Save obstacle, rock, navigatable region map into world map. Then we can update these information into world map. This step is map update. This step is the final step of vision based SLAM.

### Result in simulator autonomous mode 
Rover mapped more than 40%'s of map with 60% accuracy.
Simulator result proved that algorithm worked well.
[!(link)]


### Problem
1. Rover turn cycle in a specify scenario. So how to make rover jump out of this circle is a problem.
2. Rover's steering control is not good enough. It always bounce and not stable.
3. Decision making part still needs improvement in high level decision.

------------------------------------------------------------------------------------------------------------------------------------

[//]: # (Image References)
[image_0]: ./misc/rover_image.jpg
[![Udacity - Robotics NanoDegree Program](https://s3-us-west-1.amazonaws.com/udacity-robotics/Extra+Images/RoboND_flag.png)](https://www.udacity.com/robotics)
# Search and Sample Return Project


![alt text][image_0] 

This project is modeled after the [NASA sample return challenge](https://www.nasa.gov/directorates/spacetech/centennial_challenges/sample_return_robot/index.html) and it will give you first hand experience with the three essential elements of robotics, which are perception, decision making and actuation.  You will carry out this project in a simulator environment built with the Unity game engine.  

## The Simulator
The first step is to download the simulator build that's appropriate for your operating system.  Here are the links for [Linux](https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Linux_Roversim.zip), [Mac](	https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Mac_Roversim.zip), or [Windows](https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Windows_Roversim.zip).  

You can test out the simulator by opening it up and choosing "Training Mode".  Use the mouse or keyboard to navigate around the environment and see how it looks.

## Dependencies
You'll need Python 3 and Jupyter Notebooks installed to do this project.  The best way to get setup with these if you are not already is to use Anaconda following along with the [RoboND-Python-Starterkit](https://github.com/ryan-keenan/RoboND-Python-Starterkit). 


Here is a great link for learning more about [Anaconda and Jupyter Notebooks](https://classroom.udacity.com/courses/ud1111)

## Recording Data
I've saved some test data for you in the folder called `test_dataset`.  In that folder you'll find a csv file with the output data for steering, throttle position etc. and the pathnames to the images recorded in each run.  I've also saved a few images in the folder called `calibration_images` to do some of the initial calibration steps with.  

The first step of this project is to record data on your own.  To do this, you should first create a new folder to store the image data in.  Then launch the simulator and choose "Training Mode" then hit "r".  Navigate to the directory you want to store data in, select it, and then drive around collecting data.  Hit "r" again to stop data collection.

## Data Analysis
Included in the IPython notebook called `Rover_Project_Test_Notebook.ipynb` are the functions from the lesson for performing the various steps of this project.  The notebook should function as is without need for modification at this point.  To see what's in the notebook and execute the code there, start the jupyter notebook server at the command line like this:

```sh
jupyter notebook
```

This command will bring up a browser window in the current directory where you can navigate to wherever `Rover_Project_Test_Notebook.ipynb` is and select it.  Run the cells in the notebook from top to bottom to see the various data analysis steps.  

The last two cells in the notebook are for running the analysis on a folder of test images to create a map of the simulator environment and write the output to a video.  These cells should run as-is and save a video called `test_mapping.mp4` to the `output` folder.  This should give you an idea of how to go about modifying the `process_image()` function to perform mapping on your data.  

## Navigating Autonomously
The file called `drive_rover.py` is what you will use to navigate the environment in autonomous mode.  This script calls functions from within `perception.py` and `decision.py`.  The functions defined in the IPython notebook are all included in`perception.py` and it's your job to fill in the function called `perception_step()` with the appropriate processing steps and update the rover map. `decision.py` includes another function called `decision_step()`, which includes an example of a conditional statement you could use to navigate autonomously.  Here you should implement other conditionals to make driving decisions based on the rover's state and the results of the `perception_step()` analysis.

`drive_rover.py` should work as is if you have all the required Python packages installed. Call it at the command line like this: 

```sh
python drive_rover.py
```  

Then launch the simulator and choose "Autonomous Mode".  The rover should drive itself now!  It doesn't drive that well yet, but it's your job to make it better!  

**Note: running the simulator with different choices of resolution and graphics quality may produce different results!  Make a note of your simulator settings in your writeup when you submit the project.**

### Project Walkthrough
If you're struggling to get started on this project, or just want some help getting your code up to the minimum standards for a passing submission, we've recorded a walkthrough of the basic implementation for you but **spoiler alert: this [Project Walkthrough Video](https://www.youtube.com/watch?v=oJA6QHDPdQw) contains a basic solution to the project!**.


