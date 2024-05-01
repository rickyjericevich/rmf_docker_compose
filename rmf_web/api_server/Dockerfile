ARG IMAGE_STORE="local" # ghcr.io/open-rmf/rmf_deployment_template

FROM $IMAGE_STORE/builder-rmf-web:latest

# build the api server wheel
RUN cd /rmf_ws/src/rmf-web/packages/api-server \
    && pnpm run prepack

FROM $IMAGE_STORE/rmf:latest

ARG DB_TYPE="none" # db options are postgres, mysql & maria. Otherwise, default is in-memory

WORKDIR /rmf_ws/src

COPY --from=0 /rmf_ws/src/rmf-web/packages/api-server/dist/ .

# install the api server wheel with a supported database
RUN pip3 install $(ls -1 | grep '.*.whl')[$DB_TYPE]

ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]