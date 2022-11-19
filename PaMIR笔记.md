# PaMIR笔记
这是我在复现开源项目[PaMIR](https://github.com/ZhengZerong/PaMIR)时遇到的问题。
# 尝试在windows下安装：环境配置
## CUDA版本安装
注意！！本项目要求的CUDA=11.0，没有充分经验的专业人员不要尝试用别的版本代替！  
在Windows下安装CUDA=11.0与相应的cuDNN=8.0.4，[参考链接](https://zhuanlan.zhihu.com/p/349012526)  
这个过程很快  
## 项目虚拟环境配置
注意！！本项目运行于 **python=3.8**，选择错误的python版本会导致依赖安装失败！  
安装环境：
```
conda create -n py38 python=3.8
conda activate py38
pip install -r requirements.txt
```
1. 在安装torch的时候可能会报错找不到对应的版本，默认给定的里面最旧的也只有torch==1.7.1，而项目要求1.7.0.  
故可以去官网看[Previous Release](https://pytorch.org/get-started/previous-versions/)  
找到对应的版本安装命令，稍微改一下torchvision的版本：
```
# CUDA 11.0
conda install pytorch==1.7.0 torchvision==0.8.1 torchaudio==0.7.0 cudatoolkit=11.0 -c pytorch
```
2. windows安装opendr  
无法用`pip install opendr`
直接完成，而需要手动安装，[参考链接](https://blog.csdn.net/p690075426/article/details/107130220)  

3. 安装openEXR时可能报错 
需要手动安装，在[官网](https://www.lfd.uci.edu/~gohlke/pythonlibs/#openexr)找到对应版本的`.whl`安装包：[链接](https://download.lfd.uci.edu/pythonlibs/archived/OpenEXR-1.3.8-cp38-cp38-win_amd64.whl)  
下载好以后使用在`py38`环境下的命令行执行：
```
pip install OpenEXR-1.3.8-cp38-cp38-win_amd64.whl
```  
**完成上述操作以后再执行`pip install -r requirements.txt`就可以看到全能通过啦**  
## 运行Demo
1. 下载预训练模型
正常Windows人一般不使用它给的那一套命令，而是直接去[下载它的压缩包](https://github.com/ZhengZerong/PaMIR/releases/)(这里面有个Assets，下载里面的results.zip到项目的./network目录下)  
然后解压它即可。  
2. 运行预训练模型：  
```
cd ./networks
python main_test.py
cd ..
```
但会出现`ImportError: cannot import name '_nt_quote_args' from 'distutils.spawn'`  
[查了一下](https://blog.csdn.net/qq_51123264/article/details/126021303)，需要
```
pip install setuptools==59.6.0
```
更新的版本反而不行。  
3. 出现报错：
```
subprocess.CalledProcessError: Command '['where', 'cl']' returned non-zero exit status 1
```
[查了一下](https://blog.csdn.net/iiiiiiimp/article/details/126941469)发现是一个玄学问题，电脑上不能安装VS2022，而要换成VS2019……这就是个大工程了，有点不敢轻举妄动。  
[又查了一个](https://blog.csdn.net/Arsmart/article/details/122411994)，说的很详细，但题主最后也不知道自己是靠哪一步解决这个问题的。试了一下第一步，cl配置成果了，但项目代码依然运行失败，报同样的错，怀疑是不是要重启一下。总之*2022年11月18日20:04:18*今天就到这里了。  
*2022年11月19日*昨晚走的时候重启了电脑，然后发现cl问题消失了。  
4. 出现许多报错,大概为CUDA问题  
挑了几行看起来比较关键的列出来：
```
FAILED: voxelize_cuda.cuda.o
nvcc fatal : unsupported gpu architecture 'compute_86'
subprocess.CalledProcessError: Command '['ninja', '-v']' returned non-zero exit status 1.
RuntimeError: Error building extension 'voxelize_cuda'
```
此时使用`nvidia-smi`查看到`CUDA VERSION = 11.7`但这个好像不是指当前CUDA版本  
利用`nvcc -V`命令查看当前使用的cuda版本，看到确实是11.0  
附：[windows下切换不同的cuda版本](https://blog.csdn.net/sinat_38132146/article/details/106252877)  
  a. FAILED: voxelize_cuda.cuda.o  
  
  b. nvcc fatal : unsupported gpu architecture 'compute_86':  
  不支持86算力，[查了一下](https://blog.csdn.net/qq_31347869/article/details/123348901)发现是因为显卡的算力很高（RTX3090），但cuda11.0版本不支持这么高的算力，需要在代码里调整一下(？) 
  
  c. subprocess.CalledProcessError: Command '['ninja', '-v']' returned non-zero exit status 1.  
  
  d. RuntimeError: Error building extension 'voxelize_cuda'  
