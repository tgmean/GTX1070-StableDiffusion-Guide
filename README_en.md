# Complete Guide to Running Stable Diffusion on GTX 1070

> 🇯🇵 [日本語版はこちら](README.md)

## Overview

This guide explains how to run Stable Diffusion WebUI Forge Classic on a GTX 1070 (Pascal architecture, Compute Capability 6.1).

As of 2026, the latest Forge neo requires PyTorch with CUDA 13.0 support, which does not support the GTX 1070. This guide shows how to manually downgrade PyTorch to make it work.

## Tested Environment

| Component | Details |
|-----------|---------|
| GPU | NVIDIA GeForce GTX 1070 8GB |
| CPU | Core i5-13400 |
| RAM | 32GB |
| OS | Windows 11 |
| Compute Capability | 6.1 |

## Why GTX 1070 Doesn't Work Out of the Box

Modern Forge neo/Classic automatically installs PyTorch with CUDA 13.0, which requires Compute Capability **7.5 or higher**. GTX 1070 has CC 6.1, causing these errors:

```
SystemError: Please update your GPU driver to support cu130
# or
CUDA error: no kernel image is available for execution on the device
```

The solution is to manually replace PyTorch with an older CUDA 11.8-compatible version.

## Steps

### 1. Prerequisites

- Update NVIDIA drivers to the latest version ([here](https://www.nvidia.com/en-us/drivers/))
- Pause OneDrive sync

### 2. Install Stability Matrix

Download from [Stability Matrix](https://github.com/LykosAI/StabilityMatrix/releases).

- Install path: `C:\StabilityMatrix-win-x64` (no spaces or special characters)
- Enable Portable mode

### 3. Install Forge Classic

- Select "Stable Diffusion WebUI Forge - Classic" from Add Package
- **Select Python 3.11.9 in Advanced Options** (important!)

### 4. Download a Model

Download a model from [Civitai](https://civitai.com) and place it in:

```
C:\StabilityMatrix-win-x64\Data\Packages\Stable Diffusion WebUI Forge - Classic\models\Stable-diffusion\
```

Recommended model: **WAI-illustrious-SDXL Ver17**

### 5. Replace PyTorch (Critical Step)

Open this folder in Explorer:

```
C:\StabilityMatrix-win-x64\Data\Packages\Stable Diffusion WebUI Forge - Classic\venv\Scripts\
```

Type `cmd` in the address bar and press Enter, then run these commands in order:

```bash
pip install torch==2.1.0+cu118 torchvision==0.16.0+cu118 --index-url https://download.pytorch.org/whl/cu118

pip install numpy==1.26.4 --force-reinstall

pip install transformers==4.35.0 --force-reinstall --no-deps

pip install tokenizers==0.14.0 --force-reinstall --no-deps
```

Or place `setup.bat` in the venv\Scripts folder and double-click to run everything at once.

### 6. Set Launch Arguments

Open Forge Classic Settings and add to Extra Launch Arguments:

```
--skip-python-version-check --skip-torch-cuda-test --skip-install
```

### 7. Click Launch

When the browser opens, you're ready to generate images!

## Using LoRA

Place LoRA files in:

```
C:\StabilityMatrix-win-x64\Data\Packages\Stable Diffusion WebUI Forge - Classic\models\Lora\
```

Example prompt:

```
1girl, blonde hair, blue eyes, smile
<lora:your_lora_name:0.8>
```

## setup.bat Contents

Create `setup.bat` with the following content and place it in the venv\Scripts folder:

```batch
@echo off
echo ===================================
echo GTX1070 PyTorch Setup Tool
echo ===================================
echo.
echo Installing PyTorch CUDA 11.8...
pip install torch==2.1.0+cu118 torchvision==0.16.0+cu118 --index-url https://download.pytorch.org/whl/cu118
echo.
echo Downgrading numpy...
pip install numpy==1.26.4 --force-reinstall
echo.
echo Downgrading transformers...
pip install transformers==4.35.0 --force-reinstall --no-deps
echo.
echo Downgrading tokenizers...
pip install tokenizers==0.14.0 --force-reinstall --no-deps
echo.
echo ===================================
echo Done! Please launch from Stability Matrix.
echo ===================================
pause
```

## Important Notes

- Always set `--skip-install` to prevent automatic package overwriting on launch
- No need to run pip commands again after initial setup
- Generation speed: approximately 1 minute per image (512×512)
- LoRA training is not recommended due to VRAM limitations

## Future Plans

Upgrading to RTX 3060 12GB or higher allows comfortable use with Forge neo.

## License

MIT License - Feel free to use and share!

## Contributing

PRs welcome! If you find a better combination of package versions, please share.

## If This Guide Helped You

Please give it a ⭐️ — it really helps!
