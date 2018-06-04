FROM nvcr.io/nvidia/pytorch:18.05-py3

LABEL maintainer="Kai Lichtenberg <kai@sentin.ai>"


RUN apt-get update && apt-get install -y --allow-downgrades --no-install-recommends \
    python-qt4 \
    libpng-dev &&\
rm -rf /var/lib/apt/lists/* &&\
mkdir /fastai

WORKDIR /fastai

RUN git clone https://github.com/fastai/fastai.git . &&\
    /opt/conda/bin/conda env create &&\
    /opt/conda/bin/conda clean -ya

ENV PATH /opt/conda/envs/fastai/bin:$PATH

RUN jupyter notebook --generate-config &&\
    echo "c.NotebookApp.token = ''" >> ~/.jupyter/jupyter_notebook_config.py &&\
    echo "c.NotebookApp.password = ''" >> ~/.jupyter/jupyter_notebook_config.py

CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--no-browser", "--allow-root"]