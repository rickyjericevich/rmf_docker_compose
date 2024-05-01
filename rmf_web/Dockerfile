ARG IMAGE_STORE="local" # ghcr.io/open-rmf/rmf_deployment_template

FROM $IMAGE_STORE/rmf:latest

RUN curl -fsSL https://get.pnpm.io/install.sh | bash -

# these 2 env commands are needed to make the pnpm command available in the shell.
# See RMF's original rmf-web Dockerfile: https://github.com/open-rmf/rmf_deployment_template/blob/9f8a9abbdb491a6d9953817f7c3e30237efbd88d/rmf-web/builder-rmf-web.Dockerfile#L21
# as well as this github issue answer: https://github.com/pnpm/pnpm/issues/4495#issuecomment-1317831712
ENV PNPM_HOME /root/.local/share/pnpm
ENV PATH $PNPM_HOME:$PATH

RUN pnpm env use --global 20 \
    && pip3 install pipenv \
    && apt install python3-venv -y

WORKDIR /rmf_ws/src

RUN git clone https://github.com/open-rmf/rmf-web.git \
    && cd /rmf_ws/src/rmf-web \
    && pnpm install

ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]