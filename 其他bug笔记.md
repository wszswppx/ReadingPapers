  
  也是一些bug和解决方法，但不是项目过程中的。
  
## 1. 在Windows下使用pip  
遇到了
```
 pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
```
**解决方法**：不要用cmd，用powershell即可。

## 2. 在Windows下运行程序试图读取pkl文件时  
报错：
```
pickle.UnpicklingError: the STRING opcode argument must be quoted
```
**可能原因①**：在Windows系统下读取linux文件内容，Windows系统的文件是dos格式的， 而linux系统的文件是unix格式的，故他们的换行符表示方法不一致。  
|             | Windows     |    Linux      |
| :---        |    :----:   |        :---:  |
| 文件系统     | DOS         | UNIX          |
| 换行符       | \r\n        | \n            |

**解决方法**：在linux系统中有命令`dos2unix`和`unix2dos`来实现两种文件的转换，而Windows中可以[通过在BASTET.COM下载同款](http://www.bastet.com/)来实现。  
**结果**：在转换前后文件的大小没变、修改日期也没变，看起来没有任何变化。且bug继续存在。  

**可能原因②**：试图使用python3读取python2生成的pkl文件。（可是那些项目不也是这样？）  
**解决方法**在[CSDN上找到一个帖子](https://blog.csdn.net/palpiter/article/details/118862034)给的方法：
```
import pickle
with open(filePath, "rb") as input_file:
  content = pickle.load(input_file, encoding="latin1")
```
但在实际项目中感觉无从改起，难道要直接去site-package里的包？【待定】总之感觉就是这个原因了（或者①②都有）。

## 3. vscode连接服务器ssh时无限重复输入密码  
原因：上次退出状态异常
解决：先不要连ssh，在本地vscode菜单栏中点击`查看(view)-命令面板(command palette)`，或直接使用快捷键`ctrl+shift+P`  
![image](https://user-images.githubusercontent.com/32038518/197325195-89b64b06-28f8-48d9-b7fe-acdfb1be6763.png)  
执行：
```
kill VS Code Server on Host...
```
然后再重新连接，此时服务器端会重新下载一些依赖，等待后即可。  

## 4. 使用Jupyter时反复出现kernel died  
**可能原因**由于python和torch版本不对应导致的。  
**解决方法①**更新torch版本使其对应，但搞不好要配半天；  
**解决方法②**（来自[百度经验](https://jingyan.baidu.com/article/ca00d56c720efea89febcf46.html)）在代码开头加入：  
```
import os
os.environ['KMP_DUPLICATE_LIB_OK']='True'
```
## 4. 安装nerfacc  
```
# 执行 
pip install nerfacc

# 安装后
Successfully installed commonmark-0.9.1 nerfacc-0.3.1 ninja-1.11.1 pybind11-2.10.1 rich-12.6.0
```

## 5. 使用pip安装torch时提示：ReadTimeoutError: HTTPSConnectionPool(host='files.pythonhosted.org', port=443): Read timed out. 
**原因**：文件较大，连接maybe不太稳定，需要设置较大的超时阈值（默认是15s）  
**解决**：在pip时加入`--defalut-timeout=1688`即可，如：  
```
# 安装torch==1.11+cu113
pip --default-timeout=1688 install torch==1.11.0+cu113 torchvision==0.12.0+cu113 torchaudio==0.11.0 --extra-index-url https://download.pytorch.org/whl/cu113
```
不得不提的一点：torch和CUDA版本一致的要求很强，且 torch==1.11.0 需要 python>=3.7  
安装好torch以后第一件事：  
```
python
>>>import torch
>>>print(torch.__version__)
>>>print(torch.cuda.is_available())
```
测试cuda的可用性，否则无法正常使用！  

