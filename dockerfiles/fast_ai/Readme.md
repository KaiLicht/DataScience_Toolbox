## Dockerfile for the 2018 fast.ai course
This Dockerfile uses a pre-build and GPU-enabled pytorch image from nvidia to create an isolated environment for the 2018 fast.ai course. Those images are very convenient, because they come batteries included: All CUDA libraries, dependencies and pytorch is pre-installed and ready to go. Only the fast.ai library has to be installed.

### Who should use this Dockerfile?
You don't need this images if you are using paperspace and the installation script that is provided during the course. If you are using your own PC with a CUDA enabled device I would highly recommend docker. While isolation with `venv` or `conda` isolates your python environment, it does not isolate the complex CUDA toolchain. I pretty often ended up breaking this toolchain and it can be a very hard time fixing that. Also sometimes if you want to use different stacks like keras/TensorFlow and pytorch they can have different dependencies. So, I decided to completely decouple my host system from my data-science environments. And it works great! You can read more on that [here](https://towardsdatascience.com/use-nvidia-docker-to-create-awesome-deep-learning-environments-for-r-or-python-pt-i-df8e89b43a72).

### Prerequisites
* Your host runs Linux (I would always recommend that for deep learning!)
* You have a CUDA enabled device and the drivers installed

## Install docker-ce and the nvidia runtime
The first thing is to install docker and the nvidia runtime to pass a GPU into a container:
```shell
#Install docker via get.docker script
curl -fsSL get.docker.com -o get-docker.sh
sh get-docker.sh

#Add the package repositories for nvidia docker
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \ 
    apt-key add -

curl -s -L https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64/nvidia-docker.list | \
    tee /etc/apt/sources.list.d/nvidia-docker.list

apt-get update

#Install nvidia-docker2 and reload the Docker daemon configuration 
apt-get install -y nvidia-docker2
pkill -SIGHUP dockerd
```

To test if everything is installed correctly you can run `docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi` This downloads a CUDA base image from docker hub and executes `nvidia-smi` inside a container which should display your GPU.

![nvidia-smi](https://raw.githubusercontent.com/KaiLicht/DataScience_Toolbox/master/dockerfiles/fast_ai/nvidia-smi.png)

If the installation fails and demands Ubuntu 18.04 you can maybe try [this](https://gist.github.com/Brainiarc7/a8ab5f89494d053003454efc3be2d2ef) one.

## Get a pre-build pytorch image with GPU support from nvidia
The nvidia GPU cloud is not a cloud service provider but an image registry similar to docker hub where you can download pre build images for free. The cool thing is: The developer at nvidia took care of all the necessary libraries and tools you need to have. It’s free and you can use the images for your own or commercial purposes, but you are not allowed to redistribute them. Once you are [signed up](https://ngc.nvidia.com/signup/register) select configuration in the menu on the left and generate an API-Key. With this key you can register your docker installation for the nvidia registry. You should save the key somewhere safe. Use the command `docker login nvcr.io` to register. As username you have to use `$oauthtoken` (with the $ sign!) and the API-Key as password. Now to download the pytorch image use the command: `docker pull nvcr.io/nvidia/pytorch:18.05-py3`.

## What's inside and how to build
As you can see this is a pretty short Dockerfile and that's because of the pytorch image. All the nitty-gritty stuff is already done by nvidia. We only have to install the fast.ai library and download the data. I'm going to install the fast.ai library into the container but I'm going to mount the `fast.ai/data` folder from the host. That means, that the folder `fast.ai/data` is going to be the place where you have to store all persistent data: notebooks or code files you worked on or data you downloaded to play around. Everything else is going to be deleted when a container is deleted. Note, that you can also commit changes to a container and start and stop it (like a virtual machine). I'm not doing that, because I want to keep track of the changes I make. Also I'm disabling token and password protection, because this is for local use. So when you want to remote log on your workstation please use a ssh tunnel! To build the image download the Dockerfile to a local folder (with no other content than the Dockerfile), open a terminal in this folder and use the command:
```shell
docker build -t kailicht/fastdotai .
```
This should take quite a while.

## How to use it? 
Now you should create a data folder on your **host** where you download the cats and dogs data. For example in your home directory:
```shell
cd ~
mkdir data
cd data
wget http://files.fast.ai/data/dogscats.zip
unzip -q dogscats.zipls
```

Now you can use the following command to start a container from the image. It's mounting the data folder into the container and also exposes the port 8888 for your browser. Use `localhost:8888` or the IP of your workstation. Keep in mind that only the data in `fast.ai/data` will be persistent when you start a new container!
```shell
docker run --runtime=nvidia -it --rm -v ~/data:/fastai/data -p 8888:8888 kailicht/fastdotai
```
Now let's create a `Notebooks` folder in our `data` folder to save Notebooks we create ourselves. Also we have to link the fast.ai library so that we can easily import things:
```shell
cd ~
mkdir data/Notebooks
ln -s /fastai/fastai/ fastai/data/Notebooks/fastai
```



#### Get updates
To get updates watch this repo or follow me on [twitter](https://twitter.com/kai_lichtenberg).