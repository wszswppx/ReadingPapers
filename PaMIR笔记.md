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
