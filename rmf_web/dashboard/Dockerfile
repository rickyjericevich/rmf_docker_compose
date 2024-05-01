ARG IMAGE_STORE="local" # ghcr.io/open-rmf/rmf_deployment_template

FROM $IMAGE_STORE/builder-rmf-web:latest

# build the dashboard html
ARG DOMAIN_URL="localhost" # "rmf-deployment-template.open-rmf.org"
ARG REACT_APP_TRAJECTORY_SERVER # eg: "wss://${DOMAIN_URL}/trajectory"
ARG REACT_APP_RMF_SERVER # eg: "https://${DOMAIN_URL}/rmf/api/v1"
ARG REACT_APP_AUTH_PROVIDER="stub" # one of [stub, keycloak]
ARG REACT_APP_KEYCLOAK_CONFIG= # if REACT_APP_AUTH_PROVIDER=stub: leave empty. If REACT_APP_AUTH_PROVIDER=keycloak:  a json string with the keys realm, clientId and url eg: '{"realm": "rmf-web", "clientId": "dashboard", "url" : "https://'${DOMAIN_URL}'/auth"}'

RUN cd /rmf_ws/src/rmf-web && pnpm run build --filter rmf-dashboard...

FROM $IMAGE_STORE/rmf:latest

WORKDIR /rmf_ws/src

COPY --from=0 /rmf_ws/src/rmf-web/packages/dashboard/build ./dashboard

ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]