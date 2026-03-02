## 可視化

1. まず、モデルと対応する設定ファイルを選択します。
   [モデルZoo](model_zoo/dinov2)から選択できます。
2. 次に、以下のコマンドを実行します:

```bash
cd visualization

python video_demo.py \
  --config-file /path/to/config.yaml \
  --input /path/to/images_folder \
  --output /path/to/output \
  --opts MODEL.WEIGHTS /path/to/weight.pth
```

`/path/to/config.yaml` を設定ファイルのパスに置き換えてください。
`/path/to/images_folder` を動画フレームが格納されたフォルダのパスに置き換えてください。
`/path/to/weight.pth` を評価するチェックポイントのパスに置き換えてください。
`/path/to/output` を出力フォルダのパスに置き換えてください。
