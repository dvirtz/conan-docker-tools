FROM i386/ubuntu:bionic
ENTRYPOINT ["linux32", "--"] # https://github.com/conan-io/conan-docker-tools/issues/36

LABEL maintainer="Luis Martinez de Bartolome <luism@jfrog.com>"

ARG CONAN_VERSION

ENV CMAKE_VERSION_MAJOR_MINOR=3.18 \
    CMAKE_VERSION_FULL=3.18.2 \
    CONAN_ENV_ARCH=x86 \
    PYENV_ROOT=/opt/pyenv \
    PATH=/opt/pyenv/shims:${PATH} \
    CXX=/usr/bin/g++ \
    CC=/usr/bin/gcc

RUN apt-get -qq update \
    && apt-get -qq install -y --no-install-recommends --no-install-suggests \
       sudo=1.* \
       binutils=2.* \
       wget=1.* \
       git=1:2.* \
       libc6-dev=2.* \
       linux-libc-dev=4.* \
       g++-8 \
       libgmp-dev=2:6.* \
       libmpfr-dev=4.* \
       libmpc-dev=1.* \
       libc6-dev=2.* \
       nasm=2.* \
       dh-autoreconf=17 \
       ninja-build=1.* \
       libffi-dev=3.* \
       libssl-dev=1.* \
       pkg-config=0.* \
       subversion=1.* \
       zlib1g-dev=1:1.* \
       libbz2-dev=1.* \
       libsqlite3-dev=3.* \
       libreadline-dev=7.* \
       xz-utils=5.* \
       curl=7.* \
       libncurses5-dev=6.* \
       libncursesw5-dev=6.* \
       liblzma-dev=5.* \
       ca-certificates \
       autoconf-archive \
    && update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-8 100 \
    && update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++-8 100 \
    && update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 100 \
    && update-alternatives --install /usr/bin/cc cc /usr/bin/gcc-8 100 \
    && ln -s /usr/include/locale.h /usr/include/xlocale.h \
    && rm -rf /var/lib/apt/lists/* \
    && wget --no-check-certificate --quiet https://cmake.org/files/v${CMAKE_VERSION_MAJOR_MINOR}/cmake-${CMAKE_VERSION_FULL}.tar.gz \
    && tar -xzf cmake-${CMAKE_VERSION_FULL}.tar.gz \
    && cd cmake-${CMAKE_VERSION_FULL} \
    && ./bootstrap > /dev/null \
    && make -s -j`nproc` \
    && make -s install > /dev/null \
    && cd - \
    && rm -rf cmake-* \
    && groupadd 1001 -g 1001 \
    && groupadd 1000 -g 1000 \
    && groupadd 2000 -g 2000 \
    && groupadd 999 -g 999 \
    && useradd -ms /bin/bash conan -g 1001 -G 1000,2000,999 \
    && printf "conan:conan" | chpasswd \
    && adduser conan sudo \
    && printf "conan ALL= NOPASSWD: ALL\\n" >> /etc/sudoers \
    && wget --no-check-certificate --quiet -O /tmp/pyenv-installer https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer \
    && chmod +x /tmp/pyenv-installer \
    && /tmp/pyenv-installer \
    && rm /tmp/pyenv-installer \
    && update-alternatives --install /usr/bin/pyenv pyenv /opt/pyenv/bin/pyenv 100 \
    && PYTHON_CONFIGURE_OPTS="--enable-shared" pyenv install 3.7.5 \
    && pyenv global 3.7.5 \
    && pip install -q --upgrade --no-cache-dir pip \
    && pip install -q --no-cache-dir conan==${CONAN_VERSION} conan-package-tools \
    && chown -R conan:1001 /opt/pyenv \
    # remove all __pycache__ directories created by pyenv
    && find /opt/pyenv -iname __pycache__ -print0 | xargs -0 rm -rf \
    && update-alternatives --install /usr/bin/python python /opt/pyenv/shims/python 100 \
    && update-alternatives --install /usr/bin/python3 python3 /opt/pyenv/shims/python3 100 \
    && update-alternatives --install /usr/bin/pip pip /opt/pyenv/shims/pip 100 \
    && update-alternatives --install /usr/bin/pip3 pip3 /opt/pyenv/shims/pip3 100

USER conan
WORKDIR /home/conan

RUN mkdir -p /home/conan/.conan \
    && printf 'eval "$(pyenv init -)"\n' >> ~/.bashrc \
    && printf 'eval "$(pyenv virtualenv-init -)"\n' >> ~/.bashrc
