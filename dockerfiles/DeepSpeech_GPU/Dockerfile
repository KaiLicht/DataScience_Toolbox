FROM nvcr.io/nvidia/tensorflow:18.02-py3

MAINTAINER Kai Lichtenberg <kai@sentin.ai>

ENV TENSORFLOW_WARPCTC_WHL_URL=https://index.taskcluster.net/v1/task/project.deepspeech.tensorflow.pip.master.cpu/artifacts/public/tensorflow_warpctc-1.3.0rc0-cp27-cp27mu-linux_x86_64.whl

WORKDIR /tmp

## Install the basic things
RUN apt-get update \
   && apt-get install -y \
      build-essential \
      libssl-dev \
      libffi-dev \
   && apt-get clean \
   && rm -rf /var/lib/apt/lists/*

## Install all the python packages
RUN pip install --upgrade pip \
   && pip install --upgrade \
      virtualenv \
      pyxdg \
      python_speech_features \
      sox \
      pandas

WORKDIR /work/

RUN git clone https://github.com/mozilla/DeepSpeech \
   && cd DeepSpeech \
   && pip install -r requirements.txt

RUN wget $NATIVE_CLIENT_URL -P /tmp \
        && cd /tmp \
        && tar -xJvf native_client.tar.xz \
        && cp -r *.so /work/DeepSpeech/native_client/

RUN export LD_LIBRARY_PATH=/work/DeepSpeech/native_client

RUN pip install $DEEPSPEECH_WHL_URL

WORKDIR DeepSpeech