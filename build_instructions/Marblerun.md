# Marblerun v1.9.0

The following instructions were executed on Ubuntu 24.04 LTS with Docker installed. Note that the build requires an SGX capable machine or VM.


## Reference Measurement

1. Install SGX libraries:

    ```bash
    echo "deb [signed-by=/etc/apt/trusted.gpg.d/intel-sgx.gpg] https://download.01.org/intel-sgx/sgx_repo/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/intel-sgx.list

    wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/intel-sgx.gpg

    sudo apt update
    sudo apt install -y make cmake libssl-dev libsgx-enclave-common libsgx-dcap-ql
    ```

2. Install Edgeless Runtime v0.5.2:

    ```bash
    wget https://github.com/edgelesssys/edgelessrt/releases/download/v0.5.2/edgelessrt_0.5.2_amd64_ubuntu-24.04.deb
    sudo apt install ./edgelessrt_0.5.2_amd64_ubuntu-24.04.deb build-essential cmake libssl-dev
    ```

3. Install EGo v1.9.0:

    ```bash
    wget https://github.com/edgelesssys/ego/releases/download/v1.9.0/ego_1.9.0_amd64_ubuntu-24.04.deb
    sudo apt install ./ego_1.9.0_amd64_ubuntu-24.04.deb build-essential libssl-dev
    ```

4. Download the released Coordinator enclave:

    ```bash
    wget https://github.com/edgelesssys/marblerun/releases/download/v1.9.0/coordinator-enclave.signed
    ```
    
5. Extract MRENCLAVE with EGo:

    ```bash
    ego uniqueid coordinator-enclave.signed 
    # 2026/04/01 14:51:45 INFO EGo version=1.9.0 git_commit=6f8b18f24006383d1b38ba1cf06c3e95652bf1a7
    # 1e97125ee7340942003e8740024f956054cecb51de1d907c33ae226e694c57cd
    ```

**Reference measurement:** `1e97125ee7340942003e8740024f956054cecb51de1d907c33ae226e694c57cd`


## Build (unguided)

1. Download source code for Marblerun v1.9.0:

    ```bash
    git clone https://github.com/edgelesssys/marblerun.git
    cd marblerun/
    git checkout d4a8d760aad989ae556f3cef741351669ae4892e
    git submodule update --init --recursive
    ```

2. Build with Docker following the [BUILD docs](https://github.com/edgelesssys/marblerun/blob/d4a8d760aad989ae556f3cef741351669ae4892e/BUILD.md#build):

    ```bash
    openssl genrsa -out private.pem -3 3072
    export DOCKER_BUILDKIT=1
    docker build --secret id=signingkey,src=private.pem --target export -o. - < dockerfiles/Dockerfile.coordinator
    docker build -o. - < dockerfiles/Dockerfile.cli
    ```

4. Extract MRENCLAVE with EGo:

    ```bash
    ego uniqueid coordinator-enclave.signed 
    # 2026/04/01 15:15:59 INFO EGo version=1.9.0 git_commit=6f8b18f24006383d1b38ba1cf06c3e95652bf1a7
    # 5095e9475977aa30378a0911f13f4e8ad39f385a1e271d908a18229247a07598
    ```

**Retrieved measurement:** `5095e9475977aa30378a0911f13f4e8ad39f385a1e271d908a18229247a07598` &rarr; *mismatch*


### &rarr; NOT reproducible (unguided scenario)


## Build (guided)

The developers informed us that we need to append the flag `--build-arg=production=ON` to the docker build, resulting in the following updated build procedure:

1. Download source code for Marblerun v1.9.0:

    ```bash
    git clone https://github.com/edgelesssys/marblerun.git
    cd marblerun/
    git checkout d4a8d760aad989ae556f3cef741351669ae4892e
    git submodule update --init --recursive
    ```

2. Build with Docker following the [BUILD docs](https://github.com/edgelesssys/marblerun/blob/d4a8d760aad989ae556f3cef741351669ae4892e/BUILD.md#build):

    ```bash
    openssl genrsa -out private.pem -3 3072
    export DOCKER_BUILDKIT=1
    docker build --secret id=signingkey,src=private.pem --target export -o. - < dockerfiles/Dockerfile.coordinator --build-arg=production=ON
    docker build -o. - < dockerfiles/Dockerfile.cli
    ```

4. Extract MRENCLAVE with EGo:

    ```bash
    ego uniqueid coordinator-enclave.signed 
    # 2026/04/20 08:15:29 INFO EGo version=1.9.0 git_commit=6f8b18f24006383d1b38ba1cf06c3e95652bf1a7
    # 1e97125ee7340942003e8740024f956054cecb51de1d907c33ae226e694c57c
    ```

**Retrieved measurement:** `1e97125ee7340942003e8740024f956054cecb51de1d907c33ae226e694c57c` &rarr; *match*


### &rarr; reproducible (guided scenario)