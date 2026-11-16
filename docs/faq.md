# Frequently asked questions

## When will the code be public?

The source code will be released after paper acceptance. Installation and usage documentation may be shared in advance; running the planner requires the source package.

## Why can I not access the Hugging Face repository?

[Xinghaoyu/WTR-Drive](https://huggingface.co/Xinghaoyu/WTR-Drive) is currently private. Logging in is sufficient only if that account already has access to the repository. The link does not provide public download access.

## What is needed in addition to the WTR-Drive weights?

The planner source, a compatible CUDA/MMCV environment, the matching NAVSIM data and metric caches, maps, and the upstream V-JEPA initialization checkpoint are required. The current constructor loads that initialization checkpoint before the full planner weights. See [installation and data preparation](installation.md).

## Which checkpoint should I use?

Use `navsim_v1/model.ckpt` for NAVSIM v1 and `navsim_v2/model.ckpt` for NAVSIM v2, with the corresponding configuration. Both are full-model checkpoints with Task readout enabled. Other training variants require their own matching checkpoints.

## Are the v1 and v2 scores directly interchangeable?

No. V1 reports PDMS; v2 reports EPDMS using expert-frame sequential evaluation without HPF correction. The reported 88.24 is not a full two-stage pseudo-closed-loop result. HPF-corrected scores must be reported separately. See [evaluation details](reproduction.md).

## How much GPU memory is required?

A minimum inference VRAM requirement has not yet been measured for the release package. Full-planner execution requires a compatible GPU and CUDA operators. The reported 9-node training setup describes the original experiment, not a minimum requirement for inference.

## Can the exported checkpoints resume training?

They contain model tensors only. They do not contain the optimizer and scheduler state required to resume the original training run exactly.

## Does the concept figure show a measured driving result?

No. It is drawn from geometric elements to explain shared scene estimation, candidate-specific reads, and object/time risk. Its trajectories do not represent measured benchmark outcomes. Performance is summarized by the aggregate navtest metrics.

## Is there a WTR-Drive demo video?

A WTR-Drive demo video has not yet been prepared. The current preview includes the World–Task–Risk concept figure.

## Who should I contact?

For research questions or feedback, contact Haoyu Xing at [17843105684@163.com](mailto:17843105684@163.com).
