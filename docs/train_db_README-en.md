## This is a ChatGPT-5 English adaptation of the original document by kohya-ss ([train_db_README-ja.md](https://github.com/kohya-ss/sd-scripts/blob/main/docs/train_db_README-ja.md))

# DreamBooth Guide

Please also see the [Common Documentation on Training](./train_README-en.md).

## Overview

DreamBooth is a technique for fine-tuning an image generation model to learn a specific subject, which can then be generated using a specific identifier. [Original paper](https://arxiv.org/abs/2208.12242).

Specifically, it allows you to train a Stable Diffusion model on a character, art style, etc., and then call it in prompts with a specific token such as `shs` to make it appear in the generated images.

This script is based on [Diffusers’ DreamBooth](https://github.com/huggingface/diffusers/tree/main/examples/dreambooth) but includes additional features (some of which have since been added to the original script):

Main features of this script:
- Memory optimization using 8-bit Adam optimizer and latent caching (same as [Shivam Shrirao’s version](https://github.com/ShivamShrirao/diffusers/tree/main/examples/dreambooth)).
- Memory optimization using xformers.
- Training at arbitrary resolutions (not limited to 512×512).
- Quality improvement via augmentation.
- Supports fine-tuning both Text Encoder + U-Net, not just DreamBooth.
- Reading and writing models in Stable Diffusion format.
- Aspect Ratio Bucketing.
- Stable Diffusion v2.0 support.

---

## Training Procedure

First, refer to this repository’s README to set up your environment.

### Preparing Data

See [Preparing Training Data](./train_README-en.md).

### Running Training

Run the script. The following command minimizes VRAM usage (run as a single line). Modify arguments as needed. Works with ~12GB VRAM:

```
accelerate launch --num_cpu_threads_per_process 1 train_db.py     --pretrained_model_name_or_path=<.ckpt or .safetensors or Diffusers model directory>     --dataset_config=<.toml file created during data preparation>     --output_dir=<folder to save trained model>     --output_name=<output model file name without extension>     --save_model_as=safetensors     --prior_loss_weight=1.0     --max_train_steps=1600     --learning_rate=1e-6     --optimizer_type="AdamW8bit"     --xformers     --mixed_precision="fp16"     --cache_latents     --gradient_checkpointing
```

**Key arguments:**
- `num_cpu_threads_per_process`: Usually `1` is optimal.
- `pretrained_model_name_or_path`: The base model to fine-tune (Stable Diffusion `.ckpt`/`.safetensors`, local Diffusers directory, or model ID like `"stabilityai/stable-diffusion-2"`).
- `output_dir` & `output_name`: Save location and name for the trained model.  
  `save_model_as=safetensors` saves in `.safetensors` format.
- `dataset_config`: `.toml` dataset config. Initially set batch size to `1` to save VRAM.
- `prior_loss_weight`: Weight for regularization image loss (default `1.0`).
- `max_train_steps`: Training steps (example uses `1600`).
- `learning_rate`: `1e-6` recommended for Stable Diffusion; Diffusers version defaults to `5e-6`.
- `mixed_precision`: Use `"fp16"` to save VRAM (or `"bf16"` for RTX 30-series+ if supported).
- `gradient_checkpointing`: Saves VRAM by checkpointing gradients.
- `optimizer_type="AdamW8bit"`: Lower VRAM Adam optimizer.
- `xformers`: Use xformers CrossAttention.  
  If not installed or incompatible, use `mem_eff_attn` for memory-efficient CrossAttention (slower).
- `cache_latents`: Cache VAE outputs to save VRAM (disable for augmentation).

If you have more VRAM, increase batch size in `.toml` (e.g., `4` for speed & potential accuracy gain).

---

### Commonly Used Options

Refer to the [Common Training Documentation](./train_README-en.md) if:
- Training on Stable Diffusion 2.x or derivatives
- Training a model assuming `clip_skip ≥ 2`
- Training with captions over 75 tokens

---

### Step Count in DreamBooth

This script uses half the training passes per step compared to the original Diffusers version, because target and regularization images are trained in separate batches.  
To match training length with the original scripts, **double the step count**.

---

### Batch Size in DreamBooth

Full-model training (like fine-tuning) consumes significantly more VRAM than LoRA training.

---

### Learning Rate

- Diffusers version: `5e-6`
- Stable Diffusion version: `1e-6` (used in above example)

---

### Older Dataset Specification Format

When specifying datasets via `--train_data_dir` and `--reg_data_dir`, also set resolution and batch size. Example:

```
accelerate launch --num_cpu_threads_per_process 1 train_db.py     --pretrained_model_name_or_path=<.ckpt or .safetensors or Diffusers model directory>     --train_data_dir=<training data directory>     --reg_data_dir=<regularization images directory>     --output_dir=<output model directory>     --output_name=<output model file name>     --prior_loss_weight=1.0     --resolution=512     --train_batch_size=1     --learning_rate=1e-6     --max_train_steps=1600     --use_8bit_adam     --xformers     --mixed_precision="bf16"     --cache_latents     --gradient_checkpointing
```

---

## Generating Images with the Trained Model

After training, the `.safetensors` file will be saved to your output directory.

- **v1.4/1.5 and derivatives:** Load directly into WebUI (place in `models/Stable-diffusion`).
- **v2.x models:** Require an additional `.yaml` config file.  
  - v2.x base → `v2-inference.yaml`  
  - v2.x 768/v → `v2-inference-v.yaml`  
  Place the `.yaml` in the same directory as the model and name it identically to the model file (before the extension).

YAML configs are available from [Stability AI’s SD2.0 repository](https://github.com/Stability-AI/stablediffusion/tree/main/configs/stable-diffusion).

---

## DreamBooth-Specific Options

See separate documentation for the full list.

### `--stop_text_encoder_training`
Stops training the Text Encoder after the specified step, continuing only U-Net training.  
This may improve quality in some cases by preventing Text Encoder overfitting.

### `--no_token_padding`
Disables tokenizer output padding (matches behavior of older Diffusers DreamBooth).

```
# Example: Bucketing + Augmentation
accelerate launch --num_cpu_threads_per_process 8 train_db.py     --pretrained_model_name_or_path=<.ckpt or .safetensors or Diffusers model directory>     --train_data_dir=<training data directory>     --reg_data_dir=<regularization images directory>     --output_dir=<output model directory>     --resolution=768,512     --train_batch_size=20     --learning_rate=5e-6     --max_train_steps=800     --use_8bit_adam     --xformers     --mixed_precision="bf16"     --save_every_n_epochs=1     --save_state     --save_precision="bf16"     --logging_dir=logs     --enable_bucket     --min_bucket_reso=384     --max_bucket_reso=1280     --color_aug     --flip_aug     --gradient_checkpointing     --seed 42
```

