# Automatic Number Plate Recognition



Automatic Number Plate Recognition (ANPR) is made upon Tensorflow and uses YOLOV4  as the statte of the art detection model in order to detect license plate and recognises it.


# Steps to run this project on your local machine.


## You will require to download.

- (https://www.python.org/ftp/python/3.9.5/python-3.9.5-amd64.exe)-(Python)
- (https://repo.anaconda.com/archive/Anaconda3-2021.05-Windows-x86_64.exe)-(Anaconda)
- (https://www.python.org/ftp/python/3.9.5/python-3.9.5-amd64.exe)-(Pytesseract)




## Getting Started



Open Your Anaconda

```sh
git clone https://github.com/stalord63/licenseplatedetection.git
```

### Conda (Recommended)

```bash
# Tensorflow CPU
conda env create -f conda-cpu.yml
conda activate yolov4-cpu

# Tensorflow GPU
conda env create -f conda-gpu.yml
conda activate yolov4-gpu
```
### Pip
```bash
# TensorFlow CPU
pip install -r requirements.txt

# TensorFlow GPU
pip install -r requirements-gpu.txt
```
Run the above commands.
## Using Custom Trained YOLOv4 Weights

USE MY LICENSE PLATE TRAINED CUSTOM WEIGHTS: https://drive.google.com/file/d/1EUPtbtdF0bjRtNjGv436vDY28EN5DXDH/view?usp=sharing

# Copy the downloaded weights in data folder of the cloned repository.
![alt text](https://github.com/stalord63/licenseplatedetection/blob/main/Capture.JPG)


# Make a custom.names file in the classes folder and write the class name license_plate in it and save.
![alt text](https://github.com/stalord63/licenseplatedetection/blob/main/Capture2.JPG)
# In core/config.py change the name from coco.names to custom.names.
![alt text](https://github.com/stalord63/licenseplatedetection/blob/main/Capture3.JPG)

## Custom YOLOv4 Using TensorFlow
The following commands will allow you to run your custom yolov4 model.(Run on anaconda prompt.)
```
python save_model.py --weights ./data/custom.weights --output ./checkpoints/custom-416 --input_size 416 --model yolov4 
```
# Output in Terminal
![alt text](https://github.com/stalord63/licenseplatedetection/blob/main/Capture5.JPG)
### Running License Plate Recognition on Images.
The license plate recognition works wonders on images. All you need to do is add the `--plate` flag on top of the command to run the custom YOLOv4 model.

Try it out on this image in the repository!
```
python detect.py --weights ./checkpoints/custom-416 --size 416 --model yolov4 --images ./data/images/car2.jpg --plate
```
# Output
![alt text](https://github.com/stalord63/licenseplatedetection/blob/main/Capture7.JPG)
![alt text](https://github.com/stalord63/licenseplatedetection/blob/main/Capture6.JPG)
### Behind the Scenes
This section will highlight the steps I took in order to implement the License Plate Recognition with YOLOv4 and potential areas to be worked on further.

This demo will be showing the step-by-step workflow on the following original image.
<p align="center"><img src="data/images/car2.jpg" width="640"\></p>

First step of the process is taking the bounding box coordinates from YOLOv4 and simply taking the subimage region within the bounds of the box. Since this image is super small the majority of the time we use cv2.resize() to blow the image up 3x its original size. 
<p align="center"><img src="data/helpers/subimage.png" width="400"\></p>

Then we convert the image to grayscale and apply a small Gaussian blur to smooth it out.
<p align="center"><img src="data/helpers/gray.png" width="400"\></p>

Following this, the image is thresholded to white text with black background and has Otsu's method also applied. This white text on black background helps to find contours of image.
<p align="center"><img src="data/helpers/threshold.png" width="400"\></p>

The image is then dilated using opencv in order to make contours more visible and be picked up in future step.
<p align="center"><img src="data/helpers/dilation.png" width="400"\></p>

Next we use opencv to find all the rectangular shaped contours on the image and sort them left to right.
<p align="center"><img src="data/helpers/contours.png" width="400"\></p>

As you can see this causes many contours to be found other than just the contours of each character within the license plate number. In order to filter out the unwanted regions we apply a couple parameters to be met in order to accept a contour. These parameters are just height and width ratios (i.e. the height of region must be at least 1/6th of the total height of the image). A couple other parameters on area of region etc are also placed. Check out code to see exact details. This filtering leaves us with.
<p align="center"><img src="data/helpers/final.png" width="400"\></p>

The individual characters of the license plate number are now the only regions of interest left. We segment each subimage and apply a bitwise_not mask to flip the image to black text on white background which Tesseract is more accurate with. The final step is applying a small median blur on the image and then it is passed to Tesseract to get the letter or number from it. Example of how letters look like when going to tesseract.
<p align="center"><img src="data/helpers/string.png" width="650"\></p>

Each letter or number is then just appended together into a string and at the end you get the full license plate that is recognized! BOOM!

### Running License Plate Recognition on Video
Running the license plate recognition straight on video at the same time that YOLOv4 object detections causes a few issues. Tesseract OCR is fairly expensive in terms of time complexity and slows down the processing of the video to a snail's pace. It can still be accomplished by adding the `--plate` command line flag to any detect_video.py commands.

However, I believe the best route to go is to run video detections without the plate flag and instead run them with `--crop` flag which crops the objects found on screen and saves them as new images. [See how it works here](#crop) Once the video is done processing at a higher FPS all the license plate images will be cropped and saved within [detections/crop](https://github.com/theAIGuysCode/yolov4-custom-functions/blob/master/detections/crop/) folder. I have added an easy script within the repository called [license_plate_recognizer.py](https://github.com/theAIGuysCode/yolov4-custom-functions/blob/master/license_plate_recognizer.py) that you can run in order to recognize license plates. Plus this allows you to easily customize the script to further enhance any recognitions. I will be working on linking this functionality automatically in future commits to the repository.

Running License Plate Recognition with detect_video.py is done with the following command.
```
python detect_video.py --weights ./checkpoints/custom-416 --size 416 --model yolov4 --video ./data/video/license_plate.mp4 --output ./detections/recognition.avi --plate
```

The recommended route I think is more efficient is using this command. Customize the rate at which detections are cropped within the code itself.
```
python detect_video.py --weights ./checkpoints/custom-416 --size 416 --model yolov4 --video ./data/video/license_plate.mp4 --output ./detections/recognition.avi --crop
```

Now play around with [license_plate_recognizer.py](https://github.com/theAIGuysCode/yolov4-custom-functions/blob/master/license_plate_recognizer.py) and have some fun!
