https://www.youtube.com/watch?v=Ga6kqHVKo9g

테디노트 보고 공부합니다

## 0 환경 구성
rocky 8.10

gp2t8-g1-s50(Tesla T4 GPU 2EA, GPUMemory 32GB, vCPU 8EA, Memory 40GB, [SSD]Disk 50GB)

https://guide.ncloud-docs.com/docs/server-gpuserver-vpc#gpu-%EB%93%9C%EB%9D%BC%EC%9D%B4%EB%B2%84cuda-%EC%9E%AC%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%97%85%EA%B7%B8%EB%A0%88%EC%9D%B4%EB%93%9C

init 스크립트
```sh
#!/bin/bash

# Update system packages
dnf update -y

# Install essential tools
dnf install -y curl git wget gcc make

# Install Podman
dnf install -y podman
systemctl enable podman
systemctl start podman &

# Verify Podman installation
podman --version >> /root/setup_log.txt

# Install Podman Compose
dnf install -y python3-pip
pip3 install podman-compose

# Verify Podman Compose installation
podman-compose --version >> /root/setup_log.txt

# Clone the example repository
cd /root
git clone https://github.com/teddylee777/langchain-kr.git
```


```dockerfile
# Dockerfile

# Base image
FROM python:3.11

# Set working directory
WORKDIR /app

# Install Poetry
RUN pip install --upgrade pip \
    && pip install poetry

# Add a placeholder pyproject.toml
RUN echo '[tool.poetry]\nname = "placeholder"\nversion = "0.1.0"\ndescription = "Temporary project for Docker build"\nauthors = ["Your Name <youremail@example.com>"]\n[tool.poetry.dependencies]\npython = "^3.11"\n[build-system]\nrequires = ["poetry-core>=1.0.0"]\nbuild-backend = "poetry.core.masonry.api"' > /app/pyproject.toml

# Install dependencies
RUN poetry install

# Install Jupyter and ipykernel globally
RUN pip install jupyter ipykernel \
    && python -m ipykernel install --user --name=python3.11-env

# Expose Jupyter port
EXPOSE 8888

# Command to start Jupyter Notebook
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--no-browser", "--allow-root"]
```




```sh
podman build -t python-env .

podman run -itd --name jupyter-py311 -p 8888:8888 python-env

podman logs jupyter-py311
```


```sh
# 로그에서 아래 값 복사 후 공인ip로 변경하여 접속
http://127.0.0.1:8888/tree?token=704d0d9c3qwer123asdf
```

기타명령
```
podman stop jupyter-py311
podman rm jupyter-py311
```