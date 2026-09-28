<div align="center">
  <a href="https://autooptm.com"><img src=".autooptm/logo.png" width="96" alt="AutoOptm"></a>

  <h1>Sapiens · optimized by <a href="https://autooptm.com">AutoOptm</a></h1>

  <p><b>3.75x faster end to end</b> on the command below, output verified against the stock program.</p>

  <p>
    <a href="https://autooptm.com"><img alt="speedup" src="https://img.shields.io/badge/end--to--end-3.75x-2ea44f"></a>
    <a href="https://github.com/facebookresearch/sapiens/commit/2cb07227a740cf09896309ea3a3b8fa44429865c"><img alt="base" src="https://img.shields.io/badge/upstream-2cb07227a740-blue"></a>
    <img alt="card" src="https://img.shields.io/badge/measured%20on-A10-lightgrey">
  </p>
</div>

> This is a fork of [facebookresearch/sapiens](https://github.com/facebookresearch/sapiens) at commit
> [`2cb07227a740`](https://github.com/facebookresearch/sapiens/commit/2cb07227a740cf09896309ea3a3b8fa44429865c) with the AutoOptm patch applied on top.
> The optimisation was found, measured and verified automatically by [AutoOptm](https://autooptm.com);
> the patch is also kept at [`.autooptm/autooptm.patch`](.autooptm/autooptm.patch).

## The result

| | |
|---|---|
| **Command** | `python lite/demo/vis_pose.py sapiens_0.3b_goliath_best_goliath_AP_573_torchscript.pt2 --input <frames> --output-root <out> --batch_size 4 --num_keypoints 308` |
| **Entry point** | `lite/demo/vis_pose.py` |
| **Unit measured** | one frame of 8 in-the-wild demo frames (`pose/demo/data/itw_videos/reel1`), run in batches of 4: read → per-person crop → Sapiens 0.3B 308-keypoint pose (TorchScript checkpoint) → keypoints decoded → result written to the output directory |
| **Before (stock)** | 938 ms per frame (median of 3 runs) |
| **After (this tree)** | 250 ms per frame (median of 9 runs; process start-up and the first, warm-up batch are not included in either arm) |
| **Speedup** | **3.75x** end to end on A10, noise floor of the host 0.8% |
| **Output** | the pose heatmaps stay within 0.011 (max abs) of the stock fp32 model's, PSNR 82.8 dB against them; within 0.014 on held-out batch sizes (2 and 6) the optimiser never saw; every run writes all 8 result files |

### What changed

| File | Where | Gain |
|---|---|---|
| `lite/demo/vis_pose.py` | main() -- model setup | — |
| `lite/demo/vis_pose.py` | batch_inference_topdown() | — |
| `lite/demo/vis_pose.py` | preprocess_pose() | — |
| `lite/demo/vis_pose.py` | main() -- dataset construction and per-batch call | — |
| `lite/demo/adhoc_image_dataset.py` | AdhocImageDataset.__init__ / __getitem__ | — |
| `lite/demo/pose_utils.py` | gaussian_blur() | — |
| `lite/demo/pose_utils.py` | refine_keypoints_dark_udp() | — |

Gains per change were not recorded separately for this run; the 3.75x above is the whole patch, measured end to end.

## Reproduce

```bash
git clone https://github.com/autooptm/sapiens-ao.git
cd sapiens-ao
# set up exactly as upstream documents for Sapiens-Lite (the 0.3B pose TorchScript checkpoint
# from facebook/sapiens-pose-0.3b-torchscript, frames in a folder), then:
python lite/demo/vis_pose.py sapiens_0.3b_goliath_best_goliath_AP_573_torchscript.pt2 --input <frames> --output-root <out> --batch_size 4 --num_keypoints 308
```

The diff against upstream is one commit: `git log -1 -p` shows it, and
`git diff 2cb07227a740` is the same patch as `.autooptm/autooptm.patch`.

---

<div align="center"><sub>Optimized by <a href="https://autooptm.com">AutoOptm</a> — point it at a repository, get back a verified speedup and the patch.</sub></div>

---

<p align="center">
  <img src="./assets/sapiens_animation.gif" alt="Sapiens" title="Sapiens" width="500"/>
</p>

<p align="center">
   <h2 align="center">Foundation for Human Vision Models</h2>
   <p align="center">
      <a href="https://rawalkhirodkar.github.io/"><strong>Rawal Khirodkar</strong></a>
      ·
      <a href="https://scholar.google.ch/citations?user=oLi7xJ0AAAAJ&hl=en"><strong>Timur Bagautdinov</strong></a>
      ·
      <a href="https://una-dinosauria.github.io/"><strong>Julieta Martinez</strong></a>
      ·
      <a href="https://about.meta.com/realitylabs/"><strong>Su Zhaoen</strong></a>
      ·
      <a href="https://about.meta.com/realitylabs/"><strong>Austin James</strong></a>
      <br>
      <a href="https://www.linkedin.com/in/peter-selednik-05036499/"><strong>Peter Selednik</strong></a>
      .
      <a href="https://scholar.google.fr/citations?user=8orqBsYAAAAJ&hl=ja"><strong>Stuart Anderson</strong></a>
      .
      <a href="https://shunsukesaito.github.io/"><strong>Shunsuke Saito</strong></a>
   </p>
   <h3 align="center">ECCV 2024 - Best Paper Candidate</h3>
</p>

<p align="center">
   <a href='https://about.meta.com/realitylabs/codecavatars/sapiens/'>
      <img src='https://img.shields.io/badge/Sapiens-Page-azure?style=for-the-badge&logo=Google%20chrome&logoColor=white&labelColor=000080&color=007FFF' alt='Project Page'>
   </a>

   <a href="https://arxiv.org/abs/2408.12569">
      <img src='https://img.shields.io/badge/Paper-PDF-green?style=for-the-badge&logo=adobeacrobatreader&logoWidth=20&logoColor=white&labelColor=66cc00&color=94DD15' alt='Paper PDF'>
   </a>

   <a href='https://huggingface.co/collections/facebook/sapiens-66d22047daa6402d565cb2fc'>
      <img src='https://img.shields.io/badge/HuggingFace-Demo-orange?style=for-the-badge&logo=huggingface&logoColor=white&labelColor=FF5500&color=orange' alt='Spaces'>
   </a>

   <a href='https://rawalkhirodkar.github.io/sapiens/'>
      <img src='https://img.shields.io/badge/More-Results-ffffff?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0id2hpdGUiIHdpZHRoPSIxOCIgaGVpZ2h0PSIxOCI+PHBhdGggZD0iTTAgMGgyNHYyNEgweiIgZmlsbD0ibm9uZSIvPjxwYXRoIGQ9Ik0xOSAzSDVjLTEuMSAwLTIgLjktMiAydjE0YzAgMS4xLjkgMiAyIDJoMTRjMS4xIDAgMi0uOSAyLTJWNWMwLTEuMS0uOS0yLTItMnpNOSAxN0g3di01aDJ2NXptNCAwaC0ydi03aDJ2N3ptNCAwaC0yVjhoMnY5eiIvPjwvc3ZnPg==&logoColor=white&labelColor=8A2BE2&color=9370DB' alt='Results'>
   </a>
</p>

Sapiens offers a comprehensive suite for human-centric vision tasks (e.g., 2D pose, part segmentation, depth, normal, etc.). The model family is pretrained on 300 million in-the-wild human images and shows excellent generalization to unconstrained conditions. These models are also designed for extracting high-resolution features, having been natively trained at a 1024 x 1024 image resolution with a 16-pixel patch size.

<p align="center">
  <img src="./assets/01.gif" alt="01" title="01" width="400"/>
  <img src="./assets/03.gif" alt="03" title="03" width="400"/>
</p>
<p align="center">
  <img src="./assets/02.gif" alt="02" title="02" width="400"/>
  <img src="./assets/04.gif" alt="04" title="04" width="400"/>
</p>

Sapiens2 is out! Please checkout: https://github.com/facebookresearch/sapiens2

## 🚀 Getting Started

### Clone the Repository
   ```bash
   git clone https://github.com/facebookresearch/sapiens.git
   export SAPIENS_ROOT=/path/to/sapiens
   ```

### Recommended: Lite Installation (Inference-only)
   For users setting up their own environment primarily for running existing models in inference mode, we recommend the [Sapiens-Lite installation](lite/README.md).\
   This setup offers optimized inference (4x faster) with minimal dependencies (only PyTorch + numpy + cv2).

### Full Installation
   To replicate our complete training setup, run the provided installation script. \
   This will create a new conda environment named `sapiens` and install all necessary dependencies.

   ```bash
   cd $SAPIENS_ROOT/_install
   ./conda.sh
   ```

   Please download the **original** checkpoints from [hugging-face](https://huggingface.co/facebook/sapiens). \
   You can be selective about only downloading the checkpoints of interest.\
   Set `$SAPIENS_CHECKPOINT_ROOT` to be the path to the `sapiens_host` folder. Place the checkpoints following this directory structure:
   ```plaintext
   sapiens_host/
   ├── detector/
   │   └── checkpoints/
   │       └── rtmpose/
   ├── pretrain/
   │   └── checkpoints/
   │       ├── sapiens_0.3b/
               ├── sapiens_0.3b_epoch_1600_clean.pth
   │       ├── sapiens_0.6b/
               ├── sapiens_0.6b_epoch_1600_clean.pth
   │       ├── sapiens_1b/
   │       └── sapiens_2b/
   ├── pose/
      └── checkpoints/
         ├── sapiens_0.3b/
   └── seg/
   └── depth/
   └── normal/
   ```

## 🌟 Human-Centric Vision Tasks
We finetune sapiens for multiple human-centric vision tasks. Please checkout the list below.

- ###  [Image Encoder](docs/PRETRAIN_README.md) <sup><small><a href="lite/docs/PRETRAIN_README.md" style="color: #FFA500;">[lite]</a></small></sup>
- ### [Pose Estimation](docs/POSE_README.md) <sup><small><a href="lite/docs/POSE_README.md" style="color: #FFA500;">[lite]</a></small></sup>
- ### [Body Part Segmentation](docs/SEG_README.md) <sup><small><a href="lite/docs/SEG_README.md" style="color: #FFA500;">[lite]</a></small></sup>
- ### [Depth Estimation](docs/DEPTH_README.md) <sup><small><a href="lite/docs/DEPTH_README.md" style="color: #FFA500;">[lite]</a></small></sup>
- ### [Surface Normal Estimation](docs/NORMAL_README.md) <sup><small><a href="lite/docs/NORMAL_README.md" style="color: #FFA500;">[lite]</a></small></sup>

## 🎯 Easy Steps to Finetuning Sapiens
Finetuning our models is super-easy! Here is a detailed training guide for the following tasks.
- ### [Pose Estimation](docs/finetune/POSE_README.md)
- ### [Body-Part Segmentation](docs/finetune/SEG_README.md)
- ### [Depth Estimation](docs/finetune/DEPTH_README.md)
- ### [Surface Normal Estimation](docs/finetune/NORMAL_README.md)

## 📈 Quantitative Evaluations
- ### [Pose Estimation](docs/evaluate/POSE_README.md)

## 🤝 Acknowledgements & Support & Contributing
We would like to acknowledge the work by [OpenMMLab](https://github.com/open-mmlab) which this project benefits from.\
For any questions or issues, please open an issue in the repository.\
See [contributing](CONTRIBUTING.md) and the [code of conduct](CODE_OF_CONDUCT.md).

## License
This project is licensed under [LICENSE](LICENSE).\
Portions derived from open-source projects are licensed under [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).

## 📚 Citation
If you use Sapiens in your research, please consider citing us.
```bibtex
@article{khirodkar2024sapiens,
  title={Sapiens: Foundation for Human Vision Models},
  author={Khirodkar, Rawal and Bagautdinov, Timur and Martinez, Julieta and Zhaoen, Su and James, Austin and Selednik, Peter and Anderson, Stuart and Saito, Shunsuke},
  journal={arXiv preprint arXiv:2408.12569},
  year={2024}
}
```
