FROM us.gcr.io/broad-dsp-gcr-public/terra-jupyter-base:0.0.6
USER root
# RUN mkdir /tmp/numba_cache & chmod 777 /tmp/numba_cache & NUMBA_CACHE_DIR=/tmp/numba_cache#
#this makes it so pip runs as root, not the user
ENV PIP_USER=false

RUN apt-get update &&  apt-get upgrade -y && \
  apt-get install -yq --no-install-recommends \
  libxml2-dev \
  libxslt-dev \
  python3.7-dev \
  python-tk \
  tk-dev \
  libssl-dev \
  xz-utils \
  libhdf5-dev \
  openssl \
  make \
  liblzo2-dev \
  zlib1g-dev \ 
  libz-dev \
  g++ \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
  
#  && chown -R $USER:users /home/jupyter-user/.local
RUN pip3 -V \
 && pip3 install --upgrade pip \
 && chown -R $USER:users /home/jupyter-user/.local \
 && pip3 install cmake \
#  && pip3 install mkl==2019.0 \
#  && pip3 install intel-openmp==2019.0 \
 && pip3 install fa2 mnnpy MulticoreTSNE plotly \
 && pip3 install numpy==1.15.2 \
 && pip3 install py4j==0.10.7 \
 && python3 -mpip install matplotlib==3.0.0 \
 && pip3 install pandas==0.23.4 \
 && pip3 install pandas-gbq==0.11.0 \
 && pip3 install seaborn==0.9.0 \
 && pip3 install notebook==5.7.8 \
 && pip3 install python-lzo==1.12 \
 && pip3 install google-cloud-bigquery==1.23.1 \
 && pip3 install google-api-core==1.6.0 \
 && pip3 install google-cloud-bigquery-datatransfer==0.4.1 \
 && pip3 install google-cloud-datastore==1.10.0 \
 && pip3 install google-cloud-resource-manager==0.30.0 \
 && pip3 install google-cloud-storage==1.23.0 \
 && pip3 install --ignore-installed firecloud==0.16.25 \
 && pip3 install scikit-learn==0.20.0 \
 && pip3 install statsmodels==0.9.0 \
 && pip3 install ggplot==0.11.5 \
 && sed -i 's/pandas.lib/pandas/g' /usr/local/lib/python3.7/dist-packages/ggplot/stats/smoothers.py \
 && pip3 install bokeh==1.0.0 \
 && pip3 install pyfasta==0.5.2 \
 && pip3 install markdown==2.4.1 \
 && pip3 install pdoc==0.3.2 \
 && pip3 install biopython==1.72 \
 && pip3 install bx-python==0.8.2 \
 && pip3 install fastinterval==0.1.1 \
 && pip3 install matplotlib-venn==0.11.5 \
 && pip3 install bleach==1.5.0 \
 && pip3 install cycler==0.10.0 \
 && pip3 install enum34==1.1.6 \
 && pip3 install h5py==2.7.1 \
 && pip3 install html5lib==0.9999999 \
 && pip3 install joblib==0.11 \
 && pip3 install keras==2.1.6 \
 && pip3 install patsy==0.4.1 \
 && pip3 install protobuf==3.7.1 \
 && pip3 install pymc3==3.1 \
 && pip3 install pyparsing==2.2.0 \
 && pip3 install pysam==0.15.1 \
 && pip3 install python-dateutil==2.6.1 \
 && pip3 install pytz==2017.3 \
 && pip3 install pyvcf==0.6.8 \
 && pip3 install pyyaml==3.12 \
 && pip3 install scipy==1.2 \
 && pip3 install six==1.11.0 \
 && pip3 install tensorflow==2.0.0a0 \
 && pip3 install theano==0.9.0 \
 && pip3 install tqdm==4.19.4 \
 && pip3 install werkzeug==0.12.2 \
 && pip3 install certifi==2017.4.17 \
#  && pip3 install mkl==2018.0.3 \
 && pip3 install readline==6.2 \
 && pip3 install setuptools==36.4.0 \
 && pip3 install wheel \
 && pip3 install simpleSynapse 

ENV USER jupyter-user
USER $USER
#we want pip to install into the user's dir when the notebook is running
ENV PIP_USER=true

RUN pip3 install scanpy[louvain] \
&& pip3 install leidenalg \
&& pip3 install bbknn

# Note: this entrypoint is provided for running Jupyter independently of Leonardo.
# When Leonardo deploys this image onto a cluster, the entrypoint is overwritten to enable
# additional setup inside the container before execution.  Jupyter execution occurs when the
# init-actions.sh script uses 'docker exec' to call run-jupyter.sh.
ENTRYPOINT ["/usr/local/bin/jupyter", "notebook"]

USER root
COPY scripts $JUPYTER_HOME/scripts

RUN find $JUPYTER_HOME/scripts -name '*.sh' -type f | xargs chmod +x

RUN apt-get update \
	&& apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9 \
	&& add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/' \
	&& apt-get install -yq --no-install-recommends apt-transport-https \
	&& apt update \
	&& apt install -yq --no-install-recommends \
	apt-utils \
	sudo \
	curl \
	zfs-initramfs \
	libudunits2-dev \
	libssh2-1-dev \
	libgdal-dev \
	libgeos-dev \
	libproj-dev \
	libcurl4-gnutls-dev \
	libgit2-dev \
	gfortran-6 \
	r-base \
	r-base-dev \
	r-base-core \
	r-recommended \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/* \
	&& ln -s /usr/lib/x86_64-linux-gnu/libgfortran.so.3 /usr/lib/x86_64-linux-gnu/libgfortran.so

USER $USER

# ## Reasons for change, 1. Message during installation, 2. User cannot
# ## update packages installed in the Dockerfile.

RUN  mkdir -p /home/jupyter-user/.rpackages \
 && echo "R_LIBS=/home/jupyter-user/.rpackages" > /home/jupyter-user/.Renviron \
 && R -e 'install.packages(c("sf", "remotes", "BiocManager", "psych", "optparse", "devtools"), dependencies=TRUE)' \
 && R -e 'install.packages("https://bioconductor.org/packages/3.8/bioc/src/contrib/HDF5Array_1.10.1.tar.gz",repos=NULL,type="source")' \
#  && R -e 'BiocManager::install(version="3.9")' \
 && R -e 'BiocManager::install(c( \
 # Jupyter notebook essentials
 "IRdisplay",  \
 "DataBiosphere/Ronaldo", \
 "IRkernel/IRkernel", \
 # GCP essentials
 "bigrquery",  \
 "googleCloudStorageR", \
 # User oriented packages
 "tidyverse", \
 "pbdZMQ", \
 "uuid", \
 # Installing pathway analysis tools
 "clusterProfiler", \
 "enrichR", \
 "msigdbr", \
 # Gene mapping module
 "org.Hs.eg.db"))' \

 # Install monocle 3
 && R -e 'BiocManager::install(c( "minfi", "BiocGenerics", "DelayedArray", "DelayedMatrixStats", "limma", "S4Vectors", "SingleCellExperiment", "SummarizedExperiment", "batchelor"))' \
 && R -e 'devtools::install_github("cole-trapnell-lab/leidenbase")' \
 && R -e 'devtools::install_github("cole-trapnell-lab/monocle3")' \
#  && R -e 'install.packages("Seurat")' \
 && R -e 'IRkernel::installspec()' \
 && chown -R $USER:users /home/jupyter-user/.local

 RUN pip3 install anndata2ri

