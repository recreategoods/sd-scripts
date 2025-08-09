## This is a ChatGPT-5 English adaptation of the original document by kohya-ss ([train_db_README-ja.md](https://github.com/kohya-ss/sd-scripts/blob/main/docs/train_db_README-ja.md))

This is the DreamBooth guide.

Please also see the [Common Documentation on Training](./train_README-en.md).

# Overview

DreamBooth is a technique for fine-tuning an image generation model to learn a specific subject, which can then be generated using a specific identifier. [Original paper](https://arxiv.org/abs/2208.12242).

Specifically, it allows you to train a Stable Diffusion model to learn a character, art style, etc., and then call it with a specific word such as `shs` to make it appear in generated images.

This script is based on [Diffusers’ DreamBooth](https://github.com/huggingface/diffusers/tree/main/examples/dreambooth), but with additional features (some of which were later added to the original script).

Main features of this script:

- Memory optimization using the 8-bit Adam optimizer and latent caching (same as [Shivam Shrirao’s version](https://github.com/ShivamShrirao/diffusers/tree/main/examples/dreambooth)).
- Memory optimization using xformers.
- Training at arbitrary sizes, not limited to 512×512.
- Quality improvement via augmentation.
- Supports fine-tuning of both Text Encoder + U-Net, not just DreamBooth.
- Reading and writing models in Stable Diffusion format.
- Aspect Ratio Bucketing.
- Stable Diffusion v2.0 support.

# Training Procedure

Before starting, please refer to the README of this repository to set up your environment.

## Preparing Data

See [Preparing Training Data](./train_README-en.md).

## Running Training

Run the script. The following command minimizes VRAM usage (should be entered as a single line). Modify the arguments as needed. This should run on around 12GB of VRAM.

```
accelerate launch --num_cpu_threads_per_process 1 train_db.py \
    --pretrained_model_name_or_path=<.ckpt or .safetensors or Diffusers model directory> \
    --dataset_config=<.toml file created during data preparation> \
    --output_dir=<folder to save the trained model> \
    --output_name=<file name for the trained model (without extension)> \
    --save_model_as=safetensors \
    --prior_loss_weight=1.0 \
    --max_train_steps=1600 \
    --learning_rate=1e-6 \
    --optimizer_type="AdamW8bit" \
    --xformers \
    --mixed_precision="fp16" \
    --cache_latents \
    --gradient_checkpointing
```
`num_cpu_threads_per_process`: Usually, setting this to `1` works best.

`pretrained_model_name_or_path`: Specifies the base model for fine-tuning.  
Acceptable inputs include a Stable Diffusion checkpoint file (`.ckpt` or `.safetensors`),  
a local Diffusers model directory, or a Diffusers model ID (e.g., `"stabilityai/stable-diffusion-2"`).

`output_dir`: The folder where the trained model will be saved.  
`output_name`: The filename for the trained model, without the extension.  
`save_model_as`: Specifies the save format; here, `safetensors` is used.

`dataset_config`: Path to the `.toml` dataset configuration file.  
Initially, set the batch size in this file to `1` to minimize VRAM usage.

`prior_loss_weight`: Weight for the loss from regularization images. Usually `1.0`.

`max_train_steps`: Number of training steps. Here, `1600` is specified.  
`learning_rate`: Learning rate for training. Here, `1e-6` is specified.

`mixed_precision="fp16"`: Enables mixed-precision training to save VRAM  
(`bf16` can be used on RTX 30-series or newer if supported).  
Match this with your `accelerate` configuration.  
Also specify `gradient_checkpointing` to save memory.

`optimizer_type="AdamW8bit"`: Uses the low-memory 8-bit AdamW optimizer.

`xformers`: Enables xformers CrossAttention.  
If xformers is not installed or an error occurs (depending on environment,  
such as when using `mixed_precision="no"`), you can instead specify  
`mem_eff_attn` to use a memory-efficient CrossAttention implementation (slower).

`cache_latents`: Caches VAE outputs to save VRAM.

If you have more VRAM available, edit the `.toml` file to increase batch size  
(e.g., `4` for faster training and potentially better accuracy).  
Also, disabling `cache_latents` allows augmentation to be applied.

---

### Commonly Used Options

Refer to [Common Documentation on Training](./train_README-en.md) for:
- Training Stable Diffusion 2.x or derivative models
- Training models assuming `clip_skip ≥ 2`
- Training with captions longer than 75 tokens

---

### Step Count in DreamBooth

This script processes half as many training passes per step compared to the  
original Diffusers version, because target and regularization images are trained  
in separate batches.  

To match the training length of the original scripts, **double the number of steps**.

(Strictly speaking, the order of data may differ slightly because target and regularization images are combined and then shuffled,  
but this should not significantly affect training.)

---

### Batch Size in DreamBooth

Full-model training (like fine-tuning) uses more VRAM than LoRA training.

---

### Learning Rate

In the Diffusers version, the learning rate is `5e-6`.  
In the Stable Diffusion version, it is `1e-6` — as shown in the sample above.

---

### Older Dataset Specification Format

When using the older dataset specification format, you specify resolution and batch size via options.  
Example: 
 
```
accelerate launch --num_cpu_threads_per_process 1 train_db.py \
    --pretrained_model_name_or_path=<.ckpt or .safetensors or Diffusers model directory> \
    --train_data_dir=<training data directory> \
    --reg_data_dir=<regularization images directory> \
    --output_dir=<directory to save the trained model> \
    --output_name=<file name for the trained model (without extension)> \
    --prior_loss_weight=1.0 \
    --resolution=512 \
    --train_batch_size=1 \
    --learning_rate=1e-6 \
    --max_train_steps=1600 \
    --use_8bit_adam \
    --xformers \
    --mixed_precision="bf16" \
    --cache_latents \
    --gradient_checkpointing
```

## Generating Images with the Trained Model

After training is complete, a `.safetensors` file with the specified name will be output to the designated folder.

For v1.4/1.5 and other derivative models, you can use this model for inference in Automatic1111’s WebUI. Place it in the `models\Stable-diffusion` folder.

When generating images in WebUI using a v2.x model, an additional `.yaml` file describing the model specifications is required.  
For v2.x base models, use `v2-inference.yaml`; for 768/v models, use `v2-inference-v.yaml`. Place the `.yaml` file in the same folder as the model, and make sure its name (before the extension) matches the model’s file name.

![image](https://user-images.githubusercontent.com/52813779/210776915-061d79c3-6582-42c2-8884-8b91d2f07313.png)

These YAML files are available in [Stability AI’s SD 2.0 repository](https://github.com/Stability-AI/stablediffusion/tree/main/configs/stable-diffusion).

---

# Other Main Options Specific to DreamBooth

For a complete list of options, see the separate documentation.

## Stop Training the Text Encoder Partway — `--stop_text_encoder_training`

When you specify a value for the `stop_text_encoder_training` option,  
the Text Encoder will stop training after that step number, and only the U-Net will continue training.  
This may improve accuracy in some cases.

(It is assumed that, in some situations, the Text Encoder may overfit early, and this option can prevent that,  
though the exact effects are unknown.)

## Disable Token Padding — `--no_token_padding`

When the `no_token_padding` option is specified, the tokenizer’s output will not be padded  
(same behavior as the older Diffusers DreamBooth).

<!-- 
Example when using bucketing (described later) together with augmentation (described later):
```
accelerate launch –num_cpu_threads_per_process 8 train_db.py 
	–pretrained_model_name_or_path=<.ckpt or .safetensors or Diffusers model directory> 
	–train_data_dir= 
	–reg_data_dir= 
	–output_dir= 
	–resolution=768,512 
	–train_batch_size=20 –learning_rate=5e-6 –max_train_steps=800 
	–use_8bit_adam –xformers –mixed_precision=“bf16” 
	–save_every_n_epochs=1 –save_state –save_precision=“bf16” 
	–logging_dir=logs 
	–enable_bucket –min_bucket_reso=384 –max_bucket_reso=1280 
	–color_aug –flip_aug –gradient_checkpointing –seed 42
```
-->     
