## 評価
事前学習済みのVidEoMTモデルを評価するには、まずこの[リンク](../datasets/README.md)の手順に従ってデータセットを準備し、[こちら](dinov2.md)から学習済みの重みをダウンロードしてください。準備が完了したら、以下を実行します:


### YouTube-VIS 2019, YouTube-VIS 2021, OVIS<sup>*</sup>

```bash
python train_net_video.py \
  --num-gpus 1 \
  --config-file /path/to/config.yaml \
  --eval-only MODEL.WEIGHTS /path/to/weight.pth
  MODEL.BACKBONE.TEST.WINDOW_SIZE 1 \
  OUTPUT_DIR /path/to/output
```

🔧 `/path/to/config.yaml` を設定ファイルのパスに置き換えてください。
🔧 `/path/to/weight.pth` を評価するチェックポイントのパスに置き換えてください。
🔧 `/path/to/output` を出力フォルダのパスに置き換えてください。
🔧 `--num-gpus` の値を利用可能なGPUの数に変更してください。


### YouTube-VIS 2022

```bash
python train_net_video.py \
  --num-gpus 1 \
  --config-file /path/to/config.yaml \
  --eval-only MODEL.WEIGHTS /path/to/weight.pth
  MODEL.BACKBONE.TEST.WINDOW_SIZE 1 \
  OUTPUT_DIR /path/to/output
```
上記のコマンドで推論画像を生成した後、長い動画のAPを計算するには以下を実行します:

``` bash
python utils/yt2022_evaluate.py /path/to/dataset/ytvis_2022  /path/to/output/inference
```
🔧 `/path/to/dataset` をデータセットフォルダのパスに置き換えてください。
🔧 `/path/to/output` を出力フォルダのパスに置き換えてください。

### VIPSeg

```bash
python train_net_video.py \
  --num-gpus 1 \
  --config-file /path/to/config.yaml \
  --eval-only MODEL.WEIGHTS /path/to/weight.pth
  MODEL.BACKBONE.TEST.WINDOW_SIZE 1 \
  OUTPUT_DIR /path/to/output
```
上記のコマンドで推論画像を生成した後、VPQとSTQを計算するには以下を実行します:

``` bash
DATAROOT='/path/to/dataset/VIPSeg_720P/panomasksRGB'
IMGSAVEROOT='/path/to/output/inference'
GT_JSONFILE='/path/to/dataset/VIPSeg_720P/panoptic_gt_VIPSeg_val.json'

# ###VPQ
python utils/eval_vpq_vspw.py --submit_dir $IMGSAVEROOT --truth_dir $DATAROOT --pan_gt_json_file $GT_JSONFILE
# # ###STQ
python utils/eval_stq_vspw.py --submit_dir $IMGSAVEROOT --truth_dir $DATAROOT --pan_gt_json_file $GT_JSONFILE
```
🔧 `/path/to/dataset` をデータセットフォルダのパスに置き換えてください。
🔧 `/path/to/output` を出力フォルダのパスに置き換えてください。

### VSPW

```bash
python train_net_video.py \
  --num-gpus 1 \
  --config-file /path/to/config.yaml \
  --eval-only MODEL.WEIGHTS /path/to/weight.pth
  MODEL.BACKBONE.TEST.WINDOW_SIZE 1 \
  OUTPUT_DIR /path/to/output
```
上記のコマンドで推論画像を生成した後、mVC<sub>16</sub>とmIoUを計算するには以下を実行します:

``` bash
DATAROOT='/path/to/dataset/VSPW_480p'
IMGSAVEROOT='/path/to/output/inference'

python utils/eval_miou_vspw.py  $DATAROOT $IMGSAVEROOT
python utils/eval_vc_vspw.py $DATAROOT  $IMGSAVEROOT

```
🔧 `/path/to/dataset` をデータセットフォルダのパスに置き換えてください。
🔧 `/path/to/output` を出力フォルダのパスに置き換えてください。
