# python

## 1. 语法
+ """ 用来表示多行字符串的语法


## 2. vscode
+ 插件 python-snippets 自动补全和联想
+ 插件Pylance 跳转定义

mac下第三方包安装路径：
/Users/用户名/Library/Python/3.12/lib/python/site-packages


项目文件夹下绑定虚拟运行环境
```bash
python3.10 -m venv .venv
```


## PyTorch学习
视频：https://www.bilibili.com/video/BV1RZ421T739?p=8&spm_id_from=pageDriver&vd_source=9a6776332c8894a5253390cd88bdf876
+ tensor 张量
+ 变量
+ nn-modle

## 1.1 Tensor
张量可以理解为，0维，1维，二维，n维向量数组

机器学习：
样本+模型 ==》 Y = WX + b  w和b即变量（参数）

+ 类型
	- 和编程的数据类型一样 
+ 创建
	- a = torch.Tensor([[1, 2], [3, 4]])
	  print(a)
	  print(a.type())
+ 属性
+ 运算
	- 加减乘除之类
	- in-place 就地操作，不允许使用临时变量
	- 广播机制 张量参数自动扩展为相同大小
+ 操作
+ numpy相互转化

## 库
+ tensorflow  google的机器学习框架
+ PyTorch facebook的机器学习框架


+ numpy 用于数值计算,处理数组和矩阵运算

## 好的网站工具
+ streamlit 可以使用python库写一些前端界面


## 虚拟环境
### 1. conda

创建虚拟环境
conda create --name myenv python=3.8

使用当前环境
conda activate myenv

退出当前环境
conda deactivate

