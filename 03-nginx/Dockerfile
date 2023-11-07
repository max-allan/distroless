FROM redhat/ubi9 as build
RUN dnf -y install --installroot /foo --setopt install_weak_deps=false --nodocs -y nginx

FROM scratch
COPY --from=build /foo /
COPY --from=build /tmp /workspace
USER root
ENTRYPOINT  nginx -c /etc/nginx/nginx.conf -g 'daemon off;'
