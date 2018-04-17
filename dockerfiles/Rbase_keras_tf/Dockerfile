FROM nvcr.io/nvidia/tensorflow:18.02-py3

MAINTAINER Kai Lichtenberg <kai@sentin.ai>

#Install R-base (rocker/r-base with little modification)
#-------------------------------------------------------

# Set a default user. Available via runtime flag `--user docker` 
RUN useradd docker \
	&& mkdir /home/docker \
	&& chown docker:docker /home/docker \
	&& addgroup docker staff

RUN apt-get update \ 
	&& apt-get install -y --no-install-recommends \
		ed \
		locales \
		vim-tiny \
		fonts-texgyre \
    gnupg2 \
    libcurl4-openssl-dev \
    libssl-dev \
    libssh2-1-dev \
    sudo \
	&& rm -rf /var/lib/apt/lists/*

## Configure default locale, see https://github.com/rocker-org/rocker/issues/19
RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
	&& locale-gen en_US.utf8 \
	&& /usr/sbin/update-locale LANG=en_US.UTF-8

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8

## Use Debian unstable via pinning -- new style via APT::Default-Release and add keys
RUN echo "deb http://http.debian.net/debian sid main" > /etc/apt/sources.list.d/debian-unstable.list \
  	&& echo 'APT::Default-Release "testing";' > /etc/apt/apt.conf.d/default \  
    && gpg --keyserver pgpkeys.mit.edu --recv-key 46925553  \
    && gpg -a --export 8B48AD6246925553 | apt-key add -  
    #&& gpg --keyserver pgpkeys.mit.edu --recv-key 2B90D010 \
    #&& gpg -a --export 2B90D010 | apt-key add -

## Now install R and littler, and create a link for littler in /usr/local/bin
## Also set a default CRAN repo, and make sure littler knows about it too
RUN apt-get update \
	&& apt-get install -t unstable -y --no-install-recommends \
		littler \
    r-cran-littler \
		r-base \
		r-base-dev \
		r-recommended \
        && echo 'options(repos = c(CRAN = "https://cran.rstudio.com/"), download.file.method = "libcurl")' >> /etc/R/Rprofile.site \
        && echo 'source("/etc/R/Rprofile.site")' >> /etc/littler.r \
	&& ln -s /usr/share/doc/littler/examples/install.r /usr/local/bin/install.r \
	&& ln -s /usr/share/doc/littler/examples/install2.r /usr/local/bin/install2.r \
	&& ln -s /usr/share/doc/littler/examples/installGithub.r /usr/local/bin/installGithub.r \
	&& ln -s /usr/share/doc/littler/examples/testInstalled.r /usr/local/bin/testInstalled.r \
	&& install.r docopt \
	&& rm -rf /tmp/downloaded_packages/ /tmp/*.rds \
  && rm -rf /var/lib/apt/lists/* \
  #remove testing as Default-Release
  && rm /etc/apt/apt.conf.d/default \
  && apt-get update

#Install tensorflow and keras
#-------------------------------------------------------
ENV WORKON_HOME /home/docker/.virtualenvs

RUN install2.r devtools remotes \
  && installGithub.r rstudio/tensorflow \
  && installGithub.r rstudio/keras \
  && pip3 install virtualenv \
  && pip3 --no-cache-dir install git+git://github.com/fchollet/keras.git@2.1.5 \
  && virtualenv --system-site-packages /home/docker/.virtualenvs/r-tensorflow
