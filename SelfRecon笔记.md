# SelfRecon踩坑笔记
菜鸟试图复现开源项目SelfRecon，这是他内心的变化……
## 安装环境
### **1. CUDA和cuDNN版本**  

在执行 ` bash install.sh `时报错：
```
RuntimeError: 
The detected CUDA version (10.2) mismatches the version that was used to compile
PyTorch (11.3). Please make sure to use the same CUDA versions.
```
似乎需要CUDA=11.3，而服务器是10.2版本。  
  1. 遂根据局部cuda安装方法：按照[普通用户安装局部cuda](https://blog.csdn.net/u012422446/article/details/104882357)  
[CUDA11.3官网](https://developer.nvidia.com/cuda-11.3.0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=18.04&target_type=runfile_local)  
但知道官网并没有什么用，还是只能通过命令下载，只不过官网能查到特定版本对应的命令：
```
wget https://developer.download.nvidia.com/compute/cuda/11.3.0/local_installers/cuda_11.3.0_465.19.01_linux.run
```
>注：文件大小: 3029622552 (2.8G)  

本来试图在本地下载好再上传到服务器，但在本地使用Powershell和cmd试了两次都被报错 `wget : 引发类型为“System.OutOfMemoryException”`  
但绝不是由于pc内存不足（实验室电脑内存64G），为了防止麻烦，没有正面解决，而是改为尝试直接在服务器的终端上执行，成功。  
在当前目录下新建文件夹，命名为 `cuda11.3` 然后执行
```
sh cuda_11.3.0_465.19.01_linux.run --silent --toolkit --toolkitpath=$HOME/cuda11.3
```
        
  2. 然后下载对应版本的cuDNN：[cuDNN官网](https://developer.nvidia.com/rdp/cudnn-download)，选择**CUDA 11.X**--**Local Installer for Linux x86_64 (Tar)**。  
本地下载后得到一个文件 `cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz` 是一个双层压缩的嵌套  
先把它上传到服务器（好慢，看来还是命令行直接下载到服务器比较好）  
**悲报：传到40%突然STFP连接断开，上传失败了！！**  
尝试在服务器终端运行：
```
wget https://developer.nvidia.com/compute/cudnn/secure/8.6.0/local_installers/11.8/cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz
```
但被403，因为下载cuDNN需要登录NVIDIA账户。  
2022年10月14日19:17:20 现在仍在尝试从本地上传————捷报频传：又失败了！  

  3. 经过探索发现一种方法，可以[直接从linux服务器命令行下载百度网盘文件](https://zhuanlan.zhihu.com/p/348483516)，勉强算可以用，速度500KB/s多  
  把对应的文件放在百度网盘的 `./我的应用数据/bypy/` 文件夹下即可，在终端输入 `bypy list` 即可查看bypy文件夹下的文件列表
  于是执行
  ```
  bypy downfile cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz
  ```
  最终成功下载。

  4. 执行命令一次性解压：
```
tar xvJf cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz
```
把解压出来的文件夹改名为 `cuda` （其实只是为了方便）

  5. 依次执行
```
cp cuda/include/cudnn.h $HOME/cuda11.3/include/
cp cuda/lib/libcudnn* $HOME/cuda11.3/lib64/
chmod a+r $HOME/cuda11.3/include/cudnn.h $HOME/cuda11.3/lib64/libcudnn*
```

  6. 修改 `.bashrc` 文件，在其末尾添加/修改：
```
export CUDA_HOME="$HOME/cuda11.3" 
export PATH="$PATH:$CUDA_HOME/bin" 
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$CUDA_HOME/lib64"
```

至此CUDA配置完成。  
重新开一个终端再次执行 `bash install.sh` 蹦出来一大堆warning，还有蓝有紫的，不知道算不算问题。

### **2. [Extract-normals部分](https://github.com/jby1993/SelfReconCode#extract-normals)**  
根本不知道要干嘛……好不容易做到这一步了，你跟我说你需要预先安装好[PIFuHD](https://github.com/facebookresearch/pifuhd) 和 [Lightweight Openpose](https://github.com/Daniil-Osokin/lightweight-human-pose-estimation.pytorch)……真的很无语  

把对应的项目git下来，然后把指定的文件放进去，执行
```
cd $ROOT2
python generate_boxs.py --data $ROOT/female-3-casual/imgs
cd $ROOT1
python generate_normals.py --imgpath $ROOT/female-3-casual/imgs
# 注意：这里的$ROOT需要改成自己目录，建议写成绝对路径，虽然很麻烦但比较安全省事
```
时出现各种问题，难道还要我先把前面两个project跑通？？我不能接受  
**2022年10月14日22:46:00** 今天就先到这里吧
