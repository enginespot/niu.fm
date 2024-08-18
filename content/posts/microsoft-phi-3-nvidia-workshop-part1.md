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

我作为一个新手，带着学习的态度参加训练营，希望能够对平时的工作有帮助

### 技术方案与实施步骤
模型选择： 采用教程默认的模型。

数据的构建： 采用教程默认的数据。

功能整合（进阶版RAG必填）：  介绍进阶的语音功能、Agent功能、多模态等功能的整合策略与实现方法。
实施步骤：
#### 环境搭建： 
```Dockerfile
FROM continuumio/miniconda3

# 清空原有的源地址，并添加南京大学镜像源
RUN sed -i 's#deb.debian.org/debian$#mirrors.nju.edu.cn/debian#' /etc/apt/sources.list.d/debian.sources && \
    sed -i 's#deb.debian.org/debian-security$#mirrors.nju.edu.cn/debian-security#' /etc/apt/sources.list.d/debian.sources
RUN apt-get update && \
    apt-get install -y wget \
    curl \
    ffmpeg && \
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
        langchain-community \
        gradio \
        ffmpeg \
        transformers \
        dotnet \
        black \
        yapf \
        isort \
        autopep8
RUN conda run -n ai_endpoint pip install \
        langchain-nvidia-ai-endpoints \
        edge-tts 

RUN conda run -n ai_endpoint pip install openai-whisper==20231117

# RUN curl -sSL https://dot.net/v1/dotnet-install.sh | bash

EXPOSE 8888

# 运行 Jupyter Notebook
CMD ["conda", "run", "-n", "ai_endpoint", "jupyter", "lab", "--ip=0.0.0.0", "--no-browser", "--allow-root", "--NotebookApp.token=''", "--notebook-dir=/app"]
```
- 测试与调优： 现阶段只是把一些Warning去掉，后续还会在理解代码逻辑的基础上做进一步优化。
- 集成与部署： 
    1. 涉及到隐私的变量全部丢到环境变量文件中处理
    2. 所有需要用到的软件全部放到Dockerfile中实现
    3. 学习使用了Docker Compose + Dockerfile结合的方式，未来可以无缝集成到K8S等框架中



### 项目成果与展示

![效果图](https://niu.fm/posts/image.png "Day 3 最终演示")

### 问题与解决方案
#### 问题分析
    - openai-whisper==20231117 这个包不知为何无论如何不好通过Docker构建，暂时只能手动安装，后续解决
    - Day2老师提供的在vscode中使用jupyter的方式和Day1以及Day3提供的通过web方式使用有些不是很一致，并且当开发环境切换的时候，不方便0成本切换，最好的方式是都部署到docker环境中，0成本切换，因为时间关系，暂时还没有集成netcore 开发环境到docker中

### 项目总结与展望
- 项目评估： 整个训练营学习初步能够了解到Microsoft以及Nvidia在AI方向成果，以及技术方向，很有帮助
- 未来方向： 暂无

备注：作为重度拖延症的我，也尝试在最后一天加入学习，此文作为训练营学习的Part 1 （并且此文还会再做改动），后续还会做Part 2


### 附件与参考资料