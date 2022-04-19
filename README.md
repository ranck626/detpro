This is the code base for CVPR2022 paper [Learning to Prompt for Open-Vocabulary Object Detection with Vision-Language Model](https://arxiv.org/abs/2203.14940)

# Prepare data
Download dataset according to [LVIS](https://www.lvisdataset.org/), [VOC](http://host.robots.ox.ac.uk/pascal/VOC/), [COCO](https://cocodataset.org/#home) and [Objects365](https://www.objects365.org/overview.html). Precomputed proposals generated by RPN trained on only base classes can be downloaded from [google drive](https://drive.google.com/drive/folders/1rV6jJxbiELT4GNvfDYnRAuYJTw2AM_yR?usp=sharing) [baiduyun (code:yadt)](https://pan.baidu.com/s/1iNMS1XPzu1lOCObFYMe38g). It is recommended to download and extract the dataset somewhere outside the project directory and symlink the dataset root to data as below.
```
├── mmdet
├── tools
├── configs
├── data
├── ├── lvis_v1
├── ├── ├──annotations
├── ├── ├──train2017
├── ├── ├──val2017
├── ├── ├──proposals
│   ├── coco
│   │   ├── annotations
│   │   ├── train2017
│   │   ├── val2017
│   ├── VOCdevkit
│   │   ├── VOC2007
│   │   ├── VOC2012
│   ├── objects365
│   │   ├── annotations
│   │   ├── train
│   │   ├── val

```
All models use the backbone pretrained with [SoCo](https://github.com/hologerry/SoCo) which can be downloaded from [google drive](https://drive.google.com/file/d/1z6Tb2MPFJDv9qpEyn_J0cJcXOguKTiL0/view?usp=sharing) [baiduyun (code:kwps)](https://pan.baidu.com/s/1FHN-9vsH16w4TAusyHnXvg). Put the pretrained backbone under data/.
# Main Results
| Model                  | Lr Schd   | AP<sup>bb</sup><sub>r</sub> | AP<sup>bb</sup><sub>c</sub> | AP<sup>bb</sup><sub>f</sub> | AP<sup>bb</sup> | AP<sup>mk</sup><sub>r</sub> | AP<sup>mk</sup><sub>c</sub>| AP<sup>mk</sup><sub>f</sub> | AP<sup>mk</sup> | Config | Prompt | Model |
| ---------------------- | --------- | ---- | ---- | ---- | ---- | ------- | ---- | ---- | ---- | ------ | ------ | ----- |
| ViLD*                   | 20 epochs | 17.4 | 27.5 | 31.9 | 27.5 | 16.8 | 25.6 | 28.5 | 25.2 | [config](https://github.com/dyabel/detpro/blob/main/configs/lvis/detpro_ens_20e.py) |  [google drive](https://drive.google.com/file/d/1PMPvEWYLi2Kp2wgIiMR8m9r4mLDkxyUJ/view?usp=sharing) [baiduyun (code:a5ni)](https://pan.baidu.com/s/1Sxz_RklI7F11F07VACVYtg) | [google drive](https://drive.google.com/file/d/1w4ZIZNBTR3ZAZVzlcLgrkatljYpbvAvA/view?usp=sharing) [baiduyun (code:cyhv)](https://pan.baidu.com/s/18ijRffI6ZaDt7hXKMRA5SQ) |
| DetPro (Mask R-CNN)    | 20 epochs | 20.8 | 27.8 | 32.4 | 28.4 | 19.8 | 25.6 | 28.9 | 25.9 | [config](https://github.com/dyabel/detpro/blob/main/configs/lvis/detpro_ens_20e.py) | [google drive](https://drive.google.com/file/d/1T-Ydo0YgneDbZYU2hu3wWm9MZ2plxGT_/view?usp=sharing) [baiduyun (code:uvab)](https://pan.baidu.com/s/1MjV1DqiO0gHftyKjuiPrTA)| [google drive](https://drive.google.com/file/d/1ktTMZWFjUAGjzjlOdzxGfKQR8u9x_OmX/view?usp=sharing) [baiduyun (code:apmq)](https://pan.baidu.com/s/1E7SwKW-p4BpGsQE1j9hXXg)|
| DetPro (Cascade R-CNN) | 20 epochs | 21.7 | 29.6 | 35.0 | 30.5 | 20.0 | 26.7 | 30.4 | 27.0 | [config](https://github.com/dyabel/detpro/blob/main/configs/lvis/cascade_mask_rcnn_r50_fpn_sample1e-3_mstrain_20e_lvis_v1_pretrain_ens.py) |[google drive](https://drive.google.com/file/d/1T-Ydo0YgneDbZYU2hu3wWm9MZ2plxGT_/view?usp=sharing) [baiduyun (code:uvab)](https://pan.baidu.com/s/1MjV1DqiO0gHftyKjuiPrTA)  | [google drive](https://drive.google.com/file/d/1QAtd32QXiXK7haXuHFE_CO24GpKr07Za/view?usp=sharing) [baiduyun (code:5ee9)](https://pan.baidu.com/s/1tmi61kO_JnkuNPDyt455eQ) | 

In the original implementation of ViLD, the whole training process takes up to 180,000 iterations with batchsize of 256, approximately 460 epochs, which is unaffordable. We re-implement ViLD (denoted as ViLD*) with backbone pretrained using [SoCo](https://github.com/hologerry/SoCo). Our re-implementation version achieves comparable AP compared with the original implementation, while reducing the training epochs from 460 to 20.
# Installation
## Dependencies
+ python3.8
+ pytorch 1.7.0
+ cuda 11.0

This repo is built on [mmdetection](https://github.com/open-mmlab/mmdetection), [CLIP](https://github.com/openai/CLIP.git) and [CoOP](https://github.com/kaiyangzhou/coop)

```shell
pip install -r requirements/build.txt
pip install -e .
pip install git+https://github.com/openai/CLIP.git
pip uninstall pycocotools -y
pip uninstall mmpycocotools -y
pip install mmpycocotools
pip install git+https://github.com/lvis-dataset/lvis-api.git
pip install mmcv-full==1.2.5 -f https://download.openmmlab.com/mmcv/dist/cu110/torch1.7.0/index.html
```
# Get Started
## Quick reproduction of the main results
```
./tools/dist_test.sh <config> <model> <gpu_num> --eval bbox segm --cfg-options model.roi_head.prompt_path=<prompt> model.roi_head.load_feature=False 
```
## Prepare data for DetPro training
see [prepare.sh](https://github.com/dyabel/detpro/blob/main/prepare.sh)
This process will take a long time. So we also provide the extracted clip image embeddings of precomputed proposals [baiduyun (code:o4n5)](https://pan.baidu.com/s/13vp8s6mU9IKSbF-OPRtX9g). You can download all these zip files and merge them into one file (lvis_clip_image_embedding.zip).
## Train DetPro
see [detpro.sh](https://github.com/dyabel/detpro/blob/main/detpro.sh)
## Train ViLD with DetPro (Mask R-CNN)
see [vild_detpro.sh](https://github.com/dyabel/detpro/blob/main/vild_detpro.sh)
## Tranfer experiments
see [transer.sh](https://github.com/dyabel/detpro/blob/main/transfer.sh)
# Citation
```
@article{du2022learning,
  title={Learning to Prompt for Open-Vocabulary Object Detection with Vision-Language Model},
  author={Du, Yu and Wei, Fangyun and Zhang, Zihe and Shi, Miaojing and Gao, Yue and Li, Guoqi},
  journal={arXiv preprint arXiv:2203.14940},
  year={2022}
}
```
