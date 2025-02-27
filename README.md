# Reduce, Reuse, Recycle: Compositional Generation with Energy-Based Diffusion Models and MCMC
## [<a href="https://energy-based-model.github.io/reduce-reuse-recycle/" target="_blank">Project Page</a>][<a href="https://colab.research.google.com/drive/1jvlzWMc6oo-TH1fYMl6hsOYfrcQj2rEs?usp=sharing" target="_blank">Colab</a>]

![ezgif com-video-to-gif (1)](https://user-images.githubusercontent.com/5572232/220694796-cc599abc-086f-4030-857a-59c87468fa79.gif)


We provide a framework for probabilistically composing and repurposing diffusion models across ifferent domains as described <a href="https://energy-based-model.github.io/reduce-reuse-recycle/" target="_blank">here</a>.

[//]: # (### Abstract)
> Since their introduction, diffusion models have quickly become the prevailing approach to generative modeling in many domains. They can be interpreted as learning the gradients of a time-varying sequence of log-probability density functions. This interpretation has motivated classifier-based and classifier-free guidance as methods for post-hoc control of diffusion models. In this work, we build upon these ideas using the score-based interpretation of diffusion models, and explore alternative ways to condition, modify, and reuse diffusion models for tasks involving compositional generation and guidance. In particular, we investigate why certain types of composition fail using current techniques and present a number of solutions. We conclude that the sampler (not the model) is responsible for this failure and propose new samplers, inspired by MCMC, which enable successful compositional generation. Further, we propose an energy-based parameterization of diffusion models which enables the use of new compositional operators and more sophisticated, Metropolis-corrected samplers. Intriguingly we find these samplers lead to notable improvements in compositional generation across a wide variety of problems such as classifier-guided ImageNet modeling and compositional text-to-image generation.

For more info see the [project webpage](https://energy-based-model.github.io/reduce-reuse-recycle/).

## Notebooks

We provide two separate notebooks to aid in implementing the results illustrated in the paper. 

* **notebooks/simple_distributions.ipynb** This notebook contains code for reproducing 2D distribution results in the paper. The notebook contains a stand-alone diffusion trainer for a EBM-parameterized model as well as code for different MCMC samplers (HMC, ULA, MALA, UHA) across different distribution combinations
* **notebooks/image_tapestry.ipynb** This notebook contains code illustrating how we may use MCMC sampling on existing text-to-image to construct image tapestries. Our image tapestry results are done using the Imagen 64x64 model where we can define diffusion models across different image sizes in the pixel space. Here, tapestires are defined in latent space (where linear interpolations are not well defined) and thus results are substantially poorer (but illustrate how MCMC sampling may be implemented in existing text-to-image models)

## Training Code

Most of the larger-scale experiments done in the paper were done using the computational infrastructure at DeepMind and cannot be released. Below is a PyTorch reimplementation written by  [Bharat Runwal](https://bharat-runwal.github.io/). 

## Energy Based Diffusion Model Training 

A PyTorch implementation of  **Reduce, Reuse, Recycle: Compositional Generation with Energy-Based Diffusion Models and MCMC**.

--------------------------------------------------------------------------------------------------------
* The codebase is built upon [GLIDE-Finetune](https://github.com/afiaka87/glide-finetune) and [Composable Diffusion](https://github.com/energy-based-model/Compositional-Visual-Generation-with-Composable-Diffusion-Models-PyTorch).



## Installation

Run following to create a conda environment using the requirements file, and activate it:
```
conda create --name compose_ebm --file requirements.txt
conda activate compose_ebm
```

## Training

To train the Energy Parameterized Classifier-Free Diffusion model you can run :

```
bash energy_train_ddp_64.sh
```

You can change the Energy score used for training here [Line](https://github.com/yilundu/reduce_reuse_recycle/blob/0231ae7a3ed397ba71e9c41c16508db29e4e251f/composable_diffusion/unet.py#L1398) , currently we are using a denoising autoencoder inspired energy function .

## Inference Sampling

```anneal_samplers.py``` containts the implementation of variaous samplers (HMC, UHMC, ULA, MALA) which can be used with reverse diffusion sampling.

**Note :** In the current setting we use MCMC sampling for t > 50 as we saw in the last 50 steps the score functions doesn't change the image much. You can change this behaviour at this line [Line](https://github.com/yilundu/reduce_reuse_recycle/blob/0231ae7a3ed397ba71e9c41c16508db29e4e251f/composable_diffusion/sampler_gd.py#L485) . 
Example of running MALA Sampler with trained chkpt path : 

```
python inf_sample.py --sampler MALA --ckpt_path "./checkpoints/Energy_object_chkpt_linear_100steps/model-ft-49x1874.pt" 

```

