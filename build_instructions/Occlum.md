# Occlum 0.30.0

Occlum is a multi-process library OS for Intel SGX. The [GitHub](https://github.com/occlum/occlum) repo and docker image contains multiple demo programs. We will match the measurement of `hello_c`. All experiments were conducted on Ubuntu 22.04 LTS with Docker installed. Note that the build requires an SGX capable machine or VM.

## Inside pre-built docker image

1. Pull and run docker image
    
    ```bash
    docker pull occlum/occlum:0.30.1-ubuntu20.04
    docker run -it --privileged -v /dev/sgx:/dev/sgx occlum/occlum:0.30.1-ubuntu20.04
    ```

2. Build and run `hello_c`
    
    ```bash
    cd demos/hello_c
    mkdir occlum_workspace && cd occlum_workspace
    occlum init && rm -rf image
    copy_bom -f ../hello.yaml --root image --include-dir /opt/occlum/etc/template
    occlum build
    occlum run /bin/hello_world
    ```

4. Extract the measurement
    ```bash
    sgx_sign dump -enclave ~/demos/hello_c/occlum_workspace/build/lib/libocclum-libos.signed.so -dumpfile libocclum-libos.sigstruct.hex | cat libocclum-libos.sigstruct.hex
    # metadata->enclave_css.body.enclave_hash.m:
    # 0xc1 0xaf 0xc3 0x47 0xfc 0x52 0xe7 0x4e 0x30 0x79 0xf0 0xfe 0x96 0xf9 0x2b 0x42 
    # 0x5f 0x41 0x8f 0x6b 0x6a 0x94 0x09 0x46 0xeb 0xe7 0x98 0x89 0x0e 0xf0 0x2a 0x44
    ```

**Reference measurement:** `0xc1afc347fc52e74e3079f0fe96f92b425f418f6b6a940946ebe798890ef02a44`


## Build (unguided)

1. Build and Install the Docker image from [GitHub](https://github.com/occlum/occlum/tree/master/tools/docker) to ensure all dependencies are correct.

    ```bash
    git clone https://github.com/occlum/occlum.git
    git checkout  v0.30.0
    cd occlum/tools/docker
    ./build_image.sh 0.30.0 ubuntu20.04
    ```

2. Run the docker and build the `hello_c` binary 
    ```bash
    docker run -it --privileged -v /dev/sgx:/dev/sgx occlum_installed
    ```

3. Extract the measurement for the `hello_c` binary
    ```bash
    # metadata->enclave_css.body.enclave_hash.m:
    # 0x1e 0x2b 0xb8 0x74 0xa7 0x4a 0x12 0xa0 0x3a 0x16 0xf2 0x9e 0x81 0x90 0x9c 0x00 
    # 0x1f 0xd6 0xac 0xe9 0x9a 0x15 0x52 0x86 0x34 0x11 0x76 0xcd 0xfd 0x99 0xaa 0x2a
    ```

**Extracted measurement:** `0x1e2bb874a74a12a03a16f29e81909c001fd6ace99a155286341176cdfd99aa2a`


### &rarr; NOT reproducible (unguided scenario)
