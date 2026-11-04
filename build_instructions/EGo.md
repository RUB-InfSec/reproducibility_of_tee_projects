# Ego v1.7.0

The [GitHub](https://github.com/edgelesssys/ego/tree/master/samples/reproducible_build) repository provides a facility to produce and verify a reproducible build using Dockerfiles. We built EGo from source and followed these instructions on Ubuntu 22.04 LTS with Docker installed. Note that the build requires an SGX capable machine or VM.


## Reference Measurement

The documentation for the [reproducible build](https://github.com/edgelesssys/ego/tree/v1.7.0/samples/reproducible_build) example in the GitHub repository provides a reference hash:

**Reference Measurement:** `59e89da6161853814f6916462fed1be4727461bf502ce5865a80c8712cc2f548`


## Build

1. Install SGX libraries:

    ```bash
    echo "deb [signed-by=/etc/apt/trusted.gpg.d/intel-sgx.gpg] https://download.01.org/intel-sgx/sgx_repo/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/intel-sgx.list
    
    wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/intel-sgx.gpg
    
    sudo apt update
    sudo apt install -y libsgx-enclave-common libsgx-dcap-ql
    ```

2. Build EGo from source:

    ```bash
    git clone https://github.com/edgelesssys/ego.git
    cd ego/
    git checkout v1.7.0
    
    cd dockerfiles
    DOCKER_BUILDKIT=1 sudo docker build --target export -o. - < Dockerfile
    sudo dpkg -i ego_1.7.0_amd64_ubuntu-22.04.deb
    ```

3. Build reproducible build example:

    ```bash
    cd ../samples/reproducible_build/
    openssl genrsa -out private.pem -3 3072
    DOCKER_BUILDKIT=1 docker build --secret id=signingkey,src=private.pem -o. .
    ego uniqueid helloworld
    # 2025/05/02 06:26:17 INFO EGo version=1.7.0 git_commit=3a3f54a1d1cd9318dd1ade411f9f439f53bb6694
    # 59e89da6161853814f6916462fed1be4727461bf502ce5865a80c8712cc2f548
    ```
    
**Extracted Measurement:** `59e89da6161853814f6916462fed1be4727461bf502ce5865a80c8712cc2f548`


### &rarr; reproducible (unguided scenario)
