# Experimental protocol and provenance

This document summarizes **reported methods** from the author-provided 12-page *ALT YOLO* report. It is not a runnable reproduction guide: no source code, weights, or raw prediction files were supplied for this repository.

## Dataset

- SeaDronesSee Object Detection v2: 14,227 images in the full benchmark (8,930 train / 1,547 validation / 3,750 test).
- Study subset: 4,268 images (2,679 train / 464 validation / 1,125 test), fixed random seed 42.
- Five scored categories: swimmer, boat, jetski, life-saving appliance, buoy. The COCO `ignored` category was removed from the YOLO class mapping.
- Annotations converted from COCO `[xmin, ymin, width, height]` to YOLO normalized center-width-height format.
- Altitude bins: low <15 m; medium 15 to <40 m; high ≥40 m; unknown if metadata missing.

## Models and training

| ID | Configuration | box loss | cls loss |
| --- | --- | ---: | ---: |
| M1 | YOLO26n baseline, end-to-end | 7.5 | 0.5 |
| M2 | YOLO26n with `end2end=False` / NMS | 7.5 | 0.5 |
| M3a | Official YOLO26-P2 variant | 7.5 | 0.5 |
| M3b | Official YOLO26-P2 variant | 9.0 | 0.5 |

Training settings reported: 50 epochs, 1280×1280 input, batch 8, seed 42; mosaic enabled, mixup disabled; default Ultralytics optimizer configuration. The report describes Paperspace Gradient, NVIDIA Quadro P6000 (24 GB) for training and Quadro RTX 4000 (8 GB) for inference/ablation. Software versions in the report include Python 3.11.7 and Ultralytics 8.4.41; the reported PyTorch range varies across sessions.

## Altitude-conditioned SAHI

| Altitude | Tile size | Overlap |
| --- | --- | --- |
| High (≥40 m) | 256×256 | 0.30 |
| Medium (15 to <40 m) | 320×320 | 0.20 |
| Low (<15 m) | 480×480 | 0.10 |
| Unknown | 320×320 | 0.20 |

Predictions were merged with Greedy Non-Maximum Merging, IoU match threshold 0.5. The report includes a 20-image-per-bin detection-count comparison against fixed 320×320 slicing and manual AP evaluation on 464 validation images.

## Glare pipeline

The report describes a high-value/low-saturation HSV glare mask, intensity suppression, and CLAHE contrast enhancement at inference time. It compares baseline, glare only, SAHI only, and SAHI plus glare.

## Evaluation and interpretation

- Main model comparison: validation mAP@50, mAP@50–95, precision, recall, per-class AP, per-altitude AP and recall.
- SAHI: manual validation AP and recall, detection counts, and inference time. Manual AP may not be fully comparable to standard evaluator outputs.
- No official held-out test-set mAP was reported; test annotations were unavailable.
- Results CSV files reproduce the **numbers as printed in the report**. They are not logs or independent calculations.

## Report provenance

- Table 4, page 10: main model comparison.
- Table 5, page 10: per-class AP@50.
- Table 6, page 10: model inference speed.
- Table 7, page 10: fixed vs. altitude-conditioned slicing detection counts.
- Table 8, page 10: inference ablation results.
- Table 10, page 11: per-altitude mAP@50.
- Section 6.4, pages 10–11: SAHI inference runtime.

## What is not in this repository

There are no uploaded training/inference notebooks, dependency lock files, model checkpoints, annotation exports, or raw predictions. To make the experiment independently reproducible, those artifacts would need to be added and their environment, dataset access, and command-line execution documented.
