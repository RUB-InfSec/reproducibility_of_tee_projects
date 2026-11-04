# Signal CDSI 1.25.2

The following instructions were executed on Ubuntu 24.04 LTS with Docker installed. Note that the build requires an SGX capable machine or VM.


## Reference Measurements

*Notes:* 
- *Current enclaves are located in the `ContactDiscoveryService-Icelake-1.25.2/src/main/resources/org/signal/cdsi/enclave/` folder of the repository.*
- *Enclave measurements are included in the filenames of the enclave binaries for simplicity and reproducibility (see [Docker build process](#build-process-with-docker)).*

```bash
wget https://github.com/signalapp/ContactDiscoveryService-Icelake/archive/refs/tags/1.25.2.tar.gz
tar -xf 1.25.2.tar.gz 

ls ContactDiscoveryService-Icelake-1.25.2/src/main/resources/org/signal/cdsi/enclave/
# enclave-Standard_DC24s_v3-ee9503070127120074612b6688e593b67e486b1541449f54d71e387484eb40a3.signed
# enclave-Standard_DC2s_v3-3ded708ca5a42fd84b4639dc661a7ec4b9c9f1b92809c0fc91da2349a5a89d05.signed
# libjnishim-Standard_DC24s_v3-ee9503070127120074612b6688e593b67e486b1541449f54d71e387484eb40a3.so
# libjnishim-Standard_DC2s_v3-3ded708ca5a42fd84b4639dc661a7ec4b9c9f1b92809c0fc91da2349a5a89d05.so
```

**Reference measurements:**

| Enclave | Measurement |
|---------|-------------|
| `enclave-Standard_DC24s_v3` | `ee9503070127120074612b6688e593b67e486b1541449f54d71e387484eb40a3` |
| `enclave-Standard_DC2s_v3` | `3ded708ca5a42fd84b4639dc661a7ec4b9c9f1b92809c0fc91da2349a5a89d05` |


## Build (unguided)

Building following the [instructions](https://github.com/signalapp/ContactDiscoveryService-Icelake/blob/main/README.md) in the GitHub repo.

1. Checkout the latest version of CDSI:

    ```bash
    git clone https://github.com/signalapp/ContactDiscoveryService-Icelake.git
    cd ContactDiscoveryService-Icelake/
    git checkout tags/1.25.2

    git submodule init
    git submodule update --recursive --init || true
    git submodule update --recursive || true
    ```

2. Install depencies:

    ```bash
    # Apache Maven (3.8.7)
    sudo apt install maven

    # Java 25 (25.0.2)
    sudo apt install openjdk-25-jdk

    # SGX libraries
    echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu jammy main" > /etc/apt/sources.list.d/intel-sgx.list
    wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -
    echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/22.04/prod jammy main" > /etc/apt/sources.list.d/msprod.list
    wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt update
    sudo apt install -y libsgx-enclave-common libsgx-dcap-ql libsgx-dcap-ql-dev

    # Install clang-11 (from LLVM repo)
    sudo apt install -y lsb-release
    wget https://apt.llvm.org/llvm.sh
    chmod +x llvm.sh
    ./llvm.sh 11

    # OpenSSL
    git clone https://github.com/openssl/openssl.git
    cd openssl
    git checkout OpenSSL_1_1_1u
    ./config
    make -j8
    sudo make install -j8
    sudo ldconfig

    # OpenEnclave
    # Install Open Enclave packages
    wget https://github.com/openenclave/openenclave/releases/download/v0.19.13/Ubuntu_2204_open-enclave_0.19.13_amd64.deb
    wget https://github.com/openenclave/openenclave/releases/download/v0.19.13/Ubuntu_2204_open-enclave-hostverify_0.19.13_amd64.deb
    sudo apt install -y ./Ubuntu_2204_open-enclave_0.19.13_amd64.deb
    sudo dpkg -i --force-overwrite ./Ubuntu_2204_open-enclave-hostverify_0.19.13_amd64.deb
    sudo apt install -f -y   # fix any resulting dependency issues
    source /opt/openenclave/share/openenclave/openenclaverc
    ```

3. Clear reference enclave builds:

    ```bash
    rm src/main/resources/org/signal/cdsi/enclave/*
    ```

4. Build CDSI (not required): 

    ```bash
    mvn verify -Denforcer.skip=true
    # build fails when running the tests
    ```

5. Build release version:

    ```bash
    ./mvnw exec:exec@enclave-release
    # Files are automatically put to src/main/resources/org/signal/cdsi/enclave/
    mkdir ../enclave_build
    ```

6. Extract the measurements:

    ```bash
    src/main/resources/org/signal/cdsi/enclave/
    # enclave-debug.signed
    # enclave-Standard_DC24s_v3-ee9503070127120074612b6688e593b67e486b1541449f54d71e387484eb40a3.signed
    # enclave-Standard_DC2s_v3-3ded708ca5a42fd84b4639dc661a7ec4b9c9f1b92809c0fc91da2349a5a89d05.signed
    # libjnishim-debug.so
    # libjnishim-Standard_DC24s_v3-ee9503070127120074612b6688e593b67e486b1541449f54d71e387484eb40a3.so
    # libjnishim-Standard_DC2s_v3-3ded708ca5a42fd84b4639dc661a7ec4b9c9f1b92809c0fc91da2349a5a89d05.so
    ```

**Retrieved measurements:**

| Enclave | Measurement |
|---------|-------------|
| `enclave-Standard_DC24s_v3` | `ee9503070127120074612b6688e593b67e486b1541449f54d71e387484eb40a3` |
| `enclave-Standard_DC2s_v3` | `3ded708ca5a42fd84b4639dc661a7ec4b9c9f1b92809c0fc91da2349a5a89d05` |


### &rarr; reproducible (unguided scenario)
