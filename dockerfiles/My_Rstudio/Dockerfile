FROM kailicht/rstudio_deeplearning:latest

COPY --chown=docker LibsToInst /home/docker/LibsToInst
COPY --chown=docker PkgsToInst /home/docker/PkgsToInst
COPY --chown=docker user-settings /home/docker/.rstudio/monitored/user-settings/user-settings


RUN apt-get update \
  #fix gfortran link in /usr/lib/
  && ln -s /usr/lib/x86_64-linux-gnu/libgfortran.so.4 /usr/lib/libgfortran.so \
  #&& cat /home/docker/LibsToInst | xargs apt-get install -y 
  && cat /home/docker/PkgsToInst | xargs install.r