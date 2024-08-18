+++
author = "小牛快跑"
title = "Microsoft & Nvidia AI Agent 训练营学习 - Part 1"
date = "2024-8-18"
description = "演示"
tags = [
    "LLM",
    "AI",
    "Nvidia",
    "Microsoft"
]

+++

### NVIDIA AI-AGENT夏季训练营 - Part 1

- 项目名称：AI-AGENT夏季训练营 — RAG智能对话机器人
- 报告日期：2024年8月18日
- 项目负责人：小牛快跑

### 项目概述

[在这部分介绍项目的整体情况，包括项目的应用场景与亮点]

### 技术方案与实施步骤
模型选择（必写）： 详细描述项目采用的技术方案，包括大模型的选择理由、RAG模型的优势分析。

数据的构建（必写）： 说明数据构建过程、向量化处理方法及其优势。

功能整合（进阶版RAG必填）：  介绍进阶的语音功能、Agent功能、多模态等功能的整合策略与实现方法。
实施步骤：
#### 环境搭建： 
```Dockerfile
FROM continuumio/miniconda3

# 清空原有的源地址，并添加南京大学镜像源
RUN sed -i 's#deb.debian.org/debian$#mirrors.nju.edu.cn/debian#' /etc/apt/sources.list.d/debian.sources && \
    sed -i 's#deb.debian.org/debian-security$#mirrors.nju.edu.cn/debian-security#' /etc/apt/sources.list.d/debian.sources
RUN apt-get update && \
    apt-get install -y wget curl && \
    rm -rf /var/lib/apt/lists/*

# 设置工作目录
WORKDIR /app

# 创建 Conda 环境并安装所有必要的包
RUN conda create --name ai_endpoint python=3.8 -y
RUN conda install --yes -n ai_endpoint -c conda-forge \
        jupyterlab \
        jupyterlab_code_formatter \
        langchain-core \
        langchain \
        matplotlib \
        numpy \
        faiss-cpu=1.7.2 \
        openai \
        langchain-community

RUN conda install --yes -n ai_endpoint -c conda-forge black \
        yapf \
        isort \
        autopep8
RUN conda run -n ai_endpoint pip install \
        langchain-nvidia-ai-endpoints

#dotnet-sdk
RUN conda install --yes -n ai_endpoint -c conda-forge dotnet 

# RUN curl -sSL https://dot.net/v1/dotnet-install.sh | bash

EXPOSE 8888

# 运行 Jupyter Notebook
CMD ["conda", "run", "-n", "ai_endpoint", "jupyter", "lab", "--ip=0.0.0.0", "--no-browser", "--allow-root", "--NotebookApp.token=''", "--notebook-dir=/app"]
```
代码实现（必写）： 列出关键代码的实现步骤，可附上关键代码截图或代码块。
测试与调优： 描述测试过程，包括测试用例的设计、执行及性能调优。
集成与部署： 说明各模块集成方法及最终部署到实际运行环境的步骤。


### 项目成果与展示
应用场景展示(必写)： 描述对话机器人的具体应用场景，如客户服务、教育辅导等。
功能演示（必写）： 列出并展示实现的主要功能，附上UI页面截图，直观展示项目成果。

### 问题与解决方案
问题分析： 详细描述在项目实施过程中遇到的主要问题。
解决措施： 阐述针对每个问题采取的具体解决措施及心路历程，体现问题解决能力。


### 项目总结与展望
项目评估： 对项目的整体表现进行客观评估，总结成功点和存在的不足。
未来方向： 基于项目经验，提出未来可能的改进方向和发展规划。


### 附件与参考资料