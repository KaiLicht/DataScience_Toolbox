FROM nvcr.io/nvidia/pytorch:18.05-py3

LABEL maintainer="Kai Lichtenberg <kai@sentin.ai>"


RUN apt-get update && apt-get install -y --allow-downgrades --no-install-recommends \
    python-qt4 \
	zip \
	unzip \
    libpng-dev &&\
rm -rf /var/lib/apt/lists/* &&\
mkdir /fastai

WORKDIR /fastai

RUN git clone https://github.com/fastai/fastai.git .
RUN /opt/conda/bin/conda env create
RUN /opt/conda/bin/conda clean -ya

CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--no-browser", "--allow-root"]