# High Frame Rate Recording  

High Frame Rate Recording (HFRR) software for National Instruments LabView

## Purpose:
 - Record video stream at high frame rates (above 500 fps) reliably
 - Indicate if there is a mistake in a recording
 - Save space on hard drive
 - Allow other programs to sample video stream


## Implementation

Global notes:
 - use (github.com/kmisiunas/labview-global-properties)

### Record Video
 - Use a different threads for getting images from camera, processing and saving 

Useful inbuilt buffer use:
Examples -> Vision Acquisition -> NI-MAQ -> Low Level -> LL Ring.vi

There is a high level version as well. It is not clear which one has better performance. Need to try it out. 


### Safety
 - Track how different buffers are doing. If any of them overfills, inficate that error occurred.   

First 4 pixels can encode counter. make sure they are recorded in raw somewhere.

### Saving space 
 - Use Codec designed for this (find one)
 - Cut ROI and save it to labview-global-properties
 - Add warring is selected ROI is too large
 - Break up video into multiple files

Video codecs are not seen by LabView (again). A painful procedure to fix. Use one of the standard encoders. Might need to have layered structure to allow large file recording

Codecs coping with 512x512 500fps:
 - Y800  (5)  - max size 2GB around 8000 frames (monochrome)
 - YUV 4:2:0  (6) - max size 2GB

#### Rate Estimate

assume 8 bits / px = 1B/px

512x512 px * 1000 fps * 1B/px = 262 MB/s -> 942 GB/hr
256x256 px * 1000 fps * 1B/px =  66 MB/s -> 238 GB/hr

#### Breaking up video

*make sure there is enough buffer for the operation*

Break up the video, so it does not exceed 2GB file. File naming convention:
    videoName_s4
where _s# indicates video continuation, starting from 1.


### Integration
 - occasionally take a raw and processed frame and store it in separate buffer and thread
 - Allow access to it for other programs
 - show live action from these images


## Background Readings

ring acquisition takes EVERY image from the frame grabber, and if it buffers them

http://www.ni.com/example/28709/en/

http://lavag.org/topic/7772-grab-at-high-frame-rates-and-save/

Frame loss seems to be a problem at high frame rates.
    http://forums.ni.com/t5/Multifunction-DAQ/To-increase-the-framerate-of-a-Basler-Cameralink-Camera-A504K/m-p/2398332/highlight/true#M76505

Speed up tricks: 
    disable (or uninstall completely) antivirus software and other things which may eat your I/O bandwidth. In my case I am disabling even the network card and file indexing. Good practice is also to have two HDDs: one for OS and programs, another one for frame dump. Disable indexing on a 2nd drive.


Manual:
    http://webhome.weizmann.ac.il/home/fesegre/Gonzales/ManualeStreamer.html
