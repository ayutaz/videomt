## VidEoMT: あなたのViTは実はビデオセグメンテーションモデルでもある
**CVPR 2026** · **[📄 論文](https://arxiv.org/pdf/2602.17807v1)**

**[Narges Norouzi](https://scholar.google.com/citations?user=q7sm490AAAAJ)<sup>1</sup>, [Idil Esen Zulfikar](https://scholar.google.com/citations?user=89vcmSoAAAAJ&hl=en)<sup>2,\*</sup>, [Niccolò Cavagnero](https://scholar.google.com/citations?user=Pr4XHRAAAAAJ)<sup>1,\*</sup>, [Tommie Kerssies](https://tommiekerssies.com)<sup>1</sup>, [Bastian Leibe](https://scholar.google.com/citations?user=ZcULDB0AAAAJ)<sup>2</sup>, [Gijs Dubbelman](https://scholar.google.nl/citations?user=wy57br8AAAAJ)<sup>1</sup>, [Daan de Geus](https://ddegeus.github.io)<sup>1</sup>**

¹ Eindhoven University of Technology,
² RWTH Aachen University,
\* *同等の貢献*

## 概要

![VidEoMT 概要](./docs/static/images/teaser_arch.jpeg)

本研究では、**Video Encoder-only Mask Transformer (VidEoMT)** を提案します。これは、プレーンな Vision Transformer (ViT) をベースとした、オンラインビデオセグメンテーション向けの軽量なエンコーダのみのモデルです。専用のトラッキングモジュールや重いタスク固有のヘッドに依存することなく、ViTエンコーダ内で空間的および時間的推論を行います。

VidEoMTは、前のフレームのクエリを再利用し、学習済みのフレーム非依存なコンパクトなクエリセットと融合することで、時間方向に情報を伝播します。この設計により、既存の手法と同等の精度を達成しながら、5倍～10倍の高速化を実現し、ViT-Lバックボーンで最大160 FPSに到達します。



## インストール

Condaがインストールされていない場合は、Minicondaをインストールしてシェルを再起動してください:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

次に、環境を作成・有効化し、依存パッケージをインストールします:

```bash
conda create -n videomt python==3.12.3
conda activate videomt
pip install torch==2.7.0 torchvision==0.22.0 --index-url https://download.pytorch.org/whl/cu126
python -m pip install --no-build-isolation 'git+https://github.com/facebookresearch/detectron2.git'
pip install git+https://github.com/cocodataset/panopticapi.git
python3 -m pip install -r requirements.txt
```

実験のログと可視化には [Weights & Biases](https://wandb.ai/) (wandb) を使用しています。wandbを有効にするには、アカウントにログインしてください:

```bash
wandb login
```

## データの準備

[データセットのダウンロードと準備手順はこちら。](datasets/README.md)


## 使い方

### 評価

事前学習済みのVidEoMTモデルを評価するには、まず[こちらの手順](datasets/README.md)に従ってデータセットを準備し、[こちら](model_zoo/dinov2.md)から学習済みの重みをダウンロードしてください。準備ができたら、以下を実行します:

```bash
python train_net_video.py \
  --num-gpus 1 \
  --config-file /path/to/config.yaml \
  --eval-only MODEL.WEIGHTS /path/to/weight.pth \
  MODEL.MODEL.BACKBONE.TEST.WINDOW_SIZE 1 \
  OUTPUT_DIR /path/to/output
```

🔧 `/path/to/config.yaml` を設定ファイルのパスに置き換えてください。
🔧 `/path/to/weight.pth` を評価するチェックポイントのパスに置き換えてください。
🔧 `/path/to/output` を出力フォルダのパスに置き換えてください。
🔧 `--num-gpus` の値を利用可能なGPU数に変更してください。

各データセットでの評価実行の詳細な手順は、[評価手順](model_zoo/evaluation.md)を参照してください。

### ベンチマーク

FPSとGFLOPsを計算するには、以下を実行します:

```bash
python benchmark.py \
  --task fps \
  --config-file    /path/to/config.yaml \
  --model-weights  /path/to/weight.pth  \
  --warmup-iters 100

export TIMM_FUSED_ATTN=0
python benchmark.py \
  --task flops \
  --config-file    /path/to/config.yaml \
  --model-weights  /path/to/weight.pth
```

🔧 `/path/to/config.yaml` を設定ファイルのパスに置き換えてください。
🔧 `/path/to/weight.pth` を評価するチェックポイントのパスに置き換えてください。

## デモ

以下に可視化の例を示します。


<img src="./docs/videos/1f17cd7c_5.gif" width="400"/> <img src="./docs/videos/d4f4cf55_5.gif" width="400" />
<img src="./docs/videos/1010_kI0mOZirPGs_5.gif" width="400" /> <img src="./docs/videos/1975_1qyIMfzlXnY_5.gif" width="400" />
<!-- <img src="./docs/videos/35d5e5149d_5.gif" width="400" />  -->



追加の可視化サンプルを生成するには、[可視化手順](model_zoo/visualization.md)のコードを使用してください。


## 今後の機能追加予定

```
- [x] 推論コード
- [x] FLOPsおよびFPS計算コード
- [x] 可視化コード
- [ ] 学習コード
- [ ] DINOv3 Model Zooおよびコード

```

## Model Zoo

DINOv2ベースおよびDINOv3ベースのVidEoMTモデルの事前学習済み重みを提供しています。

- **[DINOv2 モデル](model_zoo/dinov2.md)** - 論文発表時の結果と事前学習済み重み。
- **[DINOv3 モデル](model_zoo/dinov3.md)** - DINOv3ベースのモデルと事前学習済み重み。

## 引用
本研究が役に立った場合は、以下のBibTeXエントリを使用して引用してください:

```BibTeX
@inproceedings{norouzi2026videomt,
  author     = {Norouzi, Narges and Zulfikar, Idil and Cavagnero, Niccol\`{o} and Kerssies, Tommie and Leibe, Bastian and Dubbelman, Gijs and {de Geus}, Daan},
  title      = {{VidEoMT: Your ViT is Secretly Also a Video Segmentation Model}},
  booktitle  = {Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
  year       = {2026},
}
```

## 謝辞

本プロジェクトは、以下のライブラリおよびリポジトリのコードを基に構築されています:
- [EoMT](https://github.com/tue-mps/eomt) (MIT License)
- [Hugging Face Transformers](https://github.com/huggingface/transformers) (Apache-2.0 License)
- [PyTorch Image Models (timm)](https://github.com/huggingface/pytorch-image-models) (Apache-2.0 License)
- [CAVIS](https://github.com/Seung-Hun-Lee/CAVIS) (MIT License)
- [Mask2Former](https://github.com/facebookresearch/Mask2Former) (Apache-2.0 License)
- [Detectron2](https://github.com/facebookresearch/detectron2) (Apache-2.0 License)
