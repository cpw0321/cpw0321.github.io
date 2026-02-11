# PyTorch

## 1. Anaconda
环境配置与包管理

安装：https://docs.anaconda.com/anaconda/install/windows/
会发送下载链接到邮箱

## 2. pytorch

安装： https://pytorch.org/get-started/locally/
选择好相应的gpu对应的cuda版本

conda create -n pytorch_cpu // 创建虚拟环境
conda info -a // 列出当前环境有哪些虚拟环境
conda activate pytorch_cpu // 激活环境


还有一种虚拟环境利用原生的venv
```bash
# 创建
# Python 3.x 内置 venv 模块
python3 -m venv myenv

# 激活
# windows:
myenv\Scripts\activate
# linux:
source myenv/bin/activate

# 停用
deactivate
```