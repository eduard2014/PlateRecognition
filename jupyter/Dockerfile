FROM nvcr.io/nvidia/tensorflow:23.01-tf2-py3

LABEL maintainer="Eduardo Arias Reyes eduardo2014@gmail.com"
LABEL desc="Base image for Deep Learning projects."

# Define Args
ARG UID
ARG GID

# Create group 'appgroup' that maps to the group id of the user running the container
# Create user 'jupyter' that maps to the id of the user running the container
# Create notebook directory
# Chown all the files to the app user.
RUN addgroup --gid $GID appgroup && \
    useradd -rm -d /home/jupyter -s /bin/bash -g appgroup -G video -u $UID --no-log-init jupyter && \
    mkdir -p /home/jupyter/notebook && \
    chown -R jupyter:appgroup /home/jupyter/notebook 

# Add Tini. Tini operates as a process subreaper for jupyter. This prevents kernel crashes.
ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/bin/tini
RUN chmod +x /usr/bin/tini

# Change dir
WORKDIR /home/jupyter/notebook

# Install nodejs and opencv dependencies
ENV DEBIAN_FRONTEND=noninteractive
RUN curl -fsSL https://deb.nodesource.com/setup_16.x | bash && \    
    apt-get update && \ 
    apt-get install -y nodejs && \
    # apt-get install -y --no-install-recommends pciutils usbutils && \
    apt-get install -y --no-install-recommends libgl1 libglib2.0-0 libsm6 libxext6 

# Switch to 'jupyter'
USER jupyter


# Open the server port
EXPOSE 8888

# Install requirements
COPY ./requirements.txt /home/jupyter/notebook/
RUN pip install --upgrade pip && \
    pip install -r requirements.txt && \
    pip install jupyterlab-spellchecker && \
    jupyter labextension install @jupyter-widgets/jupyterlab-manager && \
    jupyter labextension install @jupyterlab/toc && \
    jupyter labextension install jupyter-matplotlib 
    

ENTRYPOINT ["/usr/bin/tini", "--"]    


