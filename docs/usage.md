# Weight download and usage (preview)

**Training and evaluation commands require the source package, which will be released after paper acceptance.** The examples below match its prepared launcher. Model access and code availability are separate: the Hugging Face repository is currently private.

## Download weights

Install the Hugging Face CLI in your download environment, then log in from the terminal:

```bash
python -m pip install huggingface_hub
hf auth login
```

With an account authorized for [Xinghaoyu/WTR-Drive](https://huggingface.co/Xinghaoyu/WTR-Drive):

```bash
hf download Xinghaoyu/WTR-Drive --local-dir ./weights
```

The two checkpoints are `weights/navsim_v1/model.ckpt` and `weights/navsim_v2/model.ckpt`. Each has a matching `config.yaml` and `model.json`; the repository also contains results, validation information, and `SHA256SUMS`. The checkpoints contain model tensors only and do not restore optimizer or scheduler state for training resumption.

## Prepare the environment

After the source release, follow [installation and data preparation](installation.md). Run the examples from the source-package root, setting:

```bash
export WTR_DATA_ROOT=/path/to/data
export DRIVE_JEPA_VJEPA2_CKPT=/path/to/vitl_merge_3dataset_e50.pt
```

## Evaluate

```bash
python scripts/run.py eval v1 \
  --checkpoint ./weights/navsim_v1/model.ckpt \
  --output ./outputs/navsim_v1

python scripts/run.py eval v2 \
  --checkpoint ./weights/navsim_v2/model.ckpt \
  --output ./outputs/navsim_v2
```

The default is the full model with Task readout enabled. V1 reports PDMS; v2 uses expert-frame sequential evaluation without HPF correction. Read `score × 100` from the `average` row for v1 and the `average_expert_frames` row for v2. Keep `score_hpf_corrected` separate from the reported 88.24 EPDMS.

Append `--dry-run` to print the command without starting inference, or `--print-config` to inspect the resolved configuration. For an initial four-scene smoke test:

```bash
python scripts/run.py eval v1 \
  --checkpoint ./weights/navsim_v1/model.ckpt \
  --output ./outputs/smoke_v1 -- train_test_split.scene_filter.max_scenes=4
```

A smoke-test score is not the full-navtest result.

## Train

```bash
python scripts/run.py train v1 --devices 8 --output ./outputs/train_v1
python scripts/run.py train v2 --devices 8 --output ./outputs/train_v2
```

These examples use eight local GPUs. The reported training used a global batch size of 576 across 9 nodes and 16 devices per node; a smaller job changes the training recipe. See [reproduction details](reproduction.md).

## Readout intervention

Readout Off evaluates the same checkpoint with its final Task readout contribution suppressed:

```bash
python scripts/run.py eval v1 --variant main --readout-scale 0 \
  --checkpoint ./weights/navsim_v1/model.ckpt \
  --output ./outputs/v1_readout_off
```

Keep the readout head in the architecture when loading this checkpoint. This intervention is distinct from training a baseline without the head.
