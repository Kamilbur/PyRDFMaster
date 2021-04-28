FROM root_lambda:latest

SHELL ["/bin/bash", "-c"]
ARG IPYTHON_DEFAULT_PORT=8888
ENV IPYTHON_DEFAULT_PORT=${IPYTHON_DEFAULT_PORT}
ENV PS1='PEXPE\[\]CT_PROMPT \w>' PS2='>' PROMPT_COMMAND=''

RUN yum -y update

ENV PYTHONPATH=/mnt/cern_root/root_install/PyRDF:${PYTHONPATH}

# 
#
#             TODO: Przetestować, czy da się inaczej
#
RUN echo "source /mnt/cern_root/root_install/bin/thisroot.sh" >> ~/.bashrc

RUN python3 -m pip install --upgrade pip --prefix=/mnt/cern_root/chroot &&\
    python3 -m pip install jupyterlab metakernel --user &&\
    python3 -m pip install setuptools --upgrade

RUN cd /mnt/cern_root/root_install/PyRDF/PyRDF && git stash && git fetch && git checkout AWS-backend && git pull && git stash pop

# Add edited demo
ADD ./files/df102_NanoAODDimuonAnalysis.ipynb /mnt/cern_root/root_install/PyRDF/demos

#RUN sed -i '67,84s/^/#/ ; 118s/ssm.*//' /mnt/cern_root/root_install/PyRDF/PyRDF/backend/AWS.py
ADD ./files/AWS.py /mnt/cern_root/root_install/PyRDF/PyRDF/backend

# Root lambda 2

RUN python3 -m pip install jupyter_contrib_nbextensions --user && ~/.local/bin/jupyter contrib nbextension install
RUN ln /root/.local/lib/python3.7/site-packages/jupyter_contrib_nbextensions/nbextensions/ /mnt/cern_root/root_install/PyRDF/nbextensions-link -s

ENV PATH=/root/.jupyter/:${PATH}
RUN mkdir -p /root/.jupyter

RUN mkdir -p /usr/local/lib/nodejs/ && cd /usr/local/lib/nodejs && \
    curl https://nodejs.org/dist/v14.17.0/node-v14.17.0-linux-x64.tar.xz -o node.tar.xz && \
    tar -xf node.tar.xz

ENV PATH=/usr/local/lib/nodejs/node-v14.17.0-linux-x64/bin:${PATH}

ENV PATH=/root/.local/bin/:${PATH}

RUN cd /root && git clone https://github.com/Tequilac/AWSConnector.git && cd AWSConnector && source install.sh


#
#           TODO: przerobić to tak, aby puszczało jupyter-laba
#

EXPOSE 8888
CMD ["/usr/bin/env", "bash"]
