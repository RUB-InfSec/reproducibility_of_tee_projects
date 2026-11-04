# Gramine 1.8

We initially attempted to build and evaluate on Ubuntu 22.04 LTS (unguided scenario). However, after consulting with the developers (guided scenario), we switched to Debian 12 (Bookworm) with the backports repository.


## Reference from Docker image

1. Clone the Gramine repo and run Docker:

    ```bash
    git clone --depth 1  https://github.com/gramineproject/gramine.git
    docker run --device /dev/sgx_enclave --device /dev/sgx_provision -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket -v ~/gramine:/gramine -v ~/my-app:/app -it gramineproject/gramine
    ```

2. Build `hello_world` and extract mrenclave

    ```bash
    cd gramine/CI-Examples/helloworld
    make SGX=1
    gramine-sgx helloworld
    # Measurement:
    # 96e97ef9679d01a111c0dbb1221899e46e83584a1ed77c0ae95d331eb6caaaf7
    ```

**Reference Measurement:** `96e97ef9679d01a111c0dbb1221899e46e83584a1ed77c0ae95d331eb6caaaf7`


## Build from Source (Unguided)

1. Follow the build instructions from https://gramine.readthedocs.io/en/latest/devel/building.html

2. Build `hello_world` and extract mrenclave
   
    ```bash
    cd gramine/CI-Examples/helloworld
    make SGX=1
    gramine-sgx helloworld
    # Measurement:
    # 0589da732e5dc02a4416318ef247403e091bc4eda414942a54ef687936facad2
    ```

**Extracted Measurement:** `0589da732e5dc02a4416318ef247403e091bc4eda414942a54ef687936facad2`


### &rarr; unreproducible (unguided scenario)


## Build from Source (Guided)

We reached out to the developers regarding the discrepancies in measurement. The developers suggested building it on Debian 12 (bookworm) with backports and provided the buildinfo (attached).

<details>
<summary>Debian .buildinfo</summary>

```
Format: 1.0
Source: gramine
Binary: gramine gramine-dcap gramine-ratls-dcap gramine-ratls-epid
Architecture: all amd64 source
Version: 1.8
Checksums-Md5:
 7dc22b30077b3c8ee44bffccf1a3ddd5 1290 gramine_1.8.dsc
 2687a14b8ca16c4237d570448c1b98a1 4300 gramine-dcap_1.8_all.deb
 1fe3af71f7df75a93bc11270adbdd833 424460 gramine-ratls-dcap_1.8_amd64.deb
 034e258f65421a30713f7e3537b1729f 584008 gramine-ratls-epid_1.8_amd64.deb
 e3e9a6b61cfd014e4ddcdf9b0bd5c31d 3636036 gramine_1.8_amd64.deb
Checksums-Sha1:
 c9f10ab3f175668b6c7faceb1207cc87a5c4e47f 1290 gramine_1.8.dsc
 e6424ee92a014d01cdace78c1c64bf8086e6cfbd 4300 gramine-dcap_1.8_all.deb
 1ffc132ef1881b5dedd839d561435962b301fb35 424460 gramine-ratls-dcap_1.8_amd64.deb
 b4b04b6d748263ab032953f71c150caf713fa985 584008 gramine-ratls-epid_1.8_amd64.deb
 d3e4c8fb4c1daca19f7378b5063170723b2900d3 3636036 gramine_1.8_amd64.deb
Checksums-Sha256:
 b061ee1977688557467f81767c8441911480ddaec5f5d377850dd4ca735a2047 1290 gramine_1.8.dsc
 3069f10130ad449351d8b67b730d108aa13d7b46ecd0bbcac757c0972ac9054a 4300 gramine-dcap_1.8_all.deb
 1874c800a6eeb563801a7423189dec07c44bc49065d78c3cbb6218529006097b 424460 gramine-ratls-dcap_1.8_amd64.deb
 adf317bd7e3698f672ff174105eaee7fa2f34a35dec5c94f31d102464b8d654f 584008 gramine-ratls-epid_1.8_amd64.deb
 17dfd3fd8ee5139f9bed6b6b7e608c16eb3bfb315642484edadcf955a4c72e32 3636036 gramine_1.8_amd64.deb
Build-Origin: Debian
Build-Architecture: amd64
Build-Date: Mon, 21 Oct 2024 14:34:05 +0000
Installed-Build-Depends:
 autoconf (= 2.69-14),
 automake (= 1:1.16.3-2),
 autopoint (= 0.21-4),
 autotools-dev (= 20180224.1+nmu1),
 base-files (= 11.1+deb11u11),
 base-passwd (= 3.5.51),
 bash (= 5.1-2+deb11u1),
 binutils (= 2.35.2-2),
 binutils-common (= 2.35.2-2),
 binutils-x86-64-linux-gnu (= 2.35.2-2),
 bison (= 2:3.7.5+dfsg-1),
 bsdextrautils (= 2.36.1-8+deb11u2),
 bsdutils (= 1:2.36.1-8+deb11u2),
 build-essential (= 12.9),
 bzip2 (= 1.0.8-4),
 ca-certificates (= 20210119),
 cmake (= 3.18.4-2+deb11u1),
 cmake-data (= 3.18.4-2+deb11u1),
 coreutils (= 8.32-4+b1),
 cpp (= 4:10.2.1-1),
 cpp-10 (= 10.2.1-6),
 dash (= 0.5.11+git20200708+dd9ef66-5),
 debconf (= 1.5.77),
 debhelper (= 13.3.4),
 debianutils (= 4.11.2),
 dh-autoreconf (= 20),
 dh-strip-nondeterminism (= 1.12.0-1),
 diffutils (= 1:3.7-5),
 docutils-common (= 0.16+dfsg-4),
 doxygen (= 1.9.1-1),
 dpkg (= 1.20.13),
 dpkg-dev (= 1.20.13),
 dwz (= 0.13+20210201-1),
 file (= 1:5.39-3+deb11u1),
 findutils (= 4.8.0-1),
 fonts-font-awesome (= 5.0.10+really4.7.0~dfsg-4.1),
 fonts-lato (= 2.0-2.1),
 g++ (= 4:10.2.1-1),
 g++-10 (= 10.2.1-6),
 gawk (= 1:5.1.0-1),
 gcc (= 4:10.2.1-1),
 gcc-10 (= 10.2.1-6),
 gcc-10-base (= 10.2.1-6),
 gettext (= 0.21-4),
 gettext-base (= 0.21-4),
 grep (= 3.6-1+deb11u1),
 groff-base (= 1.22.4-6),
 gzip (= 1.10-4+deb11u1),
 hostname (= 3.23),
 init-system-helpers (= 1.60),
 intltool-debian (= 0.35.0+20060710.5),
 jq (= 1.6-2.1),
 libacl1 (= 2.2.53-10),
 libarchive-zip-perl (= 1.68-1),
 libarchive13 (= 3.4.3-2+deb11u1),
 libasan6 (= 10.2.1-6),
 libatomic1 (= 10.2.1-6),
 libattr1 (= 1:2.4.48-6),
 libaudit-common (= 1:3.0-2),
 libaudit1 (= 1:3.0-2),
 libbinutils (= 2.35.2-2),
 libblkid1 (= 2.36.1-8+deb11u2),
 libbrotli1 (= 1.0.9-2+b2),
 libbsd0 (= 0.11.3-1+deb11u1),
 libbz2-1.0 (= 1.0.8-4),
 libc-bin (= 2.31-13+deb11u11),
 libc-dev-bin (= 2.31-13+deb11u11),
 libc6 (= 2.31-13+deb11u11),
 libc6-dev (= 2.31-13+deb11u11),
 libcap-ng0 (= 0.7.9-2.2+b1),
 libcc1-0 (= 10.2.1-6),
 libclang-cpp11 (= 1:11.0.1-2),
 libclang1-11 (= 1:11.0.1-2),
 libcom-err2 (= 1.46.2-2),
 libcrypt-dev (= 1:4.4.18-4),
 libcrypt1 (= 1:4.4.18-4),
 libctf-nobfd0 (= 2.35.2-2),
 libctf0 (= 2.35.2-2),
 libcurl4 (= 7.74.0-1.3+deb11u13),
 libcurl4-openssl-dev (= 7.74.0-1.3+deb11u13),
 libdb5.3 (= 5.3.28+dfsg1-0.8),
 libdebconfclient0 (= 0.260),
 libdebhelper-perl (= 13.3.4),
 libdpkg-perl (= 1.20.13),
 libedit2 (= 3.1-20191231-2+b1),
 libelf1 (= 0.183-1),
 libexpat1 (= 2.2.10-2+deb11u5),
 libffi7 (= 3.3-6),
 libfile-stripnondeterminism-perl (= 1.12.0-1),
 libgcc-10-dev (= 10.2.1-6),
 libgcc-s1 (= 10.2.1-6),
 libgcrypt20 (= 1.8.7-6),
 libgdbm-compat4 (= 1.19-2),
 libgdbm6 (= 1.19-2),
 libglib2.0-0 (= 2.66.8-1+deb11u4),
 libgmp10 (= 2:6.2.1+dfsg-1+deb11u1),
 libgnutls30 (= 3.7.1-5+deb11u5),
 libgomp1 (= 10.2.1-6),
 libgpg-error0 (= 1.38-2),
 libgssapi-krb5-2 (= 1.18.3-6+deb11u5),
 libhogweed6 (= 3.7.3-1),
 libicu67 (= 67.1-7),
 libidn2-0 (= 2.3.0-5),
 libisl23 (= 0.23-1),
 libitm1 (= 10.2.1-6),
 libjq1 (= 1.6-2.1),
 libjs-jquery (= 3.5.1+dfsg+~3.5.5-7),
 libjs-sphinxdoc (= 3.4.3-2),
 libjs-underscore (= 1.9.1~dfsg-3),
 libjsoncpp24 (= 1.9.4-4),
 libk5crypto3 (= 1.18.3-6+deb11u5),
 libkeyutils1 (= 1.6.1-2),
 libkrb5-3 (= 1.18.3-6+deb11u5),
 libkrb5support0 (= 1.18.3-6+deb11u5),
 libldap-2.4-2 (= 2.4.57+dfsg-3+deb11u1),
 libllvm11 (= 1:11.0.1-2),
 liblsan0 (= 10.2.1-6),
 liblz4-1 (= 1.9.3-2),
 liblzma5 (= 5.2.5-2.1~deb11u1),
 libmagic-mgc (= 1:5.39-3+deb11u1),
 libmagic1 (= 1:5.39-3+deb11u1),
 libmd0 (= 1.0.3-3),
 libmount1 (= 2.36.1-8+deb11u2),
 libmpc3 (= 1.2.0-1),
 libmpdec3 (= 2.5.1-1),
 libmpfr6 (= 4.1.0-3),
 libncurses6 (= 6.2+20201114-2+deb11u2),
 libncursesw6 (= 6.2+20201114-2+deb11u2),
 libnettle8 (= 3.7.3-1),
 libnghttp2-14 (= 1.43.0-1+deb11u1),
 libnsl-dev (= 1.3.0-2),
 libnsl2 (= 1.3.0-2),
 libonig5 (= 6.9.6-1.1),
 libp11-kit0 (= 0.23.22-1),
 libpam-modules (= 1.4.0-9+deb11u1),
 libpam-modules-bin (= 1.4.0-9+deb11u1),
 libpam-runtime (= 1.4.0-9+deb11u1),
 libpam0g (= 1.4.0-9+deb11u1),
 libpcre2-8-0 (= 10.36-2+deb11u1),
 libpcre3 (= 2:8.39-13),
 libperl5.32 (= 5.32.1-4+deb11u3),
 libpipeline1 (= 1.5.3-1),
 libprocps8 (= 2:3.3.17-5),
 libprotobuf-c-dev (= 1.3.3-1+b2),
 libprotobuf-c1 (= 1.3.3-1+b2),
 libprotobuf23 (= 3.12.4-1+deb11u1),
 libprotoc23 (= 3.12.4-1+deb11u1),
 libpsl5 (= 0.21.0-1.2),
 libpython3-stdlib (= 3.9.2-3),
 libpython3.9-minimal (= 3.9.2-1),
 libpython3.9-stdlib (= 3.9.2-1),
 libquadmath0 (= 10.2.1-6),
 libreadline8 (= 8.1-1),
 librhash0 (= 1.4.1-2),
 librtmp1 (= 2.4+20151223.gitfa8646d.1-2+b2),
 libsasl2-2 (= 2.1.27+dfsg-2.1+deb11u1),
 libsasl2-modules-db (= 2.1.27+dfsg-2.1+deb11u1),
 libseccomp2 (= 2.5.1-1+deb11u1),
 libselinux1 (= 3.1-3),
 libsgx-dcap-quote-verify (= 1.22.100.3-focal1),
 libsgx-dcap-quote-verify-dev (= 1.22.100.3-focal1),
 libsgx-headers (= 2.25.100.3-focal1),
 libsigsegv2 (= 2.13-1),
 libsmartcols1 (= 2.36.1-8+deb11u2),
 libsqlite3-0 (= 3.34.1-3),
 libssh2-1 (= 1.9.0-2+deb11u1),
 libssl1.1 (= 1.1.1w-0+deb11u1),
 libstdc++-10-dev (= 10.2.1-6),
 libstdc++6 (= 10.2.1-6),
 libsub-override-perl (= 0.09-2),
 libsystemd0 (= 247.3-7+deb11u5),
 libtasn1-6 (= 4.16.0-2+deb11u1),
 libtinfo6 (= 6.2+20201114-2+deb11u2),
 libtirpc-common (= 1.3.1-1+deb11u1),
 libtirpc-dev (= 1.3.1-1+deb11u1),
 libtirpc3 (= 1.3.1-1+deb11u1),
 libtool (= 2.4.6-15),
 libtsan0 (= 10.2.1-6),
 libubsan1 (= 10.2.1-6),
 libuchardet0 (= 0.0.7-1),
 libudev1 (= 247.3-7+deb11u5),
 libunistring2 (= 0.9.10-4),
 libuuid1 (= 2.36.1-8+deb11u2),
 libuv1 (= 1.40.0-2+deb11u1),
 libxapian30 (= 1.4.18-3+deb11u1),
 libxml2 (= 2.9.10+dfsg-6.7+deb11u4),
 libz3-4 (= 4.8.10-1),
 libzstd1 (= 1.4.8+dfsg-2.1),
 linux-libc-dev (= 6.1.94-1~bpo11+1),
 login (= 1:4.8.1-1),
 lsb-base (= 11.1.0),
 m4 (= 1.4.18-5),
 make (= 4.3-4.1),
 man-db (= 2.9.4-2),
 mawk (= 1.3.4.20200120-2),
 media-types (= 4.0.0),
 meson (= 0.56.2-1),
 nasm (= 2.15.05-1),
 ncurses-base (= 6.2+20201114-2+deb11u2),
 ncurses-bin (= 6.2+20201114-2+deb11u2),
 ninja-build (= 1.10.1-1),
 openssl (= 1.1.1w-0+deb11u1),
 patch (= 2.7.6-7),
 perl (= 5.32.1-4+deb11u3),
 perl-base (= 5.32.1-4+deb11u3),
 perl-modules-5.32 (= 5.32.1-4+deb11u3),
 pkg-config (= 0.29.2-1),
 po-debconf (= 1.0.21+nmu1),
 procps (= 2:3.3.17-5),
 protobuf-c-compiler (= 1.3.3-1+b2),
 python-babel-localedata (= 2.8.0+dfsg.1-7),
 python3 (= 3.9.2-3),
 python3-alabaster (= 0.7.8-1.1),
 python3-babel (= 2.8.0+dfsg.1-7),
 python3-breathe (= 4.26.0-1),
 python3-certifi (= 2020.6.20-1),
 python3-chardet (= 4.0.0-1),
 python3-distutils (= 3.9.2-1),
 python3-docutils (= 0.16+dfsg-4),
 python3-idna (= 2.10-1+deb11u1),
 python3-imagesize (= 1.2.0-2),
 python3-jinja2 (= 2.11.3-1),
 python3-lib2to3 (= 3.9.2-1),
 python3-markupsafe (= 1.1.1-1+b3),
 python3-minimal (= 3.9.2-3),
 python3-packaging (= 20.9-2),
 python3-pkg-resources (= 52.0.0-4),
 python3-pygments (= 2.7.1+dfsg-2.1),
 python3-pyparsing (= 2.4.7-1),
 python3-requests (= 2.25.1+dfsg-2),
 python3-roman (= 2.0.0-5),
 python3-six (= 1.16.0-2),
 python3-snowballstemmer (= 2.1.0-1),
 python3-sphinx (= 3.4.3-2),
 python3-sphinx-rtd-theme (= 0.5.1+dfsg-1),
 python3-tomli (= 1.2.2-2~bpo11+1),
 python3-tomli-w (= 1.0.0-1~bpo11+1),
 python3-tz (= 2021.1-1),
 python3-urllib3 (= 1.26.5-1~exp1),
 python3-voluptuous (= 0.11.7-2),
 python3.9 (= 3.9.2-1),
 python3.9-minimal (= 3.9.2-1),
 readline-common (= 8.1-1),
 sed (= 4.7-1),
 sensible-utils (= 0.0.14),
 sgml-base (= 1.30),
 sphinx-common (= 3.4.3-2),
 sphinx-rtd-theme-common (= 0.5.1+dfsg-1),
 sysvinit-utils (= 2.96-7+deb11u1),
 tar (= 1.34+dfsg-1+deb11u1),
 tzdata (= 2024a-0+deb11u1),
 util-linux (= 2.36.1-8+deb11u2),
 xml-core (= 0.18+nmu1),
 xz-utils (= 5.2.5-2.1~deb11u1),
 zlib1g (= 1:1.2.11.dfsg-2+deb11u2)
Environment:
 DEB_BUILD_OPTIONS="parallel=2"
 LANG="C"
 LC_ALL="C"
 SOURCE_DATE_EPOCH="1729516940"
```

</details>

1. Install the Gramine package on Debian 12:

    ```bash
    sudo curl -fsSLo /etc/apt/keyrings/gramine-keyring-$(lsb_release -sc).gpg https://packages.gramineproject.io/gramine-keyring-$(lsb_release -sc).gpg
    echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/gramine-keyring-$(lsb_release -sc).gpg] https://packages.gramineproject.io/ $(lsb_release -sc) main" \
|   sudo tee /etc/apt/sources.list.d/gramine.list

    sudo curl -fsSLo /etc/apt/keyrings/intel-sgx-deb.asc https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/intel-sgx-deb.asc] https://download.01.org/intel-sgx/sgx_repo/ubuntu jammy main" \
|   sudo tee /etc/apt/sources.list.d/intel-sgx.list

    sudo apt-get update
    sudo apt-get install gramine
    ```

2. Build `hello_world` and extract mrenclave


### &rarr; reproducible (guided scenario)
