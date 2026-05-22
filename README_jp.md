# GTX1070 で Stable Diffusion を動かす完全ガイド

> 🇺🇸 [English version is here](README_en.md)

## 概要

GTX 1070（Pascal世代・Compute Capability 6.1）でStable Diffusion WebUI Forge Classicを動作させるための手順書です。

2026年現在、最新のForge neoが要求するPyTorch（CUDA 13.0対応）はGTX 1070をサポートしていません。本ガイドではPyTorchを手動でダウングレードすることで動作させる方法を解説します。

## 動作確認環境

| パーツ | 内容 |
|--------|------|
| GPU | NVIDIA GeForce GTX 1070 8GB |
| CPU | Core i5-13400 |
| メモリ | 32GB |
| OS | Windows 11 |
| Compute Capability | 6.1 |

## なぜGTX 1070で動かないのか

現在のForge neo/ClassicはPyTorch CUDA 13.0対応版を自動インストールします。しかしこのバージョンはCompute Capability **7.5以上**のGPUしかサポートしていません。GTX 1070はCC 6.1のためエラーになります。

```
SystemError: Please update your GPU driver to support cu130
# または
CUDA error: no kernel image is available for execution on the device
```

解決策はPyTorchをCUDA 11.8対応の古いバージョンに手動で差し替えることです。

## 手順

### 1. 事前準備

- NVIDIAドライバーを最新版に更新する（[こちら](https://www.nvidia.com/ja-jp/drivers/)）
- OneDriveを一時停止する

### 2. Stability Matrixをインストール

[Stability Matrix](https://github.com/LykosAI/StabilityMatrix/releases)からダウンロード。

- インストール先：`C:\StabilityMatrix-win-x64`（日本語・スペースなし）
- Portableモードにチェックを入れる

### 3. Forge Classicをインストール

- Add PackageからStable Diffusion WebUI Forge - Classicを選択
- **高度なオプションでPython 3.11.9を選択**（重要！）

### 4. モデルをダウンロード

[Civitai](https://civitai.com)でモデルをダウンロードして以下へ配置：

```
C:\StabilityMatrix-win-x64\Data\Packages\Stable Diffusion WebUI Forge - Classic\models\Stable-diffusion\
```

おすすめモデル：**WAI-illustrious-SDXL Ver17**

### 5. PyTorchを差し替える（最重要）

エクスプローラーで以下を開く：

```
C:\StabilityMatrix-win-x64\Data\Packages\Stable Diffusion WebUI Forge - Classic\venv\Scripts\
```

アドレスバーに`cmd`と入力してEnterを押し、以下を順番に実行：

```bash
pip install torch==2.1.0+cu118 torchvision==0.16.0+cu118 --index-url https://download.pytorch.org/whl/cu118

pip install numpy==1.26.4 --force-reinstall

pip install transformers==4.35.0 --force-reinstall --no-deps

pip install tokenizers==0.14.0 --force-reinstall --no-deps
```

または`setup.bat`をvenv\Scriptsフォルダに置いてダブルクリックで一発実行。

### 6. 起動引数を設定

Forge ClassicのSettingsボタンを開きExtra Launch Argumentsに入力：

```
--skip-python-version-check --skip-torch-cuda-test --skip-install
```

### 7. Launchボタンを押す

ブラウザが開いたら生成スタート！

## LoRAの使い方

LoRAファイルを以下へ配置：

```
C:\StabilityMatrix-win-x64\Data\Packages\Stable Diffusion WebUI Forge - Classic\models\Lora\
```

プロンプト例：

```
1girl, blonde hair, blue eyes, smile
<lora:your_lora_name:0.8>
```

## setup.bat の内容

以下の内容で`setup.bat`を作成し、venv\Scriptsフォルダに置いてダブルクリックで実行：

```batch
@echo off
echo ===================================
echo GTX1070 PyTorch セットアップツール
echo ===================================
echo.
echo PyTorch CUDA 11.8 をインストール中...
pip install torch==2.1.0+cu118 torchvision==0.16.0+cu118 --index-url https://download.pytorch.org/whl/cu118
echo.
echo numpy をダウングレード中...
pip install numpy==1.26.4 --force-reinstall
echo.
echo transformers をダウングレード中...
pip install transformers==4.35.0 --force-reinstall --no-deps
echo.
echo tokenizers をダウングレード中...
pip install tokenizers==0.14.0 --force-reinstall --no-deps
echo.
echo ===================================
echo 完了！Stability Matrix からLaunchしてください
echo ===================================
pause
```

## 注意事項

- `--skip-install`を必ず設定する（起動時の自動上書きを防ぐため）
- 初回セットアップ後はコマンドプロンプトでの差し替えは不要
- 生成速度は1枚あたり約1分（512×512）
- LoRA学習はVRAM不足のため非推奨

## 今後の展望

RTX 3060 12GB以上のGPUに乗り換えれば、Forge neoで快適に動作します。

## ライセンス

MIT License - ご自由にお使いください！

## コントリビュート

より良いパッケージバージョンの組み合わせを発見した方はPRをお願いします！

## このガイドが役に立ったら

⭐️ をいただけると励みになります！
