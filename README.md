This repository can be used to generate video sequences for instances in the NuScene dataset.

## Example
![Example scene](https://i.imgur.com/FiD54nd.png)

Here is a video that was generated for the vehicle shown in the above scene. The video consists of cropped 2D bounding boxes across all the frames of the scene.
[![u](http://img.youtube.com/vi/8FeXaBdfsnc/0.jpg)](http://www.youtube.com/watch?v=8FeXaBdfsnc "Example video sequence")

# Get 2D annotations
Use [this script](https://github.com/nutonomy/nuscenes-devkit/blob/master/python-sdk/nuscenes/scripts/export_2d_annotations_as_json.py) to convert NuScene 3D bounding boxes to 2D bounding boxes. You can follow the instructions on setting up `nuscenes-devkit`, but here is a brief overview:

### To install nuscenes-devkit

```bash
$ git clone https://github.com/nutonomy/nuscenes-devkit.git
$ cd nuscenes-devkit
$ conda create --name nuscenes
$ conda activate nuscenes
$ conda install numpy
$ conda install scikit-learn
$ conda install shapely
$ conda install pip
```

Since we are using `pip` inside a `conda` enviroment, you will need to find the `pip` installed by `conda` and then use this to install `nuscenes-devkit`. Below is an example (replace your paths with the ones I used).
```
$ which pip
/Users/ericwiener/anaconda3/envs/nuscenes/bin/pip
$ /Users/ericwiener/anaconda3/envs/nuscenes/bin/pip install nuscenes-devkit
```

### To setup data:

First download and unpack the nuscenes data ([https://www.nuscenes.org/download](https://www.nuscenes.org/download)). Make sure to use "Full dataset (v1.0)." You can test out the mini version to test the code, but eventually you will need to run 

```bash
$ cd nuscenes-devkit
$ mkdir -p data/sets
```

Then move the downloaded (and unpacked) nuscenes folder into `nuscenes-devkit/data/sets/`. 

### Generate 2D annotations

To generate 2D annotations you need to run from (the `nuscenes-devkit` directory)

```bash
python3 python-sdk/nuscenes/scripts/export_2d_annotations_as_json.py --dataroot <path to data> --version <data version>
```

Example usage:

```bash
$ python3 python-sdk/nuscenes/scripts/export_2d_annotations_as_json.py --dataroot ~/repositories/nuscenes-devkit/data/sets/v1.0-mini --version v1.0-mini
```

After a successful conversion, the location of the output file will be printed. Example:

```bash
Saved the 2D re-projections under /Users/ericwiener/repositories/nuscenes-devkit/data/sets/v1.0-mini/v1.0-mini/image_annotations.json
```

# Generate video sequences

Put the `generate_videos.py` file from this repository in `nuscenes-devkit`. Then run with

```bash
python3 generate_videos.py --dataroot <path to data> --version <version> -o <output directory>
```

```bash
python3 generate_videos.py --dataroot ~/repositories/nuscenes-devkit/data/sets/v1.0-mini --version v1.0-mini -o ~/repositories/nuscenes-devkit/output
```

There are multiple options for configuring the video sequence files created. Usage:
```
usage: generate_videos.py [-h] [-d DATAROOT] [-v VERSION] [-o OUTPUT] [-f FPS] [-m MINIMUM_FRAMES] [-p MINIMUM_PERCENTAGE]
                          [--visibility VISIBILITY] [-s SIZE SIZE] [-c CATEGORIES [CATEGORIES ...]]

optional arguments:
  -h, --help            show this help message and exit
  -d DATAROOT, --dataroot DATAROOT
                        The path to the root directory where the data is stored
  -v VERSION, --version VERSION
                        The NuScene's data version
  -o OUTPUT, --output OUTPUT
                        The output video directory
  -f FPS, --fps FPS     Frames per second for output video (use 2 to match speed of original data)
  -m MINIMUM_FRAMES, --minimum_frames MINIMUM_FRAMES
                        The minimum number of frames an instance must have
  -p MINIMUM_PERCENTAGE, --minimum_percentage MINIMUM_PERCENTAGE
                        The minimum fraction of a frame a bounding box take up to be used (0, 1)
  --visibility VISIBILITY
                        The minimum visibility a frame is allowed ('', '1', '2', '3', '4')
  -s SIZE SIZE, --size SIZE SIZE
                        Size of the output video
  -c CATEGORIES [CATEGORIES ...], --categories CATEGORIES [CATEGORIES ...]
                        The categories to extract videos for
```