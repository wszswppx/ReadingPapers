# SelfRecon踩坑笔记
菜鸟试图复现开源项目SelfRecon，这是他内心的变化……
## 安装环境
1. 在执行 ` bash install.sh `
时报错：
```
RuntimeError: 
The detected CUDA version (10.2) mismatches the version that was used to compile
PyTorch (11.3). Please make sure to use the same CUDA versions.
```
似乎需要CUDA=11.3，而服务器是10.2版本。  
遂根据局部cuda安装方法：按照[普通用户安装局部cuda](https://blog.csdn.net/u012422446/article/details/104882357)  
[CUDA11.3官网](https://developer.nvidia.com/cuda-11.3.0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=18.04&target_type=runfile_local)  
但知道官网并没有什么用，还是只能通过命令下载，只不过官网能查到特定版本对应的命令：
```
wget https://developer.download.nvidia.com/compute/cuda/11.3.0/local_installers/cuda_11.3.0_465.19.01_linux.run
```
注：文件大小: 3029622552 (2.8G)  
本来试图在本地下载好再上传到服务器，但在本地使用Powershell和cmd试了两次都被报错 wget : 引发类型为“System.OutOfMemoryException”的异常;但绝不是由于pc内存不足（实验室电脑内存64G）  
没有正面解决，而是改为尝试直接在服务器的终端上执行，成功。  
在当前目录下新建文件夹，命名为 `cuda11.3` 然后执行
```
sh cuda_11.3.0_465.19.01_linux.run --silent --toolkit --toolkitpath=$HOME/cuda11.3
```
然后下载对应版本的cuDNN：[cuDNN官网](https://developer.nvidia.com/rdp/cudnn-download)，选择**CUDA 11.X**--**Local Installer for Linux x86_64 (Tar)**。  
本地下载后得到一个文件 `cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz` 是一个双层压缩的嵌套  
先把它上传到服务器（好慢，看来还是命令行直接下载到服务器比较好）  
**悲报：传到40%突然STFP连接断开，上传失败了！！**  
尝试在服务器终端运行：
```
wget https://developer.nvidia.com/compute/cudnn/secure/8.6.0/local_installers/11.8/cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz
```
但被403，因为下载cuDNN需要登录NVIDIA账户。  
2022年10月14日19:17:20 现在仍在尝试从本地上传

执行命令一次性解压：
```
tar xvJf cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz
```
