# Normalnet

Project for PSYCH 250.

This project implements a deep convolution neural network predicting surface normals from images. 
The structure was from Paper ["Physically-Based Rendering for Indoor Scene Understanding Using Convolutional Neural Networks"](<http://robots.princeton.edu/projects/2016/PBRS/>).
The implementation here used tensorflow and the dataset used was a synthetic dataset generated by NeuroAILab in Stanford, of which the codes will be published publicly later.
The examples of the dataset were shown in Ipython-notebook in this repo.

## Structure

Detailed descriptions of the structure could be found in the [paper](<http://robots.princeton.edu/projects/2016/PBRS/>).
In short, the structure includes two modules, encoder and decoder.
The network is a fully convolutional network ([FCN](<https://people.eecs.berkeley.edu/~jonlong/long_shelhamer_fcn.pdf>)) with skip-layers.
The encoder is applied first to the input image to get a low-resolution representation using convolution and pooling layers.
The decoder starts from the low-resolution representation and uses convolution and unpooling layers to get a high-resolution surface normal prediction. 

The structure of the encoder is the same as conv1 to conv5 of [VGG-16](<https://arxiv.org/pdf/1409.1556.pdf>) network and the decoder is symmetric to the encoder. 
To help the decoder get multi-scale feature maps, skip connections from corresponding layers of encoder to that of decoder are added to the network. 

The description of the structure in config files used by the training is in "normal\_pred/normals\_config\_fcnvgg16.cfg".

## Implementation

The implementation used [tensorflow](<https://github.com/tensorflow/tensorflow>) and [tfutils](<https://github.com/neuroailab/tfutils>). 
Tfutils is a repo designed for helping the use of tensorflow including keeping logs, saving models, resuming training, and doing validations. 
Tfutils uses mongodb as the backend database to hold all logs and models.
Therefore, to run the training and validation, one will also need to connect to the original database used in the original training.

## Dataset

## Training

## Performance

## Future steps
