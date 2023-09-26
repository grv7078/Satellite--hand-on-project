<h1>
  Satellite Imagery Inference on
  <br>Raspberry Pi Pico Microcontroller
</h1>

<br>

## About The Project

The project focuses on utilizing Deep Learning to classify Satellite Imagery, aiming to showcase the feasibility of running a highly accurate yet compact model on a Raspberry Pi Pico microcontroller. This involves exploring diverse model architectures, building State-of-the-Art models for demonstrative purposes, and investigating Transfer Learning for adapting models to various satellite types. Ultimately, a small, high-accuracy model is developed and successfully deployed on the Raspberry Pi Pico. A script running on the host system sends images to the Pico, collects results in a CSV format, and evaluates both the results and the final model. TensorFlow Lite is employed to convert and deploy the model on the Raspberry Pi Pico.

----

## Directory Structure of the project

The following is the directory structure of the project along with some explanations of each file.

```
artifacts/
  diode.uf2                       # The pre-built Pico image
  eurosat.keras                   # The final model of the project (Keras Format)
  eurosat.tflite                  # The final model of the project (TFLite Format)
  pico-inference-results.csv      # CSV of 1,000 inference results from the Pico
  eurosat-model-architecture.png  # A Netron exported diagram of the final model

notebooks/
  1.exploring-dataset.ipynb             # Exploring the EuroSAT dataset
  2.establishing-baseline.ipynb         # Establishing a sensible baseline
  3.exploring-models.ipynb              # Exploring possible models
  4.powerful-small-model.ipynb          # Building the final model
  5.tflite-model-for-pico.ipynb         # Converting Model to TFLite
  6.evaluation-and-pico-results.ipynb   # Evaluation on Test Dataset
  7.transfer-learning-models.ipynb      # Transfer Learning Models
  8.sota-models.ipynb                   # State-of-the-Art Models
  core.py                               # Helper Functions

pico/
  lib/pico-tflmicro         # Git submodule of TFLiteMicro for Pico
  CMakeLists.txt            # CMake Build File
  eurosat.h                 # Automatically generated model in C Header Format
  main.cpp                  # Source code for Pico build
  pico_sdk_import.cmake     # CMake Script to locate Pico SDK (copied from SDK)
  build/                    # The Pico image is built in this folder

scripts/
  init_datasets.sh          # Script to download, split and process datasets
  infer_on_pico.py          # Script to run on the host for Pico inference
  prep_images_for_pico.py   # Script to prepare images for Pico
  split_dataset.py          # Script to split any dataset into train, val, test
```

----


## Packages required for this project

Python Packages required for this project:
```
matplotlib
numpy
pandas
pyserial
seaborn
sklearn
tensorflow
```

## Setup Instructions

1. Setup a Conda Environment (this project used Miniforge)

2. Install Packages
  * ImageMagick is required to convert TIF files of the UC Merced dataset into JPG
    ```bash
    sudo apt install imagemagick  # Ubuntu
    ```

  * Install Python Packages:
    ```bash
    $ conda activate base
    (base) $ pip install pyserial numpy tensorflow matplotlib sklearn pandas seaborn
    ```

3. Setup the Pico build environment if you want to build the Pico image from source. Otherwise, the pre-built image can be copied from `artifacts/diode.uf2` to the Pico. **See instructions below.**

4. Run `scripts/init_datasets.sh` to download, split and process the datasets.

5. All the notebooks and Pico inferencing can be re-run at this point.


----


## Deploying an image to the Pico

1. Hold the `BOOTSEL` button on the Pico, and connect the Pico to the computer.
2. Navigate to the Pico drive that shows up (usually named `RPI-RP2`).
3. Copy the `artifacts/diode.uf2` file to the Pico drive.
4. The Pico will unmount automatically and flash the image. Once that happens, disconnect the Pico from the computer.
5. Activate the already-setup `conda` environment: `conda activate base`
5. Run: `python3 scripts/infer-on-pico.py`
6. Connect the Pico to the computer.


## Building an image for the Pico from source

You only need to follow these steps if you want to build the UF2 image yourself. Otherwise, just copy the pre-built UF2 image to the Pico using the instructions above.

1. Setup the Pico SDK following the instructions here for your platform:
https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf

3. Run the following:

```
$ cd pico
$ mkdir build
$ cd build
$ cmake ..
$ make
```

4. The image is built and ready to be copied. Follow instructions above to copy the `pico/build/diode.uf2` image and run inference.


----


# The Architecture of the Final Model

```
_________________________________________________________________
Layer (type)              Output Shape             Param #
=================================================================
InputLayer                [(None, 64, 64, 3)]      0
Rescaling                 (None, 64, 64, 3)        0
Conv2D                    (None, 62, 62, 32)       896
MaxPooling2D              (None, 31, 31, 32)       0
SeparableConv2D           (None, 29, 29, 64)       2400
MaxPooling2D              (None, 14, 14, 64)       0
SeparableConv2D           (None, 12, 12, 64)       4736
MaxPooling2D              (None, 6, 6, 64)         0
SeparableConv2D           (None, 4, 4, 128)        8896
GlobalAveragePooling2D    (None, 128)              0
Dropout                   (None, 128)              0
Dense                     (None, 10)               1290
=================================================================
Total params: 18,218
Trainable params: 18,218
Non-trainable params: 0
_________________________________________________________________
```

----

### Netron Diagram of the Final Model

<a href="artifacts/eurosat-model-architecture.png">
  <img src="artifacts/eurosat-model-architecture.png?raw=true" width="250" alt="Netron Diagram of eurosat.tflite" />
</a>

Created using [Netron](https://github.com/lutzroeder/Netron)

----
