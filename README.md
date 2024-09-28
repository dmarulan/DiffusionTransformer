DiT Implementation in PyTorch
========

## DiT Tutorial Video
<img alt="DiT Tutorial" src="https://github.com/user-attachments/assets/2b0deffa-0181-4676-b79f-fec9b12d8326"
   width="400">

## Sample Output for DiT on CelebHQ
Trained for 200 epochs

<img src="https://github.com/user-attachments/assets/3fea7f05-3834-4a60-bab4-d4d10b1777e8" width="750">

___  

This repository implements DiT in PyTorch for diffusion models. It provides code for the following:
* Training and inference of VAE on CelebHQ
* Training and Inference of DiT using trained VAE on CelebHQ
* Configurable code for training all models from DIT-S to DIT-XL

This is very similar to official DiT implementation except the following changes.
* Since training is on celebhq its unconditional generation as of now (but can be easily modified to class conditional or text conditional as well)
* Variance is fixed during training and not learned (like original DDPM)
* No EMA 
* Ability to save latents of VAE for faster training


## Setup
* Create a new conda environment with python 3.10 then run below commands
* ```git clone https://github.com/explainingai-code/DiT-PyTorch.git```
* ```cd DiT-PyTorch```
* ```pip install -r requirements.txt```
* Download lpips weights from https://github.com/richzhang/PerceptualSimilarity/blob/master/lpips/weights/v0.1/vgg.pth and put it in `models/weights/v0.1/vgg.pth`
___  

## Data Preparation

### CelebHQ 
For setting up on CelebHQ, simply download the images from the official repo of CelebMASK HQ [here](https://github.com/switchablenorms/CelebAMask-HQ?tab=readme-ov-file).

Ensure directory structure is the following
```
ControlNet-PyTorch
    -> data
        -> CelebAMask-HQ
            -> CelebA-HQ-img
                -> *.jpg

```
---
## Configuration
 Allows you to play with different components of DiT and autoencoder
* ```config/celebhq.yaml``` - Configuration used for celebhq dataset

<ins>Important configuration parameters</ins>
autoencoder_acc_steps : For accumulating gradients if image size is too large and a large batch size cant be used.
save_latents : Enable this to save the latents , during inference of autoencoder. That way DiT training will be faster

___  
## Training
The repo provides training and inference for CelebHQ (Unconditional DiT) 

For working on your own dataset:
* Create your own config and have the path in config point to images (look at `celebhq.yaml` for guidance)
* Create your own dataset class which will just collect all the filenames and return the image or latent in its getitem method. Look at `celeb_dataset.py` for guidance 

Once the config and dataset is setup:
* First train the auto encoder on your dataset using [this section](#training-autoencoder-for-dit)
* For training and inference of Unconditional DiT follow [this section](#training-unconditional-dit)

## Training AutoEncoder for DiT
* For training autoencoder on celebhq follow along the instructions provided in ControlNet repo [here](https://github.com/explainingai-code/ControlNet-PyTorch/tree/main?tab=readme-ov-file#training-autoencoder-for-ldm)
* Make sure to have `save_latent`

## Training Unconditional DiT
Train the autoencoder first and setup dataset accordingly.


## Output 
Outputs will be saved according to the configuration present in yaml files.

For every run a folder of ```task_name``` key in config will be created

During training of autoencoder the following output will be saved 
* Latest Autoencoder and discriminator checkpoint in ```task_name``` directory
* Sample reconstructions in ```task_name/vae_autoencoder_samples```

During inference of autoencoder the following output will be saved
* Reconstructions for random images in  ```task_name```
* Latents will be save in ```task_name/vae_latent_dir_name``` if mentioned in config

During training and inference of unconditional DiT following output will be saved:
* During training we will save the latest checkpoint in ```task_name``` directory
* During sampling, unconditional sampled image grid for all timesteps in ```task_name/samples/*.png``` . The final decoded generated image will be `x0_0.png`. Images from `x0_999.png` to `x0_1.png` will be latent image predictions of denoising process from T=999 to T=1. Generated Image is at T=0




