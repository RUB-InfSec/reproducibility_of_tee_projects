# SGX SSE

The following instructions were executed on Ubuntu 24.04 LTS.


## Reference Measurement

```bash
git clone https://github.com/MonashCybersecurityLab/SGXSSE.git
cd SGXSSE
git checkout 052069563303c6b2fa8dc3069cef450b678e2ea3

cd SE_SGX_1
source /opt/intel/sgxsdk/environment
sgx_sign dump -enclave CryptoEnclave.signed.so -dumpfile CryptoEnclave.sigstruct.so
cat CryptoEnclave.sigstruct.so 
# metadata->enclave_css.body.enclave_hash.m:
# 0x64 0x67 0x14 0xa6 0x67 0xec 0x0c 0x9e 0x90 0x4a 0xca 0x56 0x25 0xf6 0x19 0xdb 
# 0xc6 0x21 0x7a 0x10 0xe5 0x69 0x80 0xae 0xd2 0x8e 0x02 0x79 0x51 0x57 0x90 0x84 

cd SE_SGX_2
sgx_sign dump -enclave CryptoEnclave.signed.so -dumpfile CryptoEnclave.sigstruct.so
cat CryptoEnclave.sigstruct.so 
# metadata->enclave_css.body.enclave_hash.m:
# 0x72 0x9f 0xc6 0x07 0x61 0x30 0x6a 0x3c 0xe1 0x09 0xa5 0x7c 0xa0 0x52 0x27 0x4e 
# 0x06 0x86 0xe9 0xaa 0x79 0xf6 0x55 0xec 0x7a 0x60 0x71 0x93 0x4e 0x13 0xf8 0x6c 
```

**Reference measurements:**
- CryptoEnclave (SE_SGX_1): `646714a667ec0c9e904aca5625f619dbc6217a10e56980aed28e027951579084`
- CryptoEnclave (SE_SGX_2): `729fc60761306a3ce109a57ca052274e0686e9aa79f655ec7a6071934e13f86c`

## Build (unguided)

1. Start Docker container with Ubuntu 18.04:
    ```bash
    docker run -it ubuntu:18.04
    ```

2. Install dependencies:
    ```bash
    apt-get update && apt-get install -y \
        ca-certificates \
        wget \
        git \
        build-essential \
        cmake \
        libssl-dev \
        libcurl4-openssl-dev \
        libprotobuf-dev \
        protobuf-compiler \
        libpcap-dev && \
        update-ca-certificates
    ```

3. Install SGX SDK > 2.0:
    ```bash
    wget https://download.01.org/intel-sgx/sgx-linux/2.13/distro/ubuntu18.04-server/sgx_linux_x64_sdk_2.13.100.4.bin --no-check-certificate \
        -O sgx_sdk.bin && \
        chmod +x sgx_sdk.bin && \
        printf "no\n/opt/intel\n" | ./sgx_sdk.bin
    ```

4. Install matching SGX SSL:
    ```bash
    cd /opt
    git clone --branch support_openssl_1.1.1 \
        https://github.com/intel/intel-sgx-ssl.git
    git checkout win_2.13_1.1.1k && \
        wget -P /opt/intel-sgx-ssl/openssl_source https://github.com/openssl/openssl/releases/download/OpenSSL_1_1_1k/openssl-1.1.1k.tar.gz && \
        source /opt/intel/sgxsdk/environment && \
        make all -j$(nproc)
    ```

*Note: Build fails because mitigation tools need to be installed which is not possible for this SGX SDK version. We did not find a combination of SGX SDK and SGX SSL that works with Ubuntu 18.04.*


## Build (guided)

*The developers informed us that they used SGX SDK 2.6. The prebuilt binary is not available, and the `download_prebuild.sh` script similarly fails to fetches sources in the preparatory steps of the build.*


### &rarr; NOT reproducible (guided scenario)
