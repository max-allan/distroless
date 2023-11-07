FROM redhat/ubi9 as build
RUN dnf -y install --installroot /foo git


FROM scratch
COPY --from=build /foo /
COPY --from=build /tmp /workspace
WORKDIR /workspace
ENTRYPOINT ["/usr/bin/git"]
