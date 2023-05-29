FROM debian:11-slim AS base

RUN apt-get -yqq update && \
    apt-get autoremove -y && \
    apt-get clean -y

FROM base AS build

FROM base
