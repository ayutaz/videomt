# データセットの準備

データセットは、データにアクセスするための [DatasetCatalog](https://detectron2.readthedocs.io/modules/data.html#detectron2.data.DatasetCatalog)、
またはメタデータ（クラス名など）にアクセスするための [MetadataCatalog](https://detectron2.readthedocs.io/modules/data.html#detectron2.data.MetadataCatalog) を使用して利用できます。
このドキュメントでは、上記の API で使用できるように組み込みデータセットをセットアップする方法を説明します。
[カスタムデータセットの使用](https://detectron2.readthedocs.io/tutorials/datasets.html) では、`DatasetCatalog` と `MetadataCatalog` の使い方、
および新しいデータセットの追加方法についてより詳しく解説しています。

VidEoMT はいくつかのデータセットを組み込みでサポートしています。
データセットは環境変数 `DETECTRON2_DATASETS` で指定されたディレクトリに存在することを前提としています。
このディレクトリ配下で、detectron2 は必要に応じて以下の構造でデータセットを探します。
```
$DETECTRON2_DATASETS/
  ytvis_2019/
  ytvis_2021/
  ovis/
  VIPSeg/
  VSPW_480p/
```

組み込みデータセットの場所は `export DETECTRON2_DATASETS=/path/to/datasets` で設定できます。
未設定の場合、デフォルトは現在の作業ディレクトリからの相対パス `./datasets` になります。



## [YouTube-VIS 2019](https://competitions.codalab.org/competitions/20128) の想定データセット構造:

```
ytvis_2019/
  {train,valid,test}.json
  {train,valid,test}/
    Annotations/
    JPEGImages/
```

## [YouTube-VIS 2021](https://competitions.codalab.org/competitions/28988) の想定データセット構造:

```
ytvis_2021/
  {train,valid,test}.json
  {train,valid,test}/
    JPEGImages/
    instances.json

```

## [YouTube-VIS 2022](https://codalab.lisn.upsaclay.fr/competitions/3410#participate-get_data) の想定データセット構造:

```
ytvis_2022/
  {train,valid,test}.json
  gt_{short,long}.json
  {train,valid,test}/
    JPEGImages/
    instances.json

```

## [OVIS](http://songbai.site/ovis/) の想定データセット構造:

```
ovis/
  annotations/
    annotations_{train,valid,test}.json
  {train,valid,test}/
```

## [VIPSeg](https://github.com/VIPSeg-Dataset/VIPSeg-Dataset) の想定データセット構造:

VIPSeg データセットをダウンロードした後、公式スクリプト（`/datasets/utils/vipseg_change2_720p.py`）に従って処理する必要があります。時間を節約するために、処理済みの VIPSeg データセットを [百度网盘](https://pan.baidu.com/s/1SMausnr6pVDJXTGISeFMuw)（パスワード: `dvis`）から直接ダウンロードすることもできます。
```
VIPSeg/
  VIPSeg_720P/
    images/
    panomasksRGB/
    panoptic_gt_VIPSeg_{train,val,test}.json
```

## [VSPW](https://github.com/VSPW-dataset/VSPW-dataset-download) の想定データセット構造:
```
VSPW_480p/
  data/
    video_1/
      mask/
      origin/
  train.txt
  val.txt
  test.txt
  data.txt
  abel_num_dic_final.json


```

## 独自データセットの登録:

- VIS/VPS/VSS データセットの場合は、YTVIS/VIPSeg/VSPW 形式に変換してください。画像インスタンスデータセットの場合は、COCO 形式に変換してください。
- `/videomt/data_video/datasets/{builtin,vps,vss}.py` で登録してください。

## COCO から YTVIS / OVIS 形式への変換

VidEoMT は、COCO アノテーションを COCO カテゴリでフィルタリングすることで **YTVIS 2019**、**YTVIS 2021**、**OVIS** と互換性のあるサブセットに変換するヘルパースクリプト（DVIS++ からコピー）を提供しています。

このスクリプトは、COCO アノテーションファイルが以下の場所にあることを前提としています:

```bash
$DETECTRON2_DATASETS/coco/annotations/instances_train2017.json
```

以下のディレクトリに次のファイルが生成されます:

```bash
$DETECTRON2_DATASETS/coco/annotations/

coco2ytvis2019_train.json
coco2ytvis2021_train.json
coco2ovis_train.json
```

変換スクリプトの実行方法:
```bash
python tools/convert_coco_to_vis.py
```
