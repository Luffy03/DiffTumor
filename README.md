# DiffTumor
[CVPR 2024] Generalizable Tumor Synthesis

# Synthetic Tumors Make AI Segment Tumors Better

This repository provides extensive examples of synthetic liver tumors generated by our novel strategies. Check to see if you could tell which is real tumor and which is synthetic tumor. More importantly, our synthetic tumors can be used for training AI models, and have proven to achieve a similar (actually, *better*) performance in real tumor segmentation than a model trained on real tumors. 

**Amazing**, right? 

<p align="center"><img width="100%" src="figures/VisualTuringTest.png" /></p>
<p align="center"><img width="100%" src="figures/Examples.gif" /></p>

## Paper

<b>Towards Generalizable Tumor Synthesis</b> <br/>
[Qi Chen](https://scholar.google.com/citations?user=4Q5gs2MAAAAJ&hl=en)<sup>1</sup>, [Xiaoxi Chen](https://scholar.google.com/citations?hl=en&user=bVHYVXQAAAAJ)<sup>2</sup>, [Alan L. Yuille](https://www.cs.jhu.edu/~ayuille/)<sup>3</sup>, [Zhiwei Xiong](http://staff.ustc.edu.cn/~zwxiong/)<sup>1</sup>, [Wei Chen](https://weichen582.github.io/)<sup>3</sup> and [Zongwei Zhou](https://www.zongweiz.com/)<sup>3,*</sup> <br/>
<sup>1 </sup>University of Science and Technology of China,  <br/>
<sup>2 </sup>Shanghai Jiao Tong University,  <br/>
<sup>3 </sup>Johns Hopkins University  <br/>

CVPR, 2024 <br/>
[paper]() | [code](https://github.com/MrGiovanni/DiffTumor) | [talk]() | [slides]() | [poster](h)


**We have documented common questions for the paper in [Frequently Asked Questions (FAQ)](documents/FAQ.md).**

**We have also provided a list of publications related to tumor synthesis in [Awesome Synthetic Tumors](AWESOME.md) [![Awesome](https://awesome.re/badge.svg)](https://awesome.re).**

## Model

| Tumor | Model | Download |
| ----  | ----  | ----     |
| liver  | U-Net  | [link]() |
| pancreas  | U-Net | [link]() |
| kidney  | U-Net | [link]() |
| real  | nnU-Net | [link]() |
| real  | nnU-Net | [link]() |
| synt  | nnU-Net | [link]() |
| synt  | swin_unetrv2_base | [link]() |
| synt  | swin_unetrv2_base | [link]() |
| synt  | swin_unetrv2_small | [link]() |
| synt  | swin_unetrv2_tiny | [link]() |

**Use the following command to download everything.**
```bash
wget https://www.dropbox.com/xxx
tar -xzvf checkpoint.tar.gz
```
   
## 0. Installation

```bash
git clone https://github.com/MrGiovanni/DiffTumor.git
cd DiffTumor
```

See [installation instructions](https://github.com/MrGiovanni/DiffTumor/blob/main/INSTALL.md).

## 1. Train segmentation models using synthetic tumors

```
datapath=/mnt/zzhou82/PublicAbdominalData/

# U-Net
CUDA_VISIBLE_DEVICES=0 python -W ignore main.py --optim_lr=4e-4 --batch_size=2 --lrschedule=warmup_cosine --optim_name=adamw --model_name=unet --val_every=200 --max_epochs=4000 --save_checkpoint --workers=2 --noamp --distributed --dist-url=tcp://127.0.0.1:12235 --cache_num=200 --val_overlap=0.5 --syn --logdir="runs/synt.no_pretrain.unet" --train_dir $datapath --val_dir $datapath --json_dir datafolds/healthy.json
# nnU-Net
CUDA_VISIBLE_DEVICES=0 python -W ignore main.py --optim_lr=4e-4 --batch_size=2 --lrschedule=warmup_cosine --optim_name=adamw --model_name=swin_unetrv2 --swin_type=base --val_every=200 --max_epochs=4000 --save_checkpoint --workers=2 --noamp --distributed --dist-url=tcp://127.0.0.1:12231 --cache_num=200 --val_overlap=0.5 --syn --logdir="runs/synt.pretrain.swin_unetrv2_base" --train_dir $datapath --val_dir $datapath --json_dir datafolds/healthy.json --use_pretrained
# Swin-UNETR
CUDA_VISIBLE_DEVICES=0 python -W ignore main.py --optim_lr=4e-4 --batch_size=2 --lrschedule=warmup_cosine --optim_name=adamw --model_name=swin_unetrv2 --swin_type=base --val_every=200 --max_epochs=4000 --save_checkpoint --workers=2 --noamp --distributed --dist-url=tcp://127.0.0.1:12231 --cache_num=200 --val_overlap=0.5 --syn --logdir="runs/synt.no_pretrain.swin_unetrv2_base" --train_dir $datapath --val_dir $datapath --json_dir datafolds/healthy.json
```

## 2. Evaluation

#### AI model trained by synthetic tumors

```
datapath=/mnt/zzhou82/PublicAbdominalData/

# U-Net
CUDA_VISIBLE_DEVICES=0 python -W ignore validation.py --model=unet --val_overlap=0.75 --val_dir $datapath --json_dir datafolds/lits.json --log_dir runs/synt.no_pretrain.unet --save_dir out
# Swin-UNETR-Base (pretrain)
CUDA_VISIBLE_DEVICES=0 python -W ignore validation.py --model=swin_unetrv2 --swin_type=base --val_overlap=0.75 --val_dir $datapath --json_dir datafolds/lits.json --log_dir runs/synt.pretrain.swin_unetrv2_base --save_dir out
# nnU-Net
CUDA_VISIBLE_DEVICES=0 python -W ignore validation.py --model=swin_unetrv2 --swin_type=base --val_overlap=0.75 --val_dir $datapath --json_dir datafolds/lits.json --log_dir runs/synt.no_pretrain.swin_unetrv2_base --save_dir out
# Swin-UNETR
CUDA_VISIBLE_DEVICES=0 python -W ignore validation.py --model=swin_unetrv2 --swin_type=small --val_overlap=0.75 --val_dir $datapath --json_dir datafolds/lits.json --log_dir runs/synt.no_pretrain.swin_unetrv2_small --save_dir out
```

## TODO

- [ ] Upload the paper to arxiv
- [ ] Make a video about Visual Turing Test (will appear in YouTube)
- [ ] Make an online app for Visual Turing Test
- [ ] Apply for a US patent
- [ ] Upload the evaluation code for small tumors
- [ ] Upload the evaluation code for the false-positive study
- [ ] Make a Jupyter Notebook for tumor synthesis

## Citation

```
@inproceedings{hu2023label,
  title={Label-free liver tumor segmentation},
  author={Hu, Qixin and Chen, Yixiong and Xiao, Junfei and Sun, Shuwen and Chen, Jieneng and Yuille, Alan L and Zhou, Zongwei},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={7422--7432},
  year={2023}
}

@article{hu2022synthetic,
  title={Synthetic Tumors Make AI Segment Tumors Better},
  author={Hu, Qixin and Xiao, Junfei and Chen, Yixiong and Sun, Shuwen and Chen, Jie-Neng and Yuille, Alan and Zhou, Zongwei},
  journal={NeurIPS Workshop on Medical Imaging meets NeurIPS},
  year={2022}
}

@article{li2023early,
  title={Early Detection and Localization of Pancreatic Cancer by Label-Free Tumor Synthesis},
  author={Li, Bowen and Chou, Yu-Cheng and Sun, Shuwen and Qiao, Hualin and Yuille, Alan and Zhou, Zongwei},
  journal={arXiv preprint arXiv:2308.03008},
  year={2023}
}
```

## Acknowledgement

This work was supported by the Lustgarten Foundation for Pancreatic Cancer Research and the McGovern Foundation. The segmentation backbone is based on [Swin UNETR](https://github.com/Project-MONAI/tutorials/blob/main/3d_segmentation/swin_unetr_btcv_segmentation_3d.ipynb); we appreciate the effort of the [MONAI Team](https://monai.io/) to provide and maintain open-source code to the community. We thank Camille Torrico and Alexa Delaney for improving the writing of this paper. Paper content is covered by patents pending.
