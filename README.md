# SINet: A Scale-insensitive Convolutional Neural Network for Fast Vehicle Detection

by Xiaowei Hu, Xuemiao Xu, Yongjie Xiao, Hao Chen, Shengfeng He, Jing Qin, and Pheng-Ann Heng

This implementation is written by Xiaowei Hu at the Chinese University of Hong Kong.

***

## Citation
Submitted to IEEE Transactions on Intelligent Transportation Systems.


## Requirements

1. Without cuDNN during testing, the same running speed described in our paper can be acquired. cuDNN is required to avoid out-of-memory when training the models with VGG network.

2. We also need MATLAB scripts to run the auxiliary code, caffe MATLAB wrapper is required. Please build matcaffe before running the detection demo. 

3. This code has been tested on Ubuntu 14.04, CUDA 7.0, cuDNN v3 with an NVIDIA TITAN X GPU (the running speed described in paper) and Ubuntu 16.04. CUDA 8.0 with an NVIDIA TITAN X(Pascal) GPU.
  

## Installation
1. Clone the DSC repository, and we'll call the directory that you cloned DSC into `DSC`.

    ```shell
    git clone https://github.com/xw-hu/SINet.git
    ```

2. Build SINet (based on Caffe)
    
   Follow the Caffe installation instructions here: [http://caffe.berkeleyvision.org/installation.html](http://caffe.berkeleyvision.org/installation.html)   
   
   ```shell
   make all -j XX
   make matcaffe
   ```
   
 ## Traing on KITTI car dataset

1. Download the KITTI dataset by yourself.

2. Download the PAVNet pretrained model:  Go to the folder `./model/PVA/` and Run `sh download_PVANet_imagenet.sh`.

3. Go to the folder `SINet/data/kitti/window_files`, and replace `/your/KITTI/path/training/image_2/000000.png` with your KITTI path.
  
   Another way is to run the code `mscnn_kitti_car_window_file.m` to generate the `txt` files that include the path of KITTI images.

4. Run the `SINet/data/kitti/statistical_size.m` to calculate the parameters for `ROISplit` Layer in `trainval_2nd.prototxt`. 

5. (optional) Run `SINet/data/kitti/anchor_parameter.m` to calculate the anchors for `ImageGtData` layer. This is determined by K-means.

6. Go to the folder `SINet/examples/kitti_car/SINet-pva-576-2-branch` or other folders with other parameters.
7. Run `sh train_first_stage.sh` in the command window. (using around 1 hour on a single TITAN X)
   sh train_first_stage.sh
8. Use MATLAB to run the `weight_2nd_ini.m`
9. Run `sh train_second_stage.sh` in the command window. (using around 13.5 hour on a single TITAN X)

** If the training does not converge, try some other random seeds. You should obtain fair performance after a few tries. Due to the randomness, you are difficult to fully reproduce the same models, but the performance should be close.


## Testing on KITTI car dataset

1. Use MATLAB to run the `run_SINet_2_branch.m` in `SINet/examples/kitti_car`. It will generate the detection results in `SINet/examples/kitti_car/detections`. 
   ** In `run_SINet_detection.m`, let `show = 1`, the detection results will be shown and saved in `results/`. But the speed will be slower.

2. We can get the quantitive result (Average Precision) in three levels: "easy", "moderate" and "hard" (same as the KITTI benchmark).


## Training on other datasets.

1. Go to the folder `SINet/data/kitti/` and modify the code `mscnn_kitti_car_window_file.m` to generate the `txt` files for your datasets.

2. Modify the parameters and the pathes of input images in `SINet/examples/kitti_car/SINet-pva-576/trainval_1st.prototxt` and  `SINet/examples/kitti_car/SINet-pva-576/trainval_2nd.prototxt`.

3. Others are the same as before.

## Testing on other datasets.

1. Modify the `run_SINet_2_branch.m` in `SINet/examples/kitti_car`. It will generate the detection results in one `txt` file. 

2. Use other methods to calculate the Average Precision such as VOC2012 criterion or KITTI criterion for your results (in `SINet/examples/lsvh_result`, we use the implementation of VOC2011 evaluation code to calculate the mAP for our LSVH dataset).
