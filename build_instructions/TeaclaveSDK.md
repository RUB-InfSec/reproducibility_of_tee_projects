# Teaclave-sgx-sdk v2.0.0

The following instructions were executed on Ubuntu 22.04 LTS with Docker installed.


## Reference Measurement

1. Pull and run the docker image as detailed in [API document](https://teaclave.apache.org/sgx-sdk-docs/environment-setup)
    
    ```bash
    sudo snap install rustup --classic
    rustup toolchain install nightly-2019-03-31 --profile minimal
    rustup component add rust-src
    
    git clone https://github.com/apache/teaclave-sgx-sdk
    cd teaclave-sgx-sdk/ && git checkout 1b1d03376056321441ef99716aa0888bd5ef19f7 && cd ..
    
    docker run -v incubator-teaclave-sgx-sdk:/root/sgx -ti --device /dev/sgx/enclave --device /dev/sgx/provision baiduxlab/sgx-rust
    ```

2. Make `helloworld` enclave and extract the measurement
    
    ```bash
    cd /root/sgx/samplecode/helloworld
    make clean
    cd enclave && cargo clean
    make SGX_MODE=HW SGX_DEBUG=1
    /opt/sgxsdk/bin/x64/sgx_sign dump -enclave bin/enclave.signed.so -dumpfile /tmp/dump.txt
    cat /tmp/dump.txt
    # metadata->enclave_css.body.enclave_hash.m:
    # 0x7d 0xd4 0x82 0x1c 0x0b 0x3b 0x74 0xac 0x20 0x10 0x1b 0xbd 0xd4 0xca 0x03 0x12 
    # 0xb7 0xf5 0x4d 0xfe 0x82 0x00 0x9f 0x12 0xd5 0x54 0x92 0x1b 0x6a 0xc7 0x46 0x01 
    ```

**Reference Measurement:** `7dd4821c0b3b74ac20101bbdd4ca0312b7f54dfe82009f12d554921b6ac74601`


## Build from Source

1. Clone the repo from [GitHub](https://github.com/apache/teaclave-sgx-sdk) and build the Docker image
    
    ```bash
    git clone https://github.com/apache/teaclave-sgx-sdk.git
    cd teaclave-sgx-sdk/
    git checkout 1b1d03376056321441ef99716aa0888bd5ef19f7
    cd dockerfile

    nano 05_rust.sh
    # add `--locked` at the end of the command installing xargo

    docker build -t rust-sgx-docker-2004 -f Dockerfile.2004.nightly .
    cd ../../

    docker run -v "$(pwd)/teaclave-sgx-sdk:/root/sgx" -v /var/run/aesmd:/var/run/aesmd -ti --device /dev/sgx_enclave --device /dev/sgx_provision rust-sgx-docker-2004
    ```

2. Make `helloworld` enclave and extract the measurement
    
    ```bash
    cd /root/sgx/samplecode/helloworld
    make SGX_MODE=HW SGX_DEBUG=1
    /opt/sgxsdk/bin/x64/sgx_sign dump -enclave bin/enclave.signed.so -dumpfile /tmp/dump.txt
    cat /tmp/dump.txt
    # metadata->enclave_css.body.enclave_hash.m:
    # 0xf3 0xc6 0xae 0xa5 0x86 0x47 0x20 0xfb 0xca 0x24 0x53 0x09 0xfb 0x73 0x23 0xfa 
    # 0xc8 0x3a 0x4f 0x88 0xd0 0x4a 0xf3 0xbf 0x3d 0x97 0x79 0x9a 0xe7 0xd9 0x4b 0x85 
    ```

**Reference Measurement:** `f3c6aea5864720fbca245309fb7323fac83a4f88d04af3bf3d97799ae7d94b85`


### &rarr; NOT reproducible (unguided scenario)
