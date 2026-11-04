# enclave-vrf v0.1.0

The following instructions were executed on Ubuntu 24.04 LTS with Docker installed. 

## Reference Measurement

1. Download the released reference binary for version 1.0.0:

    ```bash
    wget https://github.com/smartbch/enclave-vrf/releases/download/v0.1.0/rand_build.tgz
    tar -xzf rand_build.tgz 
    ```

2. Install EGo 0.5.0:
    
    ```bash
    sudo apt update
    sudo apt -y install libssl-dev gdb libsgx-enclave-common libsgx-quote-ex libsgx-dcap-ql libsgx-dcap-ql-dev
    wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1f-1ubuntu2_amd64.deb
    sudo dpkg -i libssl1.1_1.1.1f-1ubuntu2_amd64.deb

    wget -qO- https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add
    sudo add-apt-repository "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu `lsb_release -cs` main"
    wget https://github.com/edgelesssys/ego/releases/download/v0.5.0/ego_0.5.0_amd64.deb
    sudo apt install -y ./ego_0.5.0_amd64.deb build-essential
    ```

3. Extract MRENCLAVE with EGo:
 
    ```bash
    ego uniqueid rand
    # EGo v0.5.0 (55bad14bb8d00dbae2000a8d603f9b588bb79451)
    # e504a9d352b7514700d4169956a482eba14b6f00a16750c98a8840a22201f54b
    ```

**Reference measurement:**
`e504a9d352b7514700d4169956a482eba14b6f00a16750c98a8840a22201f54b`

*Note: Executing the same steps in the Docker container used in the subsequent section yields the same reference.*


## Build (unguided)

We followed the instructions from the [release](https://github.com/smartbch/enclave-vrf/releases/tag/v0.1.0):

1. Use Ubuntu 20.04 as the OS and install basic dependencies:

    ```bash
    docker run -it ubuntu:20.04
    apt update
    apt install  -y gnupg2 apt install wget
    ```

2. Install OpenEnclave. We skipped the installation of the driver as there is no need to execute the enclave for reproducibility tests.

    ```bash
    echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu focal main' | tee /etc/apt/sources.list.d/intel-sgx.list
    wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -

    echo "deb http://apt.llvm.org/focal/ llvm-toolchain-focal-10 main" | tee /etc/apt/sources.list.d/llvm-toolchain-focal-10.list
    wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -

    echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/20.04/prod focal main" | tee /etc/apt/sources.list.d/msprod.list
    wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

    apt update

    apt -y install dkms
    ```

3. Install EGo (0.5.0):

    ```bash
    apt -y install clang-10 libssl-dev gdb libsgx-enclave-common libsgx-quote-ex libprotobuf17 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave

    # install ego
    wget -qO- https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add
    add-apt-repository "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu `lsb_release -cs` main"
    wget https://github.com/edgelesssys/ego/releases/download/v0.5.0/ego_0.5.0_amd64.deb
    apt install -y ./ego_0.5.0_amd64.deb build-essential libssl-dev
    ```

4. Build `rand` binary:

    ```bash
    mkdir $HOME/rand
    cd $HOME/rand
    wget https://github.com/smartbch/enclave-vrf/archive/refs/tags/v0.1.0.tar.gz 
    tar zxvf v0.1.0.tar.gz 
    mv enclave-vrf-0.1.0 enclave-vrf
    cd enclave-vrf/sgx-rand
    ego-go build rand.go
    ```

5. Extract MRENCLAVE with EGo:

    ```bash
    ego sign rand
    ego uniqueid rand
    # EGo v0.5.0 (55bad14bb8d00dbae2000a8d603f9b588bb79451)
    # d18740011d46bde20a01a1e521c744e69f137fad06a5a3993e0c8f1905cf0b5d
    ```

**Retrieved measurement:** 
`d18740011d46bde20a01a1e521c744e69f137fad06a5a3993e0c8f1905cf0b5d` &rarr; mismatch


### &rarr; NOT reproducible (unguided scenario)