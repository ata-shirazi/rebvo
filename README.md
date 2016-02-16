
# REBVO
## Realtime Edge Based Visual Odometry for a Monocular Camera

Tarrio, J. J., & Pedre, S. (2015). Realtime Edge-Based Visual Odometry
for a Monocular Camera. In Proceedings of the IEEE International Conference
on Computer Vision (pp. 702-710).

REBVO tracks a camera in Realtime using edges. The system is split in 2 components.
An on-board part (rebvo itself) doing all the procesing and sendid data over UDP 
and an OpenGL visualizer. 

### System requirements

In ubuntu and most linux dist this libraries can be dowloaded directly from the
 repos, exept for TooN.

-- C++11
-- Linux, X11, v4l2
-- OpenGL development libraries (GL,GLU,glut)
-- TooN mathematical library (http://www.edwardrosten.com/cvd/toon.html)
-- Lapack (for advanced TooN functions)
-- LibAV (Video Codecs)
-- LibGD (Image managment)
-- Optionally NE10 for ARM Neon optimizations

### Compiling

REBVO has been deveped using QT creator, so a project file is provided in each of the
components folder, (rebvo and visualizer). Also two makefiles are provided on each 
of the folders, for amd64 and arm arquitectures. The first can easily be edited for 
x86.

#### Compile using NE10

The system can use the NE10 for neon simd support. In order to use it, the
correspondant line has to be uncommented in the arm makefile. 

### Configuring the system

A GlobalConfig text file can be found in each of the folders. This should be tuned to
your camera and network. All the configuration options has to be present on the files
in order for the system to start. The structure of the file is self explanatory.

#### Basic configs in rebvo GlobalConfig

This things you have to configure in order for the system to work.

-- CameraDevice: the v4l2 camera device
-- ZfX,Y: Camera focal length
-- PPx,y: Camera principal point
-- ImageWidth,Height: size to use
-- FPS: Frames per Seconds

-- VideoNetHost,Port: IP,Port of the host to send data to
-- SetAfinity: if the set to 1, the threads has to be distributed manually in 
		existing processors, this can increase speed

-- DetectorThresh: This is a fixed threshold to the edge detector only used
	 	   if autothresh is off (DetectorAutoGain = 0)
		   Is a camera dependant parameter so it should be tunned for
		   best performance. 

-- ReferencePoints: If auto thresh is on (DetectorAutoGain>0), number of KL
		   points to use. Ussual values are 5000 for 320x240 and 
		   15000 for 640x480 

-- DetectorMax,MinThresh: Limits on threshold tunning, to prevent excesive 
		   noise on images with no edges. Similar to DetectorThresh,
		   camera dependant parameters.

#### Basic configs in visualizer GlobalConfig

ImageSize, Focal Leght and Principal Point should match the config in rebvo.

### System Usage

Just run the two programs on the same o network conected computers and move
the camera trying to maximize translation.

REBVO component acept the following command line comands:

q: Quit
r: System full reset
s: Saves in SimVideoFile (rebvo GlobalConfig) SimVideoNFrames frames of
   uncomressed video for further replay. It does a direct write to disk,
   so it can loose frames depending on the system. The format is described
   in videocam.h
p: Takes Snapshot of the current image


The visualizer opens 2 kinds of windows, XLib windows (fron and top) 
displays the image and the edge detection, OpenGL windows dysplay 
the edgemap and the image in a 3D fashion.

#### Commands in the XLib windows

q: Quit
b: Clear trayectory
i: Togle show image
t: Show frame to frame matching
z,x: Increase/Decease maximun depth in top view

#### Commands in the OpenGL windows

w,s,a,d: Walk the view like DukeNuke3D
arrows: Rotate the view
f,r: flight

1..9: Load i view
Shift+1..9: Save i view
Escape: Reset View

m: Togle image rendering
n: Togle edgemap rendering
c: Togle camera rendering

t,g: Increase/decrease max distance for color scale
y,h: Same fory min distance 

i: Render edgemap depth uncertainty
v: Render Trayectory
b: Clear rayectory

,: Fix the view to world coodinates

End: Quit

#### FAQ

-- Depth is not correct, objects that are close appear far and viceversa

At to this point, rebvo relies only on filtering for initialization, this
can lead to initization errors since convergence is not warantied. If
the system didn't bootup right, just reset.

-- How is rebvo someway usefull if initizalization can fail

If gyroscope measurements are added to estimation the initialization
problem dissapears. That is the version used to fly quadrotors.
If you want that version contact the authors.

-- What about that simulation feature?

If you use the rebvo 's' command it will save video for further repetition.
You can visualize that video toggling CameraType to 1 (SimCamera)
