# How to build a GPU compatible DOCKER image on centos7 



##  Step 1: Install NVIDIA Container Toolkit on your OS 
This integrates into Docker Engine to automatically configure your containers for GPU support.


### Update yum repos
```

# get repo list 
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.repo | \
  sudo tee /etc/yum.repos.d/nvidia-docker.repo
  
# For pre-releases, you need to enable the experimental repos of all dependencies:
sudo yum-config-manager --enable libnvidia-container-experimental
sudo yum-config-manager --enable nvidia-container-runtime-experimental

# To later disable the experimental repos of all dependencies, you can run:
sudo yum-config-manager --disable libnvidia-container-experimental
sudo yum-config-manager --disable nvidia-container-runtime-experimental
```

### install nvidia container toolkit 
`yum install -y nvidia-docker2`

### restart docker
`sudo systemctl restart docker `



## Step 2: Test docker with gpus 

### Create a Dockerfile from the base image provided by nvidia 
```
mkdir -p test && cd test 
echo "FROM nvidia/cuda:10.2-base" >> Dockerfile_simple
echo "CMD nvidia-smi" >> Dockerfile_simple # https://towardsdatascience.com/how-to-properly-use-the-gpu-within-a-docker-container-4c699c78c6d1
```
### build the docker image 
`docker build --no-cache  --build-arg http_proxy=http://proxy.tch.harvard.edu:3128 -t nvidia-test-simple -f Dockerfile_simple .`

### test 
`docker run -it --gpus all nvidia-test-simple nvidia-smi && echo "location of nvidia-smi" && which nvidia-smi `




Sourced from: 
- https://nvidia.github.io/nvidia-docker/
- https://www.cloudsavvyit.com/14942/how-to-use-an-nvidia-gpu-with-docker-containers/
