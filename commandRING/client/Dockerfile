FROM root_lambda:latest

SHELL ["/bin/bash", "-c"]

ENV roothome=/mnt/cern_root/root_install
ENV CHROOT=/mnt/cern_root/chroot

RUN yum -y update

ENV PYTHONPATH=${roothome}/PyRDF:${PYTHONPATH}

RUN echo "source ${roothome}/bin/thisroot.sh" >> ~/.bashrc

RUN python3 -m pip install --upgrade pip --prefix=${CHROOT} &&\
    python3 -m pip install jupyterlab metakernel --user &&\
    python3 -m pip install setuptools --upgrade

RUN cd ${roothome}/PyRDF/PyRDF && git stash && git fetch && git checkout AWS-backend && git pull && git stash pop

# Add edited demo
ADD ./files/df102_NanoAODDimuonAnalysis.ipynb ${roothome}/PyRDF/demos

#RUN sed -i '67,84s/^/#/ ; 118s/ssm.*//' ${roothome}/PyRDF/PyRDF/backend/AWS.py
ADD ./files/AWS.py ${roothome}/PyRDF/PyRDF/backend

# Root lambda 2

ENV PATH=/root/.local/bin:/usr/local/lib/nodejs/node-v14.17.0-linux-x64/bin:${PATH}

RUN python3 -m pip install jupyter_contrib_nbextensions --user && ~/.local/bin/jupyter contrib nbextension install

RUN mkdir -p /usr/local/lib/nodejs/ && cd /usr/local/lib/nodejs && \
    curl https://nodejs.org/dist/v14.17.0/node-v14.17.0-linux-x64.tar.xz -o node.tar.xz && \
    tar -xf node.tar.xz

RUN cd /root && git clone https://github.com/Tequilac/AWSConnector.git && cd AWSConnector && ./install.sh

ENV PS1='PEXPE\[\]CT_PROMPT \w>' PS2='>' PROMPT_COMMAND=''

EXPOSE 8888
CMD ["/usr/bin/env", "bash"]
