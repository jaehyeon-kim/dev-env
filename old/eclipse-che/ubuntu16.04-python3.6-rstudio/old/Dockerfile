FROM jaehyeon/eclipse-che:ubuntu-python3.6
MAINTAINER Jaehyeon Kim <dottami@gmail.com>

USER root

#### inatall R
# rocker/r-apt:xenial - https://github.com/rocker-org/rocker/tree/master/r-apt/xenial
# openanalytics/r-base - https://github.com/openanalytics/r-base/blob/master/Dockerfile
ARG R_VERSION
ENV R_VERSION=${R_VERSION:-3.4.3} \
    LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8 \
    TERM=xterm

RUN apt-get update\
    && apt-get install -y --no-install-recommends \
        ed \
		less \
		locales \
		vim-tiny \
		wget \
		ca-certificates \
		apt-transport-https \
		gsfonts \
	&& rm -rf /var/lib/apt/lists/*

## Configure default locale, see https://github.com/rocker-org/rocker/issues/19
RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
	&& locale-gen en_US.utf8 \
	&& /usr/sbin/update-locale LANG=en_US.UTF-8

## Use Debian unstable via pinning -- new style via APT::Default-Release
# https://github.com/openanalytics/r-base
RUN echo "deb https://cloud.r-project.org/bin/linux/ubuntu xenial/" > /etc/apt/sources.list.d/cran.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9
    
# Now install R and littler, and create a link for littler in /usr/local/bin
# Also set a default CRAN repo, and make sure littler knows about it too
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        littler \
        r-base=${R_VERSION}* \
        r-base-dev=${R_VERSION}* \
        r-recommended=${R_VERSION}* \
    && echo 'options(repos = c(CRAN = "https://cloud.r-project.org/"), download.file.method = "libcurl")' >> /etc/R/Rprofile.site \
    && echo 'source("/etc/R/Rprofile.site")' >> /etc/littler.r \
	&& ln -s /usr/share/doc/littler/examples/install.r /usr/local/bin/install.r \
	&& ln -s /usr/share/doc/littler/examples/install2.r /usr/local/bin/install2.r \
	&& ln -s /usr/share/doc/littler/examples/installGithub.r /usr/local/bin/installGithub.r \
	&& ln -s /usr/share/doc/littler/examples/testInstalled.r /usr/local/bin/testInstalled.r \
	&& install.r docopt \
	&& rm -rf /tmp/downloaded_packages/ /tmp/*.rds \
	&& rm -rf /var/lib/apt/lists/*

#### install RStudio
# rocker/rstudio - https://github.com/rocker-org/rocker-versioned/tree/master/rstudio
# QuantumObject/docker-rstudio - https://github.com/QuantumObject/docker-rstudio
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        libapparmor1 \
        libcurl4-openssl-dev \
        libedit2 \
        libssl-dev \
        lsb-release \
        psmisc

RUN RSTUDIO_LATEST=$(wget --no-check-certificate -qO- https://s3.amazonaws.com/rstudio-server/current.ver) \
    && [ -z "$RSTUDIO_VERSION" ] && RSTUDIO_VERSION=$RSTUDIO_LATEST || true \
    && wget http://download2.rstudio.org/rstudio-server-${RSTUDIO_VERSION}-amd64.deb \
    && dpkg -i rstudio-server-${RSTUDIO_VERSION}-amd64.deb \
    && rm rstudio-server-*-amd64.deb
    
RUN ## Symlink pandoc & standard pandoc templates for use system-wide
    ln -s /usr/lib/rstudio-server/bin/pandoc/pandoc /usr/local/bin \
    && ln -s /usr/lib/rstudio-server/bin/pandoc/pandoc-citeproc /usr/local/bin \
    && git clone https://github.com/jgm/pandoc-templates \
    && mkdir -p /opt/pandoc/templates \
    && cp -r pandoc-templates*/* /opt/pandoc/templates && rm -rf pandoc-templates* \
    && mkdir /root/.pandoc && ln -s /opt/pandoc/templates /root/.pandoc/templates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/ \
    ## RStudio wants an /etc/R, will populate from $R_HOME/etc
    && mkdir -p /etc/R \
    ## Write config files in $R_HOME/etc
    && echo '\n\
        \n# Configure httr to perform out-of-band authentication if HTTR_LOCALHOST \
        \n# is not set since a redirect to localhost may not work depending upon \
        \n# where this Docker container is running. \
        \nif(is.na(Sys.getenv("HTTR_LOCALHOST", unset=NA))) { \
        \n  options(httr_oob_default = TRUE) \
        \n}' >> /usr/local/lib/R/etc/Rprofile.site \
    && echo "PATH=${PATH}" >> /usr/local/lib/R/etc/Renviron \
    ## Need to configure non-root user for RStudio
    && useradd rstudio \
    && echo "rstudio:rstudio" | chpasswd \
    && mkdir /home/rstudio \
    && chown rstudio:rstudio /home/rstudio \
    && addgroup rstudio staff \
    ## Prevent rstudio from deciding to use /usr/bin/R if a user apt-get installs a package
    && echo 'rsession-which-r=/usr/local/bin/R' >> /etc/rstudio/rserver.conf \ 
    ## use more robust file locking to avoid errors when using shared volumes:
    && echo 'lock-type=advisory' >> /etc/rstudio/file-locks \
    ## configure git not to request password each time 
    && git config --system credential.helper 'cache --timeout=3600' \
    && git config --system push.default simple

USER user

RUN apt-get install -y gdebi-core \
        libapparmor1 \
        libcurl4-openssl-dev \
        libssl1.0.0 \
        psmisc

RUN RSTUDIO_LATEST=$(wget --no-check-certificate -qO- https://s3.amazonaws.com/rstudio-server/current.ver) \
    && [ -z "$RSTUDIO_VERSION" ] && RSTUDIO_VERSION=$RSTUDIO_LATEST || true \
    && wget http://download2.rstudio.org/rstudio-server-${RSTUDIO_VERSION}-amd64.deb \
    && gdebi -n rstudio-server-${RSTUDIO_VERSION}-amd64.deb \
    && rm rstudio-server-*-amd64.deb