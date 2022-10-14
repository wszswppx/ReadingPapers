#【NeuralBody踩坑指南】
1. 在添加了 PATH=/usr/local/cuda-10.2/bin/:$PATH  后，出现 static library kineto_LIBRARY-NOTFOUND not found.  
于是通过 https://github.com/pytorch/pytorch/issues/62588 尝试解决.kineto已经添加到环境下的site-packages里，但仍然无效  
2. 偶然发现可能是spconv的问题，
根据 https://zhuanlan.zhihu.com/p/529251627 和
https://blog.csdn.net/baidu_34172099/article/details/109741821 ：
sudo apt-get install libboost-all-dev  
（zcc老师帮忙安装了libboost-all-dev）
3. 仍然有问题，查了以后发现是cudnn和cuda版本的问题，local环境似乎调用不了系统的cudnn  
查了半天，在local用户环境下安装了cuda=10.0和cudnn=7.6.4  
仍然没有用，提示新错误：找不到 #include <boost/geometry.hpp>  
查了以后说要安装 libboost-all-dev 但已经装好了啊。
4. 试图安装spconv1.2.1版本，但并没有用，遂还是安装现有版本2.1.22
找了半天，发现可以通过conda boost安装local的boost
然后通过https://github.com/traveller59/spconv/issues/251
修改了spconv下的setup.py代码，终于成功
