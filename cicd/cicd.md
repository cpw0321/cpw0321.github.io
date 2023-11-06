# CI/CD

## 一、工具
### 1.1 gitlab安装
参考：https://www.cnblogs.com/linhaifeng/articles/16480242.html  

### 1.2 Zadig
面向开发者设计的开源、高可用CI/CD


## 二、CD
### 2.1 argocd
官网安装指南：https://argo-cd.readthedocs.io/en/stable/getting_started/  

源码：https://github.com/argoproj/argo-cd/



## 三、Makefile

```text
VERSION ?= latest
IMAGE := harbor.example.com/auth:$(VERSION)

build:
	@docker build -t $(IMAGE) .

push:
	@docker push $(IMAGE)

start:
	@docker run -d -p 8080:8080 --name auth $(IMAGE)

stop:
	@docker rm -f auth

logs:
	@docker logs -f auth --tail=200

```