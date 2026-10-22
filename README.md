## Join Our Community on Discord

[![Discord](https://img.shields.io/badge/Join%20Discord-Chat%20Now-blue.svg)](https://discord.gg/r2UGkH7qg2)

Feel free to join our Discord server to discuss and get support!

You can also visit the [wiki](https://github.com/jareff-g/AfterScan/wiki), where you can find a [description of the AfterScan UI](https://github.com/jareff-g/AfterScan/wiki/AfterScan-user-interface-description), and a [FAQ](https://github.com/jareff-g/AfterScan/wiki/Frequently-Asked-Questions) with answers to the most common questions

# AfterScan - R8/S8 film post-processing utility for T-Scann8 (or any similar film scanner)

AfterScan can handle several post-processing tasks once film scanning is completed in ALT-Scann8.

Originally created for the T-Scann 8 project (Torulf Holmström, http://tscann8.torulf.com/index.html), but probably can be used for other frame-by-frame film scanners as well.

Actions that can be performed by AfterScan include:
- Stabilization (taking the film sprocket hole as reference)
- Cropping
- Image sharpening
- Noise removal
- Exposure fusion
- Video generation

And that's about it. Other post-processing jobs (color correction) can be done, if required, with other tools.

This tool relies in the following open source projects to achieve its objectives. Need to be installed in the system used to run AfterScan:
* [Python](https://www.python.org/)
* [OpenCV](https://opencv.org/)
* [NumPy](https://numpy.org/)
* [FFmpeg](https://ffmpeg.org/)
* [Pillow](https://python-pillow.org/)

## macOS setup (Homebrew)

From the AfterScan project folder, install the system dependencies and create a
local Python environment:

```sh
brew install python@3.13 python-tk@3.13 ffmpeg
"$(brew --prefix python@3.13)/bin/python3.13" -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
```

Start AfterScan from the project folder so it can find its image resources:

```sh
.venv/bin/python AfterScan.py
```

The analysis utility uses the same environment:

```sh
.venv/bin/python AfterScanAnalysis.py
```

## How it works:
Sprocket holes in 8mm films are expected to be in a very precise location for each film type (S8/R8). What this tool does is to detect, thanks to OpenCV, the hole(s) in each frame, and then shift the frame as required so that the holes fall in the expected position. This process is individual for each frame, so the length of the scanned film should have no effect on the result. A couple of points to highlight:
- Make sure that the input files provided to the tool contain each a full frame, with the sprocket hole(s) 50% visible at least. Otherwise the results will not be good
- When dealing with overexposed frames, where the sprocket hole is not clearly visible, the tool might not be able to stabilize the frame properly. 

## Basic instructions:
1) Select the source and target folders. They have to be different folders; frames will be taken from the source folder and, after processing, saved in the target folder, to be used at the video generation step, or by a third party tool to perform further processing
2) Using the current frame slider , search for a frame that is fully visible, and click on the 'Image crop area' to define the part of the image you want in the output files
3) Select the 'Stabilize' and 'Crop' checkboxes to enable both processes
4) Select the film type (S8/R8). Might not be necessary since when loading the source frames, the tool should detect the film type, and propose a change if the setting is incorrect
5) Finally, if you want the tool to generate the video, you can select the relevant checkbox. The checkbox 'Skip frame regeneration' is there to allow generating again all the stabilized/cropped frames in case they are already there, and go directly to the video generation step. Options available when video is generated:
   - Missing output frames are skipped automatically, including gaps in frame numbering. The remaining frames play at the selected FPS; missing files are listed in the log.
   - Skip misaligned frames: Exclude every frame in the project’s misaligned frame list (including manually corrected frames) from the video and title sequence. Remaining frames play at the selected FPS, so the video becomes shorter. This option is off by default and saved per project/job. Run stabilization first to populate the list, or use the existing list with “Skip frame regeneration”.
   - Filename: Name of the file where the video will be written. It will be stored in the target folder, together with the stabilized/cropped frames. If no name is supplied, the tool will automatically create one with a timestamp
   - FPS: Frames per second. For Super 8 this should be 18 and, I think, 16 in the case of R8
   - Quality/speed choice: Three options available, from high quality (slow) to Fast (low quality)
   
## Additional information
You can find a description of the UI elements in the [wiki](https://github.com/jareff-g/AfterScan/wiki/AfterScan-user-interface-description).



### Numbered frame folders

Select the reel's top-level folder for ALT-Scann8 scans stored in numbered
subdirectories such as `0000/picture-00001.jpg` and `0001/picture-01000.jpg`.
AfterScan reads flat files and numbered subdirectories together, including HDR
exposures, and orders frames by their frame number. Existing flat scans continue
to work. If both layouts contain the same filename, the subdirectory copy takes
priority. Other nested folders, such as templates and processing output, are not
searched for source frames.

New processed frames also use numbered subdirectories in the selected target
folder, with at most 1,000 frame numbers per directory. Frame numbers stay
continuous, all exposures of a source frame share one directory, and video
generation reads across directory boundaries. Existing files are not moved.
