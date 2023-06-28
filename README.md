<!-- TOC -->

- [Face Re-identification with OpenVINO](#face-re-identification-with-openvino)
  - [What's this](#whats-this)
  - [Reference](#reference)
  - [Environment](#environment)
  - [Required Python packages](#required-python-packages)
  - [How to use](#how-to-use)
  - [Create Face Database](#create-face-database)
    - [1. Download Face images from CelebA](#1-download-face-images-from-celeba)
    - [2. Create CSV file](#2-create-csv-file)
    - [3. Regiter command](#3-regiter-command)
    - [4. Verify data](#4-verify-data)
  - [Run app](#run-app)

<!-- /TOC -->

# Face Re-identification with OpenVINO

## What's this

This is Face Identification Test App with Intel OpenVINO Face Re-Identification Model.

You can do followings:

* Realtime Face Re-Identification
* Face Search

Real time face re-identifiction (Youtube Link)

<a href="https://youtu.be/3eXYgTp1wpQ">
<img src="https://raw.githubusercontent.com/wiki/kodamap/face_reidentification_demo/images/RealTimeRaceReID.jpg" alt="face detection" width="75%" height="auto"></a>

Face Search

<img src="https://raw.githubusercontent.com/wiki/kodamap/face_reidentification_demo/images/FaceSearch.jpg" alt="face detection" width="75%" height="auto">

## Reference

* [Install OpenVINO Toolkit](https://docs.openvinotoolkit.org/latest/index.html)

* [face-reidentification-retail-0095](https://github.com/opencv/open_model_zoo/blob/master/intel_models/face-reidentification-retail-0095/description/face-reidentification-retail-0095.md)

* [CelebA Dataset](http://mmlab.ie.cuhk.edu.hk/projects/CelebA.html)

* [Flask Video streaming](http://blog.miguelgrinberg.com/post/video-streaming-with-flask)


## Environment

* Python 3.6+ (Required Ordered Dict)
* OpenVINO Toolkit 2021.4[^1]
* Windows 10

## Required Python packages

```sh
pip install -r requirements.txt
```

## How to use

```sh
python app.py -h

usage: app.py [-h] -i INPUT [-l CPU_EXTENSION] [-d {CPU,GPU,FPGA,MYRIAD}]
              [-d_lm {CPU,GPU,FPGA,MYRIAD}] [-d_fi {CPU,GPU,FPGA,MYRIAD}]
              [--dbname DBNAME] [--no_v4l]

optional arguments:
  -h, --help            show this help message and exit
  -i INPUT, --input INPUT
                        Path to video file or image. 'cam' for capturing video
                        stream from camera
  -l CPU_EXTENSION, --cpu_extension CPU_EXTENSION
                        MKLDNN (CPU)-targeted custom layers.Absolute path to a
                        shared library with the kernels impl.
  -d {CPU,GPU,FPGA,MYRIAD}, --device {CPU,GPU,FPGA,MYRIAD}
                        Specify the target device for Face Detection to infer
                        on; CPU, GPU, FPGA or MYRIAD is acceptable.
  -d_lm {CPU,GPU,FPGA,MYRIAD}, --device_landmarks {CPU,GPU,FPGA,MYRIAD}
                        Specify the target device for Facial Landmarks
                        Estimation to infer on; CPU, GPU, FPGA or MYRIAD is
                        acceptable.
  -d_fi {CPU,GPU,FPGA,MYRIAD}, --device_reidentification {CPU,GPU,FPGA,MYRIAD}
                        Specify the target device for Facial re-identificaiton
                        to infer on; CPU, GPU, FPGA or MYRIAD is acceptable.
  --dbname DBNAME       Specify face database name
  --no_v4l              cv2.VideoCapture without cv2.CAP_V4L
```

## Create Face Database

At first, you need to create face Database.

Here is an example of face registration from csv file.

### 1. Download Face images from CelebA

Download dataset from  [CelebA](http://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) using Google Drive  [CelebA > img > img_align_celba.zip] and extract the file.

Note: img_align_celba.zip includes **202,599 face images and 1GB size**

### 2. Create CSV file

I tested to register 20,000 faces. Change /path/to/celeba.

* celeba.csv

```txt:celeba.csv
imagepath, label
<path_to_celeba_img_align_celeba>\000001.jpg,F00001
<path_to_celeba_img_align_celeba>\000002.jpg,F00002
<path_to_celeba_img_align_celeba>\000003.jpg,F00003
..
<path_to_celeba_img_align_celeba>\000004.jpg,F00004
```

### 3. Regiter command


[dbname]_vecs.gz and [dbname]_pics.gz are created.
(You can ignore the errors during face registration.)

```sh
python registrar.py csv_register --csv celeba.csv --dbname celeba --batch_size 500
```

`celeba_vecs.gz` includes feature vectors, `celeca_pics.gz` includes image path of each face.

The size of feature vectors file produced from 20,000 face images is about 22 MB.

```sh
>dir
..
2019/06/26  22:06           159,470 celeba_pics.gz
2019/06/26  22:06        23,168,652 celeba_vecs.gz
```

Face images are saved to /static/images/`dbname`

### 4. Verify data

```sh
python registrar.py list --dbname celeba
..
19781, label:F19999 file:/static/images/celeba/F19999.png
19782, label:F20000 file:/static/images/celeba/F20000.png
Rows:19782
```

## Run app


```sh
#  dbname is the face db name (ex. celeba) 
python app.py -i cam --no_v4l --dbname celeba
```

If you use OpenVINO Toolkit 2019 R3 or earlier build please specify `cpu_extension`.

```sh
#  dbname is the face db name (ex. celeba) 
python app.py -i cam -l extension\cpu_extension.dll --no_v4l --dbname celeba
```

Access to the url

```txt
http://127.0.0.1:5000/
```
