# Custom-YoloV8-NPU
## Description
Running custom YOLO models on OrangePi RK3566/68/88 using the NPU can be challenging, especially when the model is trained on a custom dataset with a different number of classes than the original model.
This Repository aims to provide an easy step-by-step procedure for installing and running a custom YoloV8 model using the NPU of orangePi5. However, it can also be used for other hardware, such as Rock 5 and Radxa Zero 3.
## Step 1:
- Install OpenCV 64-bit using [this](https://qengineering.eu/install-opencv-on-raspberry-64-os.html).
- Install RKNN-toolkit2 : ``git clone https://github.com/airockchip/rknn-toolkit2.git``
- We only need a few files, so the rest of the toolkit can be safely removed.
-  ``cd ~/rknn-toolkit2-master/rknpu2/runtime/Linux/librknn_api/aarch64``\
  ``sudo cp ./librknnrt.so /usr/local/lib `` \
  `` cd ~/rknn-toolkit2-master/rknpu2/runtime/Linux/librknn_api/include `` \
`` sudo cp ./rknn_* /usr/local/include `` \
`` cd ~ `` \
`` sudo rm -rf ./rknn-toolkit2-master ``
## Step 2:
- Clone this repo: ``git clone ADD LINK``
- Compile code using CMake
    `` mkdir build ``\
  `` cd build ``\
`` cmake ``\
`` make -j$(nproc) ``

## Step 3:
- Convert .pt model to ONNX and then into .rknn.
- #### Important: Converting the original model directly to .onnx and then to .rknn may not work as expected. The resulting .rknn model might fail to produce any detection results during inference, and it may not throw any errors either.
  ### Step 3.1
  Export .pt model to .onnx 
  - `` git clone https://github.com/ultralytics/ultralytics.git``
  - `` cd ultralytics``
  - `` git checkout 0b0bc56675997fe66b13aa0d250b777c8a467e32``
  - `` # Adjust the model file path in "./ultralytics/cfg/default.yaml" (default is yolov8n.pt). If you trained your own model, please provide the corresponding path. ``
  - `` export PYTHONPATH=./ ``
  - `` # Upon completion, the ".onnx" model will be generated. If the original model is "yolov8n.pt," the generated model will be "yolov8n.onnx" ``\
Using [Netron.app](https://netron.app/), your converted model's output should look like the one on the right side of the figure below.
![Screenshot from 2025-04-14 16-00-37](https://github.com/user-attachments/assets/0ba27125-d173-4f7d-bb70-b68a4c2abb68)

### Step 3.2
Convert .onnx to .rknn
- ``git clone https://github.com/airockchip/rknn_model_zoo.git``
- `` cd rknn_model_zoo/examples/yolov8/python ``
- python convert.py <onnx_model> <TARGET_PLATFORM> <dtype(optional)> <output_rknn_path(optional)> 
- for example :  `` python convert.py ../model/yolov8n.onnx rk3588 ``(output model will be saved as ../model/yolov8.rknn)
- Visualize converted .rknn model using Netron app, and your model should look like the one on the right side.
![Screenshot from 2025-04-14 16-23-55 (1)](https://github.com/user-attachments/assets/ba930129-1c21-44ba-919a-31f867ccf56f)

### Step 4
- Change number of classes your modle has been trained on, by default it is set to 80.
-  include/rk_common.h
-  for example, if your yoloV8 model is trained on 10 classes, value value from 80 to 10 on line 12
-  #define OBJ_CLASS_NUM 80 --> #define OBJ_CLASS_NUM 10
### Done
