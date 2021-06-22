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