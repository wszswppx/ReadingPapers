# VideoAvatar踩坑笔记
菜鸟复现VideoAvatar，这虽然是一个base project，但年久失修，其中存在太多问题……

## 代码文件逻辑
代码分为三个步骤：
```
1. step1_pose.py: pose reconstruction
2. step2_consensus.py: consensus shape optimization
3. step3_texture.py: texture calculation
```
总结为以下三步：  
1. `run_step1.sh` :   
keypoints.hdf5 + masks.hdf5 + camera.pkl => reconstructed_poses.hdf5  
这一步运行很慢，要七八个小时，记得使用tmux

2. `run_step2.sh` :   
reconstructed_poses.hdf5 + masks.hdf5 + camera.pkl => consensus.pkl + consensus.obj

3. `run_step3.sh` :   
consensus.pkl + camera.pkl + .mp4 + reconstructed_poses.hdf5 + masks.hdf5 => texture.jpg

故需运行videoavatar需要的前置文件：  
1. keypoints.hdf5
2. masks.hdf5
3. camera.pkl
4. .mp4

## 环境配置时的bug
VA的项目主页给出的说明文档十分不严谨，让人看了云里雾里，甚至其requirements.txt也写得不好。  
安装环境时需要注意：  
1. 需要python=2.7
2. 需要tensorflow-gpu==1.14.0
3. 需要CUDA==10.0以上，cuDNN==7.4以上

## 运行在自己的数据集上
videoavatar本身是可以在people_snapshot或者ZJU_mocap上运行，如果想通过自己的数据集运行该咋办呢？这个我还在研究。  
但不可缺少的是4个前置文件。
