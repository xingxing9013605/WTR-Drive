# Installation and data preparation (preview)

**Source code will be released after paper acceptance.** The commands below describe the prepared source package. Paths such as `Drive-JEPA/requirements.txt` become available with that release; this documentation preview cannot run the planner by itself.

## Reference environment

The inherited software stack uses Python 3.9/3.10, PyTorch 2.1.0, torchvision 0.16.0, CUDA 12.1, MMCV-full 1.7.2, MMDetection 2.28.2, and PyTorch Lightning 2.2.1. A fresh installation of the complete GPU stack remains to be verified; see [validation status](reproduction.md#validation-status).

After obtaining the source package, run from its root in a separate environment:

```bash
conda create -n wtr-drive python=3.10
conda activate wtr-drive
python -m pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 \
  --index-url https://download.pytorch.org/whl/cu121
python -m pip install -r Drive-JEPA/requirements.txt
python -m pip install tensorboard
```

MMCV requires compiled CUDA operators. If a matching wheel is unavailable, build MMCV-full 1.7.2 against the same PyTorch and CUDA toolchain, using a CUDA toolkit with `nvcc`. Check the environment before running the planner:

```bash
python -c 'import torch; from mmcv.ops import MultiScaleDeformableAttnFunction; print(torch.__version__, torch.cuda.is_available())'
```

The planned package retains both NAVSIM source trees because v2 shares model components with v1. Its launcher selects the version using `PYTHONPATH`.

## Data and upstream resources

Follow the [NAVSIM data instructions](https://github.com/autonomousvision/navsim/blob/main/docs/install.md) and the [Drive-JEPA resource repository](https://huggingface.co/datasets/LinhanWang/Drive-JEPA) to obtain the matching scenes, sensor data, maps, metric caches, teacher-anchor indices, and visual-encoder initialization checkpoint. Use the caches for the selected NAVSIM version.

The prepared launcher expects this layout by default:

```text
WTR_DATA_ROOT/
├── openscene/openscene-v1.1/
│   ├── navsim_logs/{trainval,test}/
│   ├── sensor_blobs/{trainval,test}/
│   └── synthetic_scenes/                 # when required by the v2 configuration
├── nuplan/dataset/maps/
└── shared_dataset/
    ├── drive_jepa_v1/
    │   ├── train_metric_cache/
    │   ├── anchors_scores_index/
    │   └── metric_cache/
    └── drive_jepa_v2/
        ├── train_metric_cache_v2_pruned/
        ├── anchors_scores_index_v2/
        └── metric_cache_v2/
```

Set the resource paths before invoking the launcher:

```bash
export WTR_DATA_ROOT=/path/to/data
export DRIVE_JEPA_VJEPA2_CKPT=/path/to/vitl_merge_3dataset_e50.pt
```

The current model constructor needs the upstream V-JEPA initialization checkpoint for both training and evaluation, before loading the WTR-Drive planner weights. Cached metadata may also need its paths updated to match local resources.

## Hardware

Full-planner execution requires a compatible NVIDIA GPU and the CUDA operators above. The reported training used 9 nodes × 16 devices × 4 samples per device, for a global batch size of 576. This is the experiment scale, not a minimum hardware requirement. A minimum inference VRAM requirement has not yet been measured for the release package.

Continue with [weight download and usage](usage.md).
