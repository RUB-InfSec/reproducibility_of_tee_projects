# EdgelessRT v0.4.9

The following instructions were executed on Ubuntu 24.04 LTS with Docker installed. Note that the build requires an SGX capable machine or VM. 


## Reference Measurement

1. Download and Install EdgelessRT:

   ```bash
    ERT_DEB=edgelessrt_0.4.9_amd64_ubuntu-$(lsb_release -rs).deb
    wget https://github.com/edgelesssys/edgelessrt/releases/download/v0.4.9/$ERT_DEB
    sudo dpkg -i edgelessrt_0.4.9_amd64_ubuntu-22.04.deb
    source /opt/edgelessrt/share/openenclave/openenclaverc
    ```

3. Download and build sample enclave from [GitHub](https://github.com/edgelesssys/edgelessrt):
   
    ```bash
    git clone https://github.com/edgelesssys/edgelessrt.git
    cd edgelessrt/samples/helloworld/
    git checkout v0.4.9

    mkdir build && cd build
    cmake .. -DCMAKE_PREFIX_PATH=/opt/edgelessrt
    make -j
    OE_SIMULATION=1 erthost enclave.signed
    oesign dump -e enclave.signed
    # mrenclave=5e3750d5ee8f40b7f1b9dbc74b74f8747f59a5bfb16f6266c721cac479266bbc
    ```
    
**Reference Measurement:**  `5e3750d5ee8f40b7f1b9dbc74b74f8747f59a5bfb16f6266c721cac479266bbc`


## Build (Unguided)

We followed the official build instructions on Ubuntu 22.04 LTS with Docker and SGX installed.

1. Install SGX libraries:

    ```bash
    echo "deb [signed-by=/etc/apt/trusted.gpg.d/intel-sgx.gpg] https://download.01.org/intel-sgx/sgx_repo/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/intel-sgx.list

    wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/intel-sgx.gpg

    sudo apt update
    sudo apt install -y make cmake clang-11 libssl-dev libsgx-enclave-common libsgx-dcap-ql
    ```

2. Build and run the Docker image:

    ```bash
    git clone https://github.com/edgelesssys/edgelessrt.git
    cd edgelessrt
    git checkout v0.4.9
    
    docker build -f dockerfiles/Dockerfile -t edgelessrt-docker-build .
    docker run  --privileged -ti --device /dev/sgx/enclave --device /dev/sgx/provision edgelessrt-docker-build
    ```

3. Download and build sample code inside the Docker container:

    ```bash
    git clone https://github.com/edgelesssys/edgelessrt.git
    cd edgelessrt/samples/helloworld/
    git checkout v0.4.9

    mkdir build && cd build
    cmake .. -DCMAKE_PREFIX_PATH=/opt/edgelessrt
    make -j
    erthost enclave.signed
    oesign dump -e enclave.signed
    # mrenclave=f4f34f438d388a3234f0e414ef383bae3dd5c0c8aea0af450a73c8805adba963
    ```

**Retreived measurement:** `0xf4f34f438d388a3234f0e414ef383bae3dd5c0c8aea0af450a73c8805adba963`


### &rarr; NOT reproducible (unguided scenario)
