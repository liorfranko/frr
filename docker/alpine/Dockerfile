FROM alpine:3.7 as source-builder
ARG commit
RUN apk add --no-cache abuild acct alpine-sdk attr autoconf automake bash \
    binutils binutils-libs bison bsd-compat-headers build-base \
    c-ares c-ares-dev ca-certificates cryptsetup-libs curl \
    device-mapper-libs expat fakeroot flex fortify-headers g++ gcc gdbm \
    git gmp isl json-c json-c-dev kmod lddtree libacl libatomic libattr \
    libblkid libburn libbz2 libc-dev libcap libcurl libedit libffi libgcc \
    libgomp libisoburn libisofs libltdl libressl libssh2 \
    libstdc++ libtool libuuid linux-headers lzip lzo m4 make mkinitfs mpc1 \
    mpfr3 mtools musl-dev ncurses-libs ncurses-terminfo ncurses-terminfo-base \
    patch pax-utils pcre perl pkgconf python2 python2-dev readline \
    readline-dev sqlite-libs squashfs-tools sudo tar texinfo xorriso xz-libs \
    groff gzip bc py-sphinx
RUN mkdir -p /src
ADD src.tar /src
RUN (cd /src && \
	./bootstrap.sh && \
	./configure \
		--enable-numeric-version \
		--with-pkg-extra-version=_git$commit && \
	make dist)
FROM alpine:3.7 as alpine-builder
RUN apk add --no-cache abuild alpine-sdk && mkdir -p /pkgs/apk
ADD alpine-build.sh /usr/bin/
ADD builder /etc/sudoers.d
COPY --from=source-builder /src/*.tar.gz /src/alpine/APKBUILD /dist/
RUN adduser -D -G abuild builder && chown -R builder /dist /pkgs
USER builder
RUN /usr/bin/alpine-build.sh
