# Normalnet

Project for PSYCH 250.

This project implements a deep convolution neural network predicting surface normals from images. 
The structure was from Paper ["Physically-Based Rendering for Indoor Scene Understanding Using Convolutional Neural Networks"](<http://robots.princeton.edu/projects/2016/PBRS/>).
The implementation here used tensorflow and the dataset used was a synthetic dataset generated by NeuroAILab in Stanford, of which the codes will be published publicly later.
The examples of the dataset as well as some results of the network were shown in [Ipython-notebook](<https://github.com/chengxuz/normalnet/blob/master/Prediction.ipynb>) in this repo.

## Structure

Detailed descriptions of the structure could be found in the [paper](<http://robots.princeton.edu/projects/2016/PBRS/>).
In short, the structure includes two modules, encoder and decoder.
The network is a fully convolutional network ([FCN](<https://people.eecs.berkeley.edu/~jonlong/long_shelhamer_fcn.pdf>)) with skip-layers.
The encoder is applied first to the input image to get a low-resolution representation using convolution and pooling layers.
The decoder starts from the low-resolution representation and uses convolution and unpooling layers to get a high-resolution surface normal prediction. 

The structure of the encoder is the same as conv1 to conv5 of [VGG-16](<https://arxiv.org/pdf/1409.1556.pdf>) network and the decoder is symmetric to the encoder. 
To help the decoder get multi-scale feature maps, skip connections from corresponding layers of encoder to that of decoder are added to the network. 

The description of the structure in config file used by the training is in "[normal\_pred/normals\_config\_fcnvgg16.cfg](<https://github.com/chengxuz/normalnet/blob/master/normal_pred/normals_config_fcnvgg16.cfg>)".

## Implementation

The implementation used [tensorflow](<https://github.com/tensorflow/tensorflow>) and [tfutils](<https://github.com/neuroailab/tfutils>). 
Tfutils is a repo designed for helping the use of tensorflow including keeping logs, saving models, resuming training, and doing validations. 
Tfutils uses mongodb as the backend database to hold all logs and models.
Therefore, to run the training and validation, one will also need to connect to the original database used in the original training.

## Dataset

The dataset used is generated using a realistic game engine, [Unity 5](<https://unity3d.com>).
A large number of objects are randomly sampled from a large 3D object dataset [ShapeNet](<https://www.shapenet.org/>).
The scales and orientations of the objects are randomly sampled and then the objects are placed into a large room.

The images and surface normals generated were used in this experiment. 
The example images can be seen in the [notebook](<https://github.com/chengxuz/normalnet/blob/master/Prediction.ipynb>).

## Training

Training scheme used is similar to that mentioned in the paper. 
[RMSprop](<https://www.coursera.org/learn/neural-networks/lecture/YQHki/rmsprop-divide-the-gradient-by-a-running-average-of-its-recent-magnitude>) is used for optimization and the loss used is the inverse of the dot product of normalzied surface normals and predicted normals.
The learning rate is set to be 0.001 at the beginning and reduced to 0.0005 after 300K batches, while the batch size is 32. 
The training is done on one NVIDIA Tesla P40. 
Random cropping of 224\*224 from 256\*256 is used for data augmentation. 
Before sending into the network, every image is subtracted by 128.

To start the training, one needs to run the following code under `normal_pred` directory: 
```
python train_normalnet.py --nport 27016 --pathconfig normals_config_fcnvgg16.cfg --expId rms_momopt --whichloss 1 --namefunc normal_vgg16_tfutils
``` 

But as the dataset is stored in the cluster of NeuroAILab, one will need access to the cluster to start the training.

## Code structure

All training related codes are stored in [`normal_pred`](<https://github.com/chengxuz/normalnet/tree/master/normal_pred>) directory.
As mentioned, [`train_normalnet.py`](<https://github.com/chengxuz/normalnet/blob/master/normal_pred/train_normalnet.py>) is the entrance to start the training.
Functions to build the network are stored in [`normal_encoder_asymmetric_with_bypass.py`](<https://github.com/chengxuz/normalnet/blob/master/normal_pred/normal_encoder_asymmetric_with_bypass.py>).
Related help functions are in [`model.py`](<https://github.com/chengxuz/normalnet/blob/master/normal_pred/model.py>).
The definition of the network is in [`normals_config_fcnvgg16.cfg`](<https://github.com/chengxuz/normalnet/blob/master/normal_pred/normals_config_fcnvgg16.cfg>).

## Performance

Some predictions are shown in the [notebook](<https://github.com/chengxuz/normalnet/blob/master/Prediction.ipynb>).
It seems that the model could get the coarse normals, while there are still some strange artifacts.
Some of them might be due to the artifacts of the dataset, such as the normals of the windows, the ground, and the walls are not correct in the ground truth.

## Future steps

To avoid the artifacts in the dataset, new synthetic datasets could be used.
Besides, depth prediction should be similar to normal prediction. Therefore, one could try to do these two tasks at the same time using one shared network.
Currently, the training of the network takes ver long time (1 week for 300k batches). New structure should be investigated to find more efficient networks.

## Acknowledgement

Thanks for my advisor Daniel Yamins for his advices and our neuroai-cluster for running the network training.
