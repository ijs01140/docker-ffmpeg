FROM debian:11-slim AS base

RUN apt-get -yqq update && \
    apt-get install -y \
        ca-certificates wget \
        nasm \
        libass-dev \
        libfreetype6-dev \
        libmp3lame-dev \
        libx264-dev \
        libx265-dev \
        libnuma-dev \
        libvpx-dev \
        libopus-dev \
        libdav1d-dev \
        libvorbis-dev && \
    apt-get autoremove -y && \
    apt-get clean -y

FROM base AS build

RUN buildDeps="autoconf \
        automake \
        cmake \
        curl \
        bzip2 \
        libexpat1-dev \
        g++ \
        gcc \
        git \
        gperf \
        libtool \
        make \
        meson \
        nasm \
        perl \
        pkg-config \
        python \
        libssl-dev \
        yasm \
        zlib1g-dev" && \
    #apt-get -yqq update && \
    apt-get install -yq --no-install-recommends ${buildDeps}

RUN cd /tmp && \
    git -C SVT-AV1 pull 2> /dev/null || git clone https://gitlab.com/AOMediaCodec/SVT-AV1.git && \
    mkdir -p SVT-AV1/build && \
    cd SVT-AV1/build && \
    cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release -DBUILD_DEC=OFF -DBUILD_SHARED_LIBS=OFF .. && \
    make -j$(nproc) && \
    make install

RUN mkdir /tmp/ffmpeg_sources && \
    cd /tmp/ffmpeg_sources && \
    wget -O ffmpeg-snapshot.tar.bz2 https://ffmpeg.org/releases/ffmpeg-snapshot.tar.bz2 && \
    tar xjvf ffmpeg-snapshot.tar.bz2 && \
    cd ffmpeg && \
    ./configure \
        --extra-libs="-lpthread -lm" \
        --ld="g++" \
        --disable-debug \
        --disable-doc \
        --disable-ffplay \
        --enable-gpl \
        --enable-openssl \
        --enable-libass \
        --enable-libfreetype \
        --enable-libmp3lame \
        --enable-libopus \
        --enable-libdav1d \
        --enable-libsvtav1 \
        --enable-libvorbis \
        --enable-libvpx \
        --enable-libx264 \
        --enable-libx265 \
        --enable-nonfree && \
    make -j$(nproc) && \
    make install && \
    hash -r

FROM base

COPY --from=build /usr/local /usr/local/