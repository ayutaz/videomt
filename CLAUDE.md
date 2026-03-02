# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

VidEoMT (Video Encoder-only Mask Transformer) は、CVPR 2026で発表されたオンライン動画セグメンテーションのための軽量なエンコーダのみのViTモデル。DINOv2事前学習済みViTバックボーンを使用し、ViT-Lで最大160 FPSを達成。

## 主要コマンド

### 学習
```bash
python train_net_video.py --num-gpus <N> --config-file configs/<dataset>/<config>.yaml
```

### 評価
```bash
python train_net_video.py --num-gpus 1 --config-file <config>.yaml --eval-only MODEL.WEIGHTS <weight>.pth
```

### ベンチマーク (FPS/FLOPs)
```bash
python benchmark.py --task fps --config-file <config>.yaml --model-weights <weight>.pth
python benchmark.py --task flops --config-file <config>.yaml
```

### ビジュアライゼーション
```bash
cd visualization
python video_demo.py --config-file <config>.yaml --input <frames_dir> --output <output_dir>
```

### データセット固有の評価スクリプト
```bash
python utils/eval_vpq_vspw.py    # VIPSeg VPQ評価
python utils/eval_miou_vspw.py   # VSPW mIoU評価
python utils/eval_stq_vspw.py    # STQ評価
python utils/yt2021_eval.py      # YouTube-VIS 2021評価
```

## アーキテクチャ

### フレームワーク
Detectron2をベースフレームワークとして使用。設定管理、学習ループ、データローディングはすべてDetectron2の機構に依存。

### コアモデルの流れ
1. **`videomt/modeling/backbone/videomt.py`** (`VidEoMT_CLASS`): ViTエンコーダ + マスクヘッド + クラスヘッド + アップスケーリングブロック
2. **`videomt/videomt.py`** (`videomt`クラス): フルパイプライン。フォワードパス、学習、推論ロジックを統合。前フレームからのクエリ伝搬とフレーム非依存の学習済みクエリを結合
3. **`videomt/criterion_videomt.py`**: dice loss, CE loss, point-based supervision, ReID lossの損失関数群
4. **`videomt/modeling/matcher.py`**: インスタンスレベルの損失計算用ハンガリアンマッチング

### 設定システム
Detectron2の`CfgNode`による階層的YAML設定。`videomt/config.py`でデフォルト値を定義し、`configs/`以下のYAMLファイルでデータセット/モデル固有の設定をオーバーライド。

### データパイプライン
- **`videomt/data_video/datasets/`**: データセット登録 (YouTube-VIS, VIPSeg, VSPW, OVIS)
- **`videomt/data_video/dataset_mapper*.py`**: タスク別データマッパー（VIS/VPS/VSS）
- **`videomt/data_video/build.py`**: DataLoader構築
- **`videomt/data_video/augmentation.py`**: データ拡張

### 対応タスク・データセット
| タスク | データセット |
|--------|------------|
| Video Instance Segmentation (VIS) | YouTube-VIS 2019/2021/2022, OVIS |
| Video Panoptic Segmentation (VPS) | VIPSeg |
| Video Semantic Segmentation (VSS) | VSPW |

### 学習の特徴
- **Attention Mask Annealing**: `train_net_video.py`の`AttentionMaskAnnealingHook`で時間的注意を段階的に有効化
- **Layer-Wise Learning Rate Decay (LLRD)**: バックボーンとデコーダで異なる学習率
- **Two-Stage Warmup**: `two_stage_warmup_poly_schedule.py`による多項式スケジュール
- **W&B統合**: 実験ログ記録

## 依存関係

主要ライブラリ: PyTorch 2.7.0, Detectron2, timm, transformers, einops, pycocotools, wandb。Python 3.12.3, CUDA 12.6。詳細は`requirements.txt`参照。

## データセット配置

データセットのディレクトリ構成は`datasets/README.md`を参照。COCO形式からYTVIS形式への変換スクリプトは`datasets/utils/`に配置。
