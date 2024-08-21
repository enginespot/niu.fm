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
# RUN conda create --name ai_endpoint python=3.8 -y
RUN conda create --name ai_endpoint -y
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
    langchain-nvidia-ai-endpoints edge-tts
RUN conda run -n ai_endpoint pip install \
    openai-whisper==20231117

RUN conda install --yes -n ai_endpoint -c conda-forge \
    widgetsnbextension \
    ipywidgets

# dotnet support
ENV PATH="${PATH}:/root/.dotnet/tools"
RUN wget https://packages.microsoft.com/config/debian/12/packages-microsoft-prod.deb -O packages-microsoft-prod.deb && \
    dpkg -i packages-microsoft-prod.deb && \
    rm packages-microsoft-prod.deb && \
    apt-get update && \
    apt-get install -y dotnet-sdk-8.0
RUN dotnet tool install -g Microsoft.dotnet-interactive
RUN conda run -n ai_endpoint dotnet interactive jupyter install

# 因为.net 工程需要默认安装一些软件包，这里是提前把相关的nuget包安装到全局缓存中，后续其它工程如果需要用到这个包，就不需要额外下载了
# 比如 代码块 #r "nuget: Microsoft.SemanticKernel, *-*" 就不需要额外下载
# NUGET 软件包下载需要在某个project目录下，所以需要先创建一个console的工程
RUN dotnet new console -o temp && \
    cd temp && \
    dotnet add package Newtonsoft.Json && \
    dotnet add package Microsoft.SemanticKernel -v *-* && \
    cd .. && \
    rm -rf temp

EXPOSE 8888

# 运行 Jupyter Notebook
CMD ["conda", "run", "-n", "ai_endpoint", "jupyter", "lab", "--ip=0.0.0.0", "--no-browser", "--allow-root", "--NotebookApp.token=''", "--notebook-dir=/app"]