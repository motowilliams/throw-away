FROM debian:latest

ENV PYTHONUNBUFFERED=1

RUN apt-get update && \
    apt-get install -y -qq \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    gnupg-agent \
    lsb-release \
    python3 \
    python3-pip \
    rsync \
    software-properties-common \
    tar \
    zip

# Docker apt repo
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
RUN apt-get update && apt-get install -y docker-ce docker-ce-cli

# https://www.mkdocs.org/ https://mkdocs-macros-plugin.readthedocs.io/ https://squidfunk.github.io/mkdocs-material/
RUN pip3 install --no-cache --upgrade pip setuptools
RUN pip3 install mkdocs mkdocs-macros-plugin mkdocs-material

RUN FILE=theme.list && \
    echo 'mkdocs'      >> /$FILE && \
    echo 'readthedocs' >> /$FILE && \
    echo 'material'    >> /$FILE

# //TODO setup this repo to be a pip package
RUN cd /tmp && \
    git clone https://gitlab.com/motowilliams/mkdocs-macros-file-include.git && \
    cd mkdocs-macros-file-include && \
    python3 setup.py install && \
    cd / && \
    rm -rf /tmp/

# these used to be added as files - heredocs would be useful but we don't know
#  if the users CI / local setup will support it
RUN echo "Creating wrapper scripts"                                         && \
    SCRIPT=/usr/local/bin/build                                             && \
    echo '#!/usr/bin/env bash'                                   >> $SCRIPT && \
    echo                                                         >> $SCRIPT && \
    echo 'echo "Setting directory to $DOCS_SRC_PATH"'            >> $SCRIPT && \
    echo 'cd $DOCS_SRC_PATH'                                     >> $SCRIPT && \
    echo                                                         >> $SCRIPT && \
    echo 'echo "Building documentation site"'                    >> $SCRIPT && \
    echo 'mkdocs build'                                          >> $SCRIPT && \
    chmod 755 $SCRIPT                                                       && \
    SCRIPT=/usr/local/bin/serve                                             && \
    echo '#!/usr/bin/env bash'                                   >> $SCRIPT && \
    echo                                                         >> $SCRIPT && \
    echo 'echo "Setting directory to $DOCS_SRC_PATH"'            >> $SCRIPT && \
    echo 'cd $DOCS_SRC_PATH'                                     >> $SCRIPT && \
    echo                                                         >> $SCRIPT && \
    echo 'echo "Serving documentation site on port $LOCAL_PORT"' >> $SCRIPT && \
    echo 'mkdocs serve -v --dev-addr=0.0.0.0:$LOCAL_PORT'        >> $SCRIPT && \
    chmod 755 $SCRIPT
