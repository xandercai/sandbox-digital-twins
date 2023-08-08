FROM mambaorg/micromamba:git-7545124-jammy as sandbox-conda

USER root
COPY environment.yml /conf/
RUN micromamba create  -y -p /env -f /conf/environment.yml && \
    micromamba clean --all --yes && \
    micromamba env export -p /env --explicit

ARG MAMBA_DOCKERFILE_ACTIVATE=1

FROM ubuntu:jammy-20220815

ARG nb_user=jovyan
ARG nb_uid=1000
ARG nb_gid=100

RUN useradd -l -m -s /bin/bash -N -g $nb_gid -u $nb_uid $nb_user

COPY --chown=$nb_uid:$nb_gid --from=sandbox-conda /env /env

ENV LC_ALL=C.UTF-8 \
    SHELL=bash \
    GDAL_DRIVER_PATH=/env/lib/gdalplugins \
    PROJ_LIB=/env/share/proj \
    GDAL_DATA=/env/share/gdal \
    PATH=/env/bin:$PATH

USER $nb_user

WORKDIR "/home/$nb_user"

EXPOSE 5000
ENTRYPOINT ["/env/bin/tini", "--"]
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "Digital-Twins.src.app:app"]

