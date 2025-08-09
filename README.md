## This is a ChatGPT-4 English adaptation of the original document by kohya-ss ([README.md](https://github.com/kohya-ss/sd-scripts/blob/main/README.md))

I wanted to bring the japanese tutorial to the english speaking community, merging knowledge globally to pursue the development of the stable diffusion models to another level.

Happy Fine-Tuning!

##
Latest update: 2025-03-21 (Version 0.9.1)

This repository contains training, generation, and utility scripts for Stable Diffusion.

The [__Change History__](#change-history) has been moved to the bottom of the page.

For the [Japanese version of the README](https://github.com/kohya-ss/sd-scripts/blob/main/README-ja.md).

The development version is in the `dev` branch. Please check the dev branch for the latest changes.

FLUX.1 and SD3/SD3.5 support is done in the `sd3` branch. If you want to train them, please use the sd3 branch.

For easier usage, including GUI and PowerShell scripts, please visit [the repository maintained by bmaltais](https://github.com/bmaltais/kohya_ss). Special thanks to @bmaltais!

This repository includes scripts for the following:

* DreamBooth training, including U-Net and Text Encoder
* Fine-tuning (native training), including U-Net and Text Encoder
* LoRA training
* Textual Inversion training
* Image generation
* Model conversion (supports 1.x and 2.x, Stable Diffusion ckpt/safetensors, and Diffusers)

__The Stable Diffusion web UI now appears to support LoRA training with ``sd-scripts``.__ Thank you for the great work!

## About requirements.txt

The file does not contain requirements for PyTorch. Because the version of PyTorch depends on the environment, it is not included in the file. Please install PyTorch first according to the environment. See installation instructions below.

The scripts are tested with Pytorch 2.1.2. PyTorch 2.2 or later will work. Please install the appropriate version of PyTorch and xformers.

## Links to usage documentation

Most of the documents are written in Japanese.

* [Training guide - common](./docs/train_README-en.md): data preparation, options, etc.
  * [Chinese version](https://github.com/kohya-ss/sd-scripts/blob/main/docs/train_README-zh.md)
* [SDXL training](./docs/train_SDXL-en.md)
* [Dataset config](./docs/config_README-en.md)
* [DreamBooth training guide](./docs/train_db_README-en.md)
* [Step-by-step fine-tuning guide](./docs/fine_tune_README-en.md)
* [LoRA training](./docs/train_network_README-en.md)
* [Textual Inversion training](./docs/train_ti_README-en.md)
* [Image generation](./docs/gen_img_README-en.md)
* [Model conversion](https://note.com/kohya_ss/n/n374f316fe4ad) on note.com

## Windows Required Dependencies

Python 3.10.6 and Git:

- Python 3.10.6: https://www.python.org/ftp/python/3.10.6/python-3.10.6-amd64.exe
- Git: https://git-scm.com/download/win

Python 3.10.x, 3.11.x, and 3.12.x will work but not tested.

Grant unrestricted script access to PowerShell so that venv can work:

- Open an administrator PowerShell window
- Type `Set-ExecutionPolicy Unrestricted` and answer A
- Close the admin PowerShell window

## Windows Installation

Open a regular PowerShell terminal and enter the following commands:

```powershell
git clone https://github.com/kohya-ss/sd-scripts.git
cd sd-scripts

python -m venv venv
.\venv\Scripts\activate

pip install torch==2.1.2 torchvision==0.16.2 --index-url https://download.pytorch.org/whl/cu118
pip install --upgrade -r requirements.txt
pip install xformers==0.0.23.post1 --index-url https://download.pytorch.org/whl/cu118

accelerate config
```

If `python -m venv` shows only `python`, change `python` to `py`.

Note: Now `bitsandbytes==0.44.0`, `prodigyopt==1.0` and `lion-pytorch==0.0.6` are included in the requirements.txt. If you'd like to use the another version, please install it manually.

This installation is for CUDA 11.8. If you use a different version of CUDA, please install the appropriate version of PyTorch and xformers. For example, if you use CUDA 12, please install `pip install torch==2.1.2 torchvision==0.16.2 --index-url https://download.pytorch.org/whl/cu121` and `pip install xformers==0.0.23.post1 --index-url https://download.pytorch.org/whl/cu121`.

If you use PyTorch 2.2 or later, please change `torch==2.1.2` and `torchvision==0.16.2` and `xformers==0.0.23.post1` to the appropriate version.

<!-- 
cp .\bitsandbytes_windows\*.dll .\venv\Lib\site-packages\bitsandbytes\
cp .\bitsandbytes_windows\cextension.py .\venv\Lib\site-packages\bitsandbytes\cextension.py
cp .\bitsandbytes_windows\main.py .\venv\Lib\site-packages\bitsandbytes\cuda_setup\main.py
-->
Answers to accelerate config:

```txt
- This machine
- No distributed training
- NO
- NO
- NO
- all
- fp16
```

If you'd like to use bf16, please answer `bf16` to the last question.

Note: Some user reports ``ValueError: fp16 mixed precision requires a GPU`` is occurred in training. In this case, answer `0` for the 6th question: 
``What GPU(s) (by id) should be used for training on this machine as a comma-separated list? [all]:`` 

(Only the single GPU with id `0` will be used.)

## Upgrade

When a new release is available, you can upgrade your repository using the following command:

```powershell
cd sd-scripts
git pull
.\venv\Scripts\activate
pip install --use-pep517 --upgrade -r requirements.txt
```

Once the commands have been executed successfully, you should be ready to use the new version.

### Upgrade PyTorch

If you want to upgrade PyTorch, you can upgrade it with `pip install` command in [Windows Installation](#windows-installation) section. `xformers` is also required to be upgraded when PyTorch is upgraded.

## Credits

The implementation for LoRA is based on [cloneofsimo's repo](https://github.com/cloneofsimo/lora). Thank you for the excellent work!

The LoRA expansion to Conv2d 3x3 was initially released by cloneofsimo, and its effectiveness was demonstrated at [LoCon](https://github.com/KohakuBlueleaf/LoCon) by KohakuBlueleaf. Thank you so much, KohakuBlueleaf!

## License

The majority of the scripts are licensed under ASL 2.0 (including codes from Diffusers, cloneofsimo's, and LoCon). However, portions of the project are available under separate license terms:

[Memory Efficient Attention Pytorch](https://github.com/lucidrains/memory-efficient-attention-pytorch): MIT

[bitsandbytes](https://github.com/TimDettmers/bitsandbytes): MIT

[BLIP](https://github.com/salesforce/BLIP): BSD-3-Clause

## Change History
### Mar 21, 2025 /  2025-03-21 Version 0.9.1

- Fixed a bug where some of the LoRA modules for the CLIP Text Encoder were not being trained. Thank you Nekotekina for PR [#1964](https://github.com/kohya-ss/sd-scripts/pull/1964)
  - The LoRA modules for the CLIP Text Encoder now total 264 modules, the same as before. In the previous version, only 88 modules were trained.

### Jan 17, 2025 /  2025-01-17 Version 0.9.0

- __Important__ The dependent libraries have been updated. Please see [Upgrade](#upgrade) and update the libraries.
  - bitsandbytes, transformers, accelerate, and huggingface_hub have been updated. 
  - If you encounter any issues, please report them.

- The dev branch has been merged into main. Documentation is delayed — apologies for this. I will gradually improve it.
- The state just before the merge has been released as Version 0.8.8, so please use that version if you encounter any issues.
- The following changes are included.

#### Changes

- Fixed a bug where the loss weight was incorrect when `--debiased_estimation_loss` was specified with `--v_parameterization`. PR [#1715](https://github.com/kohya-ss/sd-scripts/pull/1715) Thanks to catboxanon! See [the PR](https://github.com/kohya-ss/sd-scripts/pull/1715) for details.
  - Removed the warning when `--v_parameterization` is specified in SDXL and SD1.5. PR [#1717](https://github.com/kohya-ss/sd-scripts/pull/1717)

- Fixed a bug where the `min_bucket_reso` / `max_bucket_reso` in the dataset configuration did not create the correct resolution bucket if it was not divisible by `bucket_reso_steps`. These values are now warned and automatically rounded to a divisible value. Thanks to Maru-mee for raising the issue. Related PR [#1632](https://github.com/kohya-ss/sd-scripts/pull/1632)

- Updated `bitsandbytes` to 0.44.0. Now you can use `AdEMAMix8bit` and `PagedAdEMAMix8bit` in the training script. PR [#1640](https://github.com/kohya-ss/sd-scripts/pull/1640) Thanks to sdbds!
  - There is no abbreviation, so please specify the full path like `--optimizer_type bitsandbytes.optim.AdEMAMix8bit` (not bnb but bitsandbytes).

- Fixed a bug in the cache of latents. When `flip_aug`, `alpha_mask`, and `random_crop` differ between multiple subsets in the dataset configuration file (.toml), the last subset’s values were incorrectly applied to all.

- Fixed an issue where timesteps in the batch were the same when using Huber loss. PR [#1628](https://github.com/kohya-ss/sd-scripts/pull/1628) Thanks to recris!

- Improvements in OFT (Orthogonal Finetuning) Implementation
  1. **Optimization of Calculation Order:**
      - Changed the calculation order in the forward method from (Wx)R to W(xR).
      - This improves computational efficiency and processing speed.
  2. **Correction of Bias Application:**
      - In the previous implementation, R was incorrectly applied to the bias.
      - The new implementation now correctly handles bias by using F.conv2d and F.linear.
  3. **Efficiency Enhancement in Matrix Operations:**
      - Introduced einsum in both the forward and merge_to methods.
      - This optimizes matrix operations, further improving speed.
  4. **Proper Handling of Data Types:**
      - Calculations now use torch.float32, with results converted back to the original type.
      - This maintains precision while ensuring compatibility with the original model.
  5. **Unified Processing for Conv2d and Linear Layers:**
     - Implemented a consistent method for applying OFT to both layer types.
  - These changes make the OFT implementation more efficient and accurate, potentially improving model performance and training stability.

  - **Additional Information**
    * Recommended α value for OFT constraint: We recommend α between 1e-4 and 1e-2. This differs from the original "(α\*out_dim\*out_dim)" formula; our implementation uses "(α\*out_dim)", hence slightly higher values than the original’s 1e-5 recommendation are advised.
    * **Performance Improvement:** Training speed improved by ~30%.
    * **Inference Environment:** Compatible with Stable Diffusion web UI (SD1/2 and SDXL).

- The INVERSE_SQRT, COSINE_WITH_MIN_LR, and WARMUP_STABLE_DECAY learning rate schedules are now available in transformers. See PR [#1393](https://github.com/kohya-ss/sd-scripts/pull/1393) for details. Thanks to sdbds!
  - See the [transformers documentation](https://huggingface.co/docs/transformers/v4.44.2/en/main_classes/optimizer_schedules#schedules) for details.
  - `--lr_warmup_steps` and `--lr_decay_steps` can now be ratios of total training steps. Example: `--lr_warmup_steps=0.1` or `10%`.

- Image enlargement now uses Pillow’s resize with LANCZOS interpolation instead of OpenCV2’s Lanczos4. This may slightly improve quality. PR [#1426](https://github.com/kohya-ss/sd-scripts/pull/1426) Thanks to sdbds!

- Sample image generation during training now works on non-CUDA devices. PR [#1433](https://github.com/kohya-ss/sd-scripts/pull/1433) Thanks to millie-v!

- `--v_parameterization` is now available in `sdxl_train.py`. Results are unpredictable — use with caution. PR [#1505](https://github.com/kohya-ss/sd-scripts/pull/1505) Thanks to liesened!

- **Fused optimizer** is now available for SDXL training. PR [#1259](https://github.com/kohya-ss/sd-scripts/pull/1259) Thanks to 2kpr!
  - Integrates the optimizer’s backward pass with step, greatly reducing memory usage.
  - Specify `--fused_backward_pass` in `sdxl_train.py`. Currently supports only AdaFactor. No gradient accumulation.
  - `no` mixed precision uses less memory than `fp16` or `bf16`.
  - Training possible with ~17GB VRAM at batch size 1, fp32. With `--full_bf16`, memory use is further reduced (with lower accuracy).
  - PyTorch 2.1+ is required for `Tensor.register_post_accumulate_grad_hook(hook)`.
  - Mechanism: Normally all gradients are stored before step; this fuses backward/step per parameter, applying gradients immediately.

- **Optimizer groups** added for SDXL training. PR [#1319](https://github.com/kohya-ss/sd-scripts/pull/1319)
  - Reduces memory usage by grouping parameters into separate optimizers.
  - Specify `--fused_optimizer_groups <n>` in `sdxl_train.py` (4–10 recommended).
  - Cannot be used with `--fused_backward_pass`. PyTorch 2.1+ required.

- **LoRA+** support. PR [#1233](https://github.com/kohya-ss/sd-scripts/pull/1233) Thanks to rockerBOO!
  - Increases LoRA-B learning rate to improve training speed.
  - Example: `--network_args "loraplus_lr_ratio=16"`
  - Separate ratios possible for U-Net and Text Encoder.

- Transparency (alpha channel) can now be used as a mask in loss calculation. PR [#1223](https://github.com/kohya-ss/sd-scripts/pull/1223) Thanks to u-haru!

- SDXL LoRA training now supports block-wise learning rates and dim (rank). PR [#1331](https://github.com/kohya-ss/sd-scripts/pull/1331)

- Negative learning rates are now allowed in SDXL training. PR [#1277](https://github.com/kohya-ss/sd-scripts/pull/1277) Thanks to Cauldrath!

- Training settings can now be logged to wandb or TensorBoard. PR [#1285](https://github.com/kohya-ss/sd-scripts/pull/1285)

- Fixed broken SD1.5/2.x `train_controlnet.py`. PR [#1284](https://github.com/kohya-ss/sd-scripts/pull/1284) Thanks to sdbds!

- Data loading order is now restored when resuming training in `train_network.py` and `sdxl_train_network.py`. PR [#1353](https://github.com/kohya-ss/sd-scripts/pull/1353) [#1359](https://github.com/kohya-ss/sd-scripts/pull/1359)

- Added `--disable_mmap_load_safetensors` option for SDXL to speed up model loading in WSL. PR [#1266](https://github.com/kohya-ss/sd-scripts/pull/1266)

- Cached latents file errors now display the filename. PR [#1278](https://github.com/kohya-ss/sd-scripts/pull/1278)

- Fixed `tag_images_by_wd14_tagger.py` crash with `--max_dataloader_n_workers` when Onnx not used. PR [#1291](https://github.com/kohya-ss/sd-scripts/pull/1291)

- Fixed `caption_separator` subset bug in dataset .toml. PR [#1312](https://github.com/kohya-ss/sd-scripts/pull/1312) [#1313](https://github.com/kohya-ss/sd-scripts/pull/1313)

- Fixed potential bug in ControlNet-LLLite training. PR [#1322](https://github.com/kohya-ss/sd-scripts/pull/1322)

- Fixed various DeepSpeed issues. Related [#1247](https://github.com/kohya-ss/sd-scripts/pull/1247)

- Added `--f` option to `gen_imgs.py` to set output filename; LoRA weights with Diffusers keys now supported.

### Oct 27, 2024 / 2024-10-27:

- `svd_merge_lora.py` VRAM usage reduced. Main memory usage increases (32GB sufficient).
- Will be in the next release.

### Oct 26, 2024 / 2024-10-26:

- Fixed hash calculation bug in `svd_merge_lora.py`, `sdxl_merge_lora.py`, `resize_lora.py` when `save_precision` differs from calculation precision. PR [#1722](https://github.com/kohya-ss/sd-scripts/pull/1722) Thanks to JujoHotaru!
- Will be in the next release.

### Sep 13, 2024 / 2024-09-13:

- `sdxl_merge_lora.py` now supports OFT. PR [#1580](https://github.com/kohya-ss/sd-scripts/pull/1580) Thanks to Maru-mee.
- `svd_merge_lora.py` now supports LBW. PR [#1575](https://github.com/kohya-ss/sd-scripts/pull/1575) Thanks to terracottahaniwa.
- `sdxl_merge_lora.py` also supports LBW.
- See [LoRA Block Weight](https://github.com/hako-mikan/sd-webui-lora-block-weight) for details.
- Will be in the next release.

### Jun 23, 2024 / 2024-06-23:

- Fixed `cache_latents.py` and `cache_text_encoder_outputs.py` not working. Will be in the next release.

### Apr 7, 2024 / 2024-04-07: v0.8.7

- Default `huber_schedule` in Scheduled Huber Loss changed from `exponential` to `snr` for better results.

### Apr 7, 2024 / 2024-04-07: v0.8.6

### May 11, 2023

- Added an option `--dim_from_weights` to `train_network.py` to automatically determine the dim(rank) from the weight file. [PR #491](https://github.com/kohya-ss/sd-scripts/pull/491) Thanks to AI-Casanova!
  - It is useful in combination with `resize_lora.py`. Please see the PR for details.
- Fixed a bug where the noise resolution was incorrect with Multires noise. [PR #489](https://github.com/kohya-ss/sd-scripts/pull/489) Thanks to sdbds!
  - Please see the PR for details.
- The image generation scripts can now use img2img and highres fix simultaneously.
- Fixed a bug where the hint image of ControlNet was incorrectly BGR instead of RGB in the image generation scripts.
- Added a feature to the image generation scripts to use the memory-efficient VAE.
  - If you specify a number with the `--vae_slices` option, the memory-efficient VAE will be used. The maximum output size will be larger, but it will be slower. Please specify a value of about `16` or `32`.
  - The implementation of the VAE is in `library/slicing_vae.py`.

### May 7, 2023

- The documentation has been moved to the `docs` folder. If you have links, please update them accordingly.
- Removed `gradio` from `requirements.txt`.
- DAdaptAdaGrad, DAdaptAdan, and DAdaptSGD are now supported by DAdaptation. [PR#455](https://github.com/kohya-ss/sd-scripts/pull/455) Thanks to sdbds!
  - DAdaptation needs to be installed. Also, depending on the optimizer, DAdaptation may need to be updated. Please update with `pip install --upgrade dadaptation`.
- Added support for pre-calculation of LoRA weights in image generation scripts. Specify `--network_pre_calc`.
  - The prompt option `--am` is available. Also, it is disabled when Regional LoRA is used.
- Added Adaptive noise scale to each training script. Specify a number with `--adaptive_noise_scale` to enable it.
  - __This is an experimental option. It may be removed or changed in the future.__
  - This is an original implementation that automatically adjusts the value of the noise offset according to the absolute value of the mean of each channel of the latents. It is expected that appropriate noise offsets will be set for bright and dark images, respectively.
  - Specify it together with `--noise_offset`.
  - The actual value of the noise offset is calculated as `noise_offset + abs(mean(latents, dim=(2,3))) * adaptive_noise_scale`. Since the latent is close to a normal distribution, it may be a good idea to specify a value of about 1/10 to the same as the noise offset.
  - Negative values can also be specified, in which case the noise offset will be clipped to 0 or more.
- Other minor fixes.

#### Highlights

- The dependent libraries are updated. Please see [Upgrade](#upgrade) and update the libraries.
  - Especially `imagesize` is newly added, so if you cannot update the libraries immediately, please install with `pip install imagesize==1.4.1` separately.
  - `bitsandbytes==0.43.0`, `prodigyopt==1.0`, `lion-pytorch==0.0.6` are included in the requirements.txt.
    - `bitsandbytes` no longer requires complex procedures as it now officially supports Windows.  
  - Also, the PyTorch version is updated to 2.1.2 (PyTorch does not need to be updated immediately). In the upgrade procedure, PyTorch is not updated, so please manually install or update torch, torchvision, xformers if necessary (see [Upgrade PyTorch](#upgrade-pytorch)).
- When logging to wandb is enabled, the entire command line is exposed. Therefore, it is recommended to write wandb API key and HuggingFace token in the configuration file (`.toml`). Thanks to bghira for raising the issue.
  - A warning is displayed at the start of training if such information is included in the command line.
  - Also, if there is an absolute path, the path may be exposed, so it is recommended to specify a relative path or write it in the configuration file. In such cases, an INFO log is displayed.
  - See [#1123](https://github.com/kohya-ss/sd-scripts/pull/1123) and PR [#1240](https://github.com/kohya-ss/sd-scripts/pull/1240) for details.
- Colab seems to stop with log output. Try specifying `--console_log_simple` option in the training script to disable rich logging.
- Other improvements include the addition of masked loss, scheduled Huber Loss, DeepSpeed support, dataset settings improvements, and image tagging improvements. See below for details.

#### Training scripts

- `train_network.py` and `sdxl_train_network.py` are modified to record some dataset settings in the metadata of the trained model (`caption_prefix`, `caption_suffix`, `keep_tokens_separator`, `secondary_separator`, `enable_wildcard`).
- Fixed a bug that U-Net and Text Encoders are included in the state in `train_network.py` and `sdxl_train_network.py`. The saving and loading of the state are faster, the file size is smaller, and the memory usage when loading is reduced.
- DeepSpeed is supported. PR [#1101](https://github.com/kohya-ss/sd-scripts/pull/1101)  and [#1139](https://github.com/kohya-ss/sd-scripts/pull/1139) Thanks to BootsofLagrangian! See PR [#1101](https://github.com/kohya-ss/sd-scripts/pull/1101) for details.
- The masked loss is supported in each training script. PR [#1207](https://github.com/kohya-ss/sd-scripts/pull/1207) See [Masked loss](#about-masked-loss) for details.
- Scheduled Huber Loss has been introduced to each training scripts. PR [#1228](https://github.com/kohya-ss/sd-scripts/pull/1228/) Thanks to kabachuha for the PR and cheald, drhead, and others for the discussion! See the PR and [Scheduled Huber Loss](#about-scheduled-huber-loss) for details.
- The options `--noise_offset_random_strength` and `--ip_noise_gamma_random_strength` are added to each training script. These options can be used to vary the noise offset and ip noise gamma in the range of 0 to the specified value. PR [#1177](https://github.com/kohya-ss/sd-scripts/pull/1177) Thanks to KohakuBlueleaf!
- The options `--save_state_on_train_end` are added to each training script. PR [#1168](https://github.com/kohya-ss/sd-scripts/pull/1168) Thanks to gesen2egee!
- The options `--sample_every_n_epochs` and `--sample_every_n_steps` in each training script now display a warning and ignore them when a number less than or equal to `0` is specified. Thanks to S-Del for raising the issue.

#### Dataset settings

- The [English version of the dataset settings documentation](./docs/config_README-en.md) is added. PR [#1175](https://github.com/kohya-ss/sd-scripts/pull/1175) Thanks to darkstorm2150!
- The `.toml` file for the dataset config is now read in UTF-8 encoding. PR [#1167](https://github.com/kohya-ss/sd-scripts/pull/1167) Thanks to Horizon1704!
- Fixed a bug that the last subset settings are applied to all images when multiple subsets of regularization images are specified in the dataset settings. The settings for each subset are correctly applied to each image. PR [#1205](https://github.com/kohya-ss/sd-scripts/pull/1205) Thanks to feffy380!
- Some features are added to the dataset subset settings.
  - `secondary_separator` is added to specify the tag separator that is not the target of shuffling or dropping. 
    - Specify `secondary_separator=";;;"`. When you specify `secondary_separator`, the part is not shuffled or dropped. 
  - `enable_wildcard` is added. When set to `true`, the wildcard notation `{aaa|bbb|ccc}` can be used. The multi-line caption is also enabled.
  - `keep_tokens_separator` is updated to be used twice in the caption. When you specify `keep_tokens_separator="|||"`, the part divided by the second `|||` is not shuffled or dropped and remains at the end.
  - The existing features `caption_prefix` and `caption_suffix` can be used together. `caption_prefix` and `caption_suffix` are processed first, and then `enable_wildcard`, `keep_tokens_separator`, shuffling and dropping, and `secondary_separator` are processed in order.
  - See [Dataset config](./docs/config_README-en.md) for details.
- The dataset with DreamBooth method supports caching image information (size, caption). PR [#1178](https://github.com/kohya-ss/sd-scripts/pull/1178) and [#1206](https://github.com/kohya-ss/sd-scripts/pull/1206) Thanks to KohakuBlueleaf! See [DreamBooth method specific options](./docs/config_README-en.md#dreambooth-specific-options) for details.

#### Image tagging

- The support for v3 repositories is added to `tag_image_by_wd14_tagger.py` (`--onnx` option only). PR [#1192](https://github.com/kohya-ss/sd-scripts/pull/1192) Thanks to sdbds!
  - Onnx may need to be updated. Onnx is not installed by default, so please install or update it with `pip install onnx==1.15.0 onnxruntime-gpu==1.17.1` etc. Please also check the comments in `requirements.txt`.
- The model is now saved in the subdirectory as `--repo_id` in `tag_image_by_wd14_tagger.py` . This caches multiple repo_id models. Please delete unnecessary files under `--model_dir`.
- Some options are added to `tag_image_by_wd14_tagger.py`.
  - Some are added in PR [#1216](https://github.com/kohya-ss/sd-scripts/pull/1216) Thanks to Disty0!
  - Output rating tags `--use_rating_tags` and `--use_rating_tags_as_last_tag`
  - Output character tags first `--character_tags_first`
  - Expand character tags and series `--character_tag_expand`
  - Specify tags to output first `--always_first_tags`
  - Replace tags `--tag_replacement`
  - See [Tagging documentation](./docs/wd14_tagger_README-en.md) for details.
- Fixed an error when specifying `--beam_search` and a value of 2 or more for `--num_beams` in `make_captions.py`.

#### About Masked loss

The masked loss is supported in each training script. To enable the masked loss, specify the `--masked_loss` option.

The feature is not fully tested, so there may be bugs. If you find any issues, please open an Issue.

ControlNet dataset is used to specify the mask. The mask images should be the RGB images. The pixel value 255 in R channel is treated as the mask (the loss is calculated only for the pixels with the mask), and 0 is treated as the non-mask. The pixel values 0-255 are converted to 0-1 (i.e., the pixel value 128 is treated as the half weight of the loss). See details for the dataset specification in the [LLLite documentation](./docs/train_lllite_README.md#preparing-the-dataset).

#### About Scheduled Huber Loss

Scheduled Huber Loss has been introduced to each training scripts. This is a method to improve robustness against outliers or anomalies (data corruption) in the training data.

With the traditional MSE (L2) loss function, the impact of outliers could be significant, potentially leading to a degradation in the quality of generated images. On the other hand, while the Huber loss function can suppress the influence of outliers, it tends to compromise the reproduction of fine details in images.

To address this, the proposed method employs a clever application of the Huber loss function. By scheduling the use of Huber loss in the early stages of training (when noise is high) and MSE in the later stages, it strikes a balance between outlier robustness and fine detail reproduction.

Experimental results have confirmed that this method achieves higher accuracy on data containing outliers compared to pure Huber loss or MSE. The increase in computational cost is minimal.

The newly added arguments loss_type, huber_schedule, and huber_c allow for the selection of the loss function type (Huber, smooth L1, MSE), scheduling method (exponential, constant, SNR), and Huber's parameter. This enables optimization based on the characteristics of the dataset.

See PR [#1228](https://github.com/kohya-ss/sd-scripts/pull/1228/) for details.

- `loss_type`: Specify the loss function type. Choose `huber` for Huber loss, `smooth_l1` for smooth L1 loss, and `l2` for MSE loss. The default is `l2`, which is the same as before.
- `huber_schedule`: Specify the scheduling method. Choose `exponential`, `constant`, or `snr`. The default is `snr`.
- `huber_c`: Specify the Huber's parameter. The default is `0.1`.

Please read [Releases](https://github.com/kohya-ss/sd-scripts/releases) for recent updates.

## Additional Information

### Naming of LoRA

The LoRA supported by `train_network.py` has been named to avoid confusion. The documentation has been updated. The following are the names of LoRA types in this repository.

1. __LoRA-LierLa__ : (LoRA for __Li__ n __e__ a __r__  __La__ yers)

    LoRA for Linear layers and Conv2d layers with 1x1 kernel

2. __LoRA-C3Lier__ : (LoRA for __C__ olutional layers with __3__ x3 Kernel and  __Li__ n __e__ a __r__ layers)

    In addition to 1., LoRA for Conv2d layers with 3x3 kernel 
    
LoRA-LierLa is the default LoRA type for `train_network.py` (without `conv_dim` network arg). 
<!-- 
LoRA-LierLa can be used with [our extension](https://github.com/kohya-ss/sd-webui-additional-networks) for AUTOMATIC1111's Web UI, or with the built-in LoRA feature of the Web UI.

To use LoRA-C3Lier with Web UI, please use our extension. 
-->

### Sample image generation during training
  A prompt file might look like this, for example

```
# prompt 1
masterpiece, best quality, (1girl), in white shirts, upper body, looking at viewer, simple background --n low quality, worst quality, bad anatomy,bad composition, poor, low effort --w 768 --h 768 --d 1 --l 7.5 --s 28

# prompt 2
masterpiece, best quality, 1boy, in business suit, standing at street, looking back --n (low quality, worst quality), bad anatomy,bad composition, poor, low effort --w 576 --h 832 --d 2 --l 5.5 --s 40
```

  Lines beginning with `#` are comments. You can specify options for the generated image with options like `--n` after the prompt. The following can be used.

  * `--n` Negative prompt up to the next option.
  * `--w` Specifies the width of the generated image.
  * `--h` Specifies the height of the generated image.
  * `--d` Specifies the seed of the generated image.
  * `--l` Specifies the CFG scale of the generated image.
  * `--s` Specifies the number of steps in the generation.

  The prompt weighting such as `( )` and `[ ]` are working.
