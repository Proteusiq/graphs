  
FROM continuumio/miniconda3:latest

# installing via pip
ADD requirements.txt /tmp

# it is prefered to install packages with conda install <PACKAGE_NAME>
RUN conda install --quiet --yes \ 
    'jupyterlab'\
    'pandas'\
    && python -m pip install -r /tmp/requirements.txt \
    && useradd -rm -d /home/${NOTEBOOK_USER:-danpra} -s /bin/bash -g root -G sudo -u 1000 ${NOTEBOOK_USER:-danpra}\
    && mkdir /home/${NOTEBOOK_USER:-danpra}/project \ 
    && chown -R ${NOTEBOOK_USER:-danpra}: /home/${NOTEBOOK_USER:-danpra}/project

WORKDIR /home/${NOTEBOOK_USER:-danpra}
USER ${NOTEBOOK_USER:-danpra}

EXPOSE 8888

CMD ["jupyter", "lab", "--no-browser", "--ip", "0.0.0.0",\
    "--allow-root","--notebook-dir=./project" \
    ]