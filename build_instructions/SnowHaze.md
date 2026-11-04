# SnowHaze zka-sgx

The following instructions were executed on Ubuntu 24.04 LTS with Docker installed.


## Reference Measurement

The reference is included in the [README](https://github.com/snowhaze/zka-sgx/blob/b90512d51393e07506ec13944dcee10ba53dcfe3/README.md):

```
RESULT:  Verified Chain for Enclave db156b322ac5e8814d03f02c5154bed5cc9706ae22af69c1f1f4bdad3ee8be7a
NOTE:
  Tool Versions:
  - zkacli: 1.0 (a4b7f4ac497b03697c0558bb0d39c97ae443cf6a)
  - libsodium: 'stable' Branch (2b5f8f2b6810121c2d9a8cc8a392e01f4d3de433)
  - SGX SDK: 2.10.100.2
  - Compiler: cc (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0
  - Signed Enclave: ce422cc914757a4d114e287daec538f7dce97096a5b73c6134c0a28fb2f9d33c
```

**Reference measurement:** `ce422cc914757a4d114e287daec538f7dce97096a5b73c6134c0a28fb2f9d33c`


## Build (unguided)

Following the instructions for [Building the Enclave](https://github.com/snowhaze/zka-sgx#building-the-enclave) in a Docker container with the required Ubuntu 20.04.

1. Install dependencies:

    ```bash
    docker run -it ubuntu:20.04 bash

    apt update
    apt upgrade
    apt install build-essential
    apt install git-all
    apt install wget
    apt install nano

    # download libsodium
    cd ~
    git clone https://github.com/jedisct1/libsodium.git
    cd libsodium
    git checkout 2b5f8f2b6810121c2d9a8cc8a392e01f4d3de433
    cd ~

    # gcc 7.5
    apt install gcc-7 g++-7
    update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 70
    update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 70
    update-alternatives --config gcc

    # SGX setup
    git clone https://github.com/snowhaze/zka-sgx.git
    cd zka-sgx
    git checkout a4b7f4ac497b03697c0558bb0d39c97ae443cf6a
    cd ..
    nano ./zka-sgx/sgx_install.sh
    # remove all sudo commands
    bash ./zka-sgx/sgx_install.sh
    chown -R root /opt/intel/sgxsdk/environment
    source /opt/intel/sgxsdk/environment
    ```

2. Build the enclave:

    ```bash
    cd zka-sgx/zkacli
    make release-1

    # generate sign.pem
    openssl genrsa -3 -out private.key 3072
    openssl rsa -in private.key -out private.pem
    openssl rsa -in private.pem -pubout -out sign.pem

    openssl dgst -sha256 -sign private.pem -out enclave.signdata.sig enclave.signdata

    export LD_LIBRARY_PATH=/opt/intel/sgxsdk/lib64:$LD_LIBRARY_PATH
    export LIBRARY_PATH=/opt/intel/sgxsdk/lib64:$LIBRARY_PATH
    make release-2
    ```

3. Extract enclave measurement:

    ```bash
    awk -f get_enclave_hash.awk enclave_dumpfile
    # 30de294064fa95c5b09ee1fdd93407910c0769a70b11847f1e330fc1a3c609d7
    ```

**Retrieved measurement:** `30de294064fa95c5b09ee1fdd93407910c0769a70b11847f1e330fc1a3c609d7`

### &rarr; NOT reproducible (unguided scenario)
