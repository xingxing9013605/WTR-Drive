# Reproduction information

This preview records the manuscript settings. The source code and experiment configurations will be released after paper acceptance. Installation and usage commands are provided in advance.

## Main results and evaluation

| Setting | NAVSIM v1 | NAVSIM v2 |
|---|---|---|
| Split | navtest | navtest |
| Non-missing expert-scene records | 12,146 | 12,146 |
| Metric, on a 0–100 scale | PDMS | EPDMS |
| Reported score | 94.66 | 88.24 |
| Evaluation | Expert scenes | Expert-frame sequential, without HPF correction |
| CSV summary row | `average` | `average_expert_frames` |
| Selected checkpoint epoch, zero-based | 41 | 47 |
| Training seed | 0 | 0 |

Both scores come from the original formal evaluations. The v2 protocol does not measure full two-stage pseudo-closed-loop performance. HPF-corrected EPDMS is a separate aggregate. Differences should be computed from unrounded values before displaying two decimal places. Candidate diagnostics during training use a separate 512-scene validation subset.

## Model settings

The main model is E6B (`scene_query_task_readout_components`) with Task readout scale 1 and subscore supervision. It does not enable the optional map class weighting and convolutional refinement.

| Setting | Value |
|---|---|
| Visual encoder | V-JEPA ViT-L |
| Inputs | Left, front, and right cameras; two frames per camera |
| Image size | 256 × 512 |
| Candidate trajectories | 32 |
| Future poses per candidate | 8 at 0.5 s intervals |
| Refinement rounds | 4, with shared parameters |
| World slots | 32 object slots and an 8 × 8 map grid |

## Training settings

The reported runs use 50 epochs, Adam with learning rate 1e-4, a 0.1 backbone learning-rate multiplier, and mixed precision. A ReduceLROnPlateau schedule monitors validation performance. Global batch size is 576: 9 nodes × 16 devices × 4 samples, without gradient accumulation. V1 uses gradient clipping at 1; v2 disables gradient clipping.

A smaller GPU job is supported by the prepared launcher but does not automatically reproduce the original effective batch size. Full-planner execution requires a compatible CUDA/MMCV environment; the release package's minimum inference VRAM requirement is not yet measured.

## Validation status

Completed during packaging:

- Matched the source checkpoint hashes against the checkpoints used in the formal evaluations.
- Exported tensor-only v1/v2 checkpoints and checked every tensor against its source.
- Strictly loaded the World/Task modules and scorer; checked four refinement rounds on synthetic CPU inputs.
- Parsed source and configuration files and resolved the main and ablation configurations.
- Uploaded the weight package privately, downloaded all 10 files from the committed revision, and verified their SHA-256 hashes.

Still pending for the release package: a fresh installation of the complete CUDA stack, full-planner GPU inference on real scenes, and a new full-navtest evaluation. CPU module checks and the original formal scores do not substitute for these checks.
