FROM python:3.8-slim 

WORKDIR /home/

COPY requirements_docker.txt ./
RUN apt-get update
RUN pip install --upgrade pip
RUN pip install setuptools
RUN pip install --no-cache-dir -r requirements_docker.txt 
RUN pip install torch-scatter  torch-sparse -f https://data.pyg.org/whl/torch-1.12.0+cpu.html
RUN pip install torch-cluster -f https://data.pyg.org/whl/torch-1.12.0+cpu.html
COPY . .
#New Portion
RUN pip install -e esm/.



CMD ["bash"]
