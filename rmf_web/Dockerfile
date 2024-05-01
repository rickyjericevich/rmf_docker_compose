ARG IMAGE_STORE="local" # ghcr.io/open-rmf/rmf_deployment_template

FROM $IMAGE_STORE/rmf:latest

RUN curl -fsSL https://get.pnpm.io/install.sh | bash -

# these 2 env commands are needed to make the pnpm command available in the shell.
# See RMF's original rmf-web Dockerfile: https://github.com/open-rmf/rmf_deployment_template/blob/9f8a9abbdb491a6d9953817f7c3e30237efbd88d/rmf-web/builder-rmf-web.Dockerfile#L21
# as well as this github issue answer: https://github.com/pnpm/pnpm/issues/4495#issuecomment-1317831712
ENV PNPM_HOME /root/.local/share/pnpm
ENV PATH $PNPM_HOME:$PATH

RUN pnpm env use --global 20

RUN pip3 install pipenv

RUN apt install python3-venv -y

WORKDIR /rmf_ws/src

RUN git clone https://github.com/open-rmf/rmf-web.git

# build the api server wheel
RUN cd /rmf_ws/src/rmf-web \
    && pnpm install -w --filter api-server... \
    && cd packages/api-server \
    && pnpm run prepack

# build the dashboard html
ARG DOMAIN_URL="localhost" # "rmf-deployment-template.open-rmf.org"
ARG REACT_APP_TRAJECTORY_SERVER # eg: "wss://${DOMAIN_URL}/trajectory"
ARG REACT_APP_RMF_SERVER # eg: "https://${DOMAIN_URL}/rmf/api/v1"
ARG REACT_APP_AUTH_PROVIDER="stub" # one of [stub, keycloak]
ARG REACT_APP_KEYCLOAK_CONFIG= # if REACT_APP_AUTH_PROVIDER=stub: leave empty. If REACT_APP_AUTH_PROVIDER=keycloak:  a json string with the keys realm, clientId and url eg: '{"realm": "rmf-web", "clientId": "dashboard", "url" : "https://'${DOMAIN_URL}'/auth"}'

RUN cd /rmf_ws/src/rmf-web/packages/dashboard && pnpm run build --filter rmf-dashboard...

FROM $IMAGE_STORE/rmf:latest

ARG DB_TYPE="none" # db options are postgres, mysql & maria. Otherwise, default is in-memory

WORKDIR /rmf_ws/src

COPY --from=0 /rmf_ws/src/rmf-web/packages/api-server/dist/ .

COPY --from=0 /rmf_ws/src/rmf-web/packages/dashboard/build ./dashboard

# install the api server wheel with a supported database
RUN pip3 install $(ls -1 | grep '.*.whl')[$DB_TYPE]

ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]