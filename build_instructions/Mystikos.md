# Mystikos v0.13.0

Base for all following steps is the README of [this commit](https://github.com/microsoft/mystikos/tree/v0.13.0) on Ubuntu 20.04. OS.


## Reference Measurement

1. Install SGX and OpenEnclave:

    ```bash
    echo "deb [signed-by=/etc/apt/trusted.gpg.d/intel-sgx.gpg] https://download.01.org/intel-sgx/sgx_repo/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/intel-sgx.list
    wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/intel-sgx.gpg

    curl -sSL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /usr/share/keyrings/microsoft.gpg > /dev/null
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft.gpg] https://packages.microsoft.com/ubuntu/20.04/prod focal main" | \
    sudo tee /etc/apt/sources.list.d/msprod.list

    sudo apt update
    sudo apt upgrade

    sudo apt -y install clang-11 libssl-dev gdb libsgx-enclave-common libsgx-quote-ex libprotobuf17 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
    ```

2. Install Cmake version > 3.5:

    ```bash
    wget https://github.com/Kitware/CMake/releases/download/v3.29.0/cmake-3.29.0-linux-x86_64.sh
    sudo mkdir -p /opt/cmake
    sudo sh cmake-3.29.0-linux-x86_64.sh --prefix=/opt/cmake --skip-license
    echo 'export PATH=/opt/cmake/bin:$PATH' >> ~/.bashrc
    source ~/.bashrc
    ```

3. Download release package:

    ```bash
    wget https://github.com/deislabs/mystikos/releases/download/v0.13.0/Ubuntu-2004_mystikos-0.13.0-x86_64.deb
    dpkg-deb -xv Ubuntu-2004_mystikos-0.13.0-x86_64.deb .
    ```

4. Sign the enclave:

    ```bash
    openssl genrsa -3 -out key.pem 3072
    /opt/openenclave/bin/oesign sign -e opt/mystikos/lib/openenclave/mystenc.so --key-file key.pem
    ```

5. Extract the MRENCLAVE:

    ```bash
    /opt/openenclave/bin/oesign dump -e opt/mystikos/lib/openenclave/mystenc.so.signed > mystenc.sigstruct.hex
    cat mystenc.sigstruct.hex
    ```

**Reference measurement:** `fcf7bd2a245c38ac0196cbcdadcb2343bee0236b5e7e825f02054231a4f72f6a`


## Build (unguided)

We followed the build instructions in [`BUILDING.md`](https://github.com/microsoft/mystikos/blob/8a0487935abfdbb0662d755944e85200d7563763/BUILDING.md).

1. Install prerequesites:

    ```bash
    sudo apt update
    sudo apt install -y git make libmbedtls-dev docker.io
    sudo systemctl start docker
    sudo systemctl enable docker && sudo chmod 666 /var/run/docker.sock
    ```

2. Build from source code:

    ```bash
    git clone https://github.com/deislabs/mystikos.git
    cd mystikos
    git checkout e0c2698 # -> v0.13.0

    make
    sudo make install
    cp /opt/mystikos/lib/openenclave/mystenc.so .
    ```

3. Sign the enclave (with the same key that the release enclave was signed with):

    ```bash
    /opt/openenclave/bin/oesign sign -e mystenc.so --key-file ../../binaries_v0.13.0/key.pem
    ```

4. Extract the MRENCLAVE:

    ```bash
    /opt/openenclave/bin/oesign dump -e mystenc.so.signed > mystenc.sigstruct.hex
    cat mystenc.sigstruct.hex
    ```

**Retrieved measurement:** `64a64891668e01c1206611d0763205bce00b6d2113456c4cfd0c0cbe667ebdec`


### &rarr; NOT reproducible (unguided scenario)


---


# Non-determinism Root Cause Analysis

To rule out sources of non-determinism as root causes, we conducted the following checks:


### Timestamp-dependence

Rebuilding the enclave following the steps from above yields the same hash.

&rarr; **The binary does not include any timestamps and the compiler does not introduce randomness.**


### Location-dependence

Inspect whether the build path is included in the binary:

```bash
strings -n 10 /mystikos/lib/openenclave/mystenc.so | grep /path/to/build
```

The build path is included in binary &rarr; inspect the build path in the release binaries:
    - Checking the original files for paths including the term `mystikos` reveals that the build folder was likely `/__w/1/s/`.

        ```bash
        strings -n 10 ~/reference/opt/mystikos/lib/openenclave/mystenc.so | grep mystikos
        ```

Inspecting the output, the build folder was likely `/__w/1/s/`, so we rebuilt the enclave in this directory following the steps from above.

**Retrieved Measurement:** `858b01542ebf437159053bf9bff062e53059e4f8fbf76c031e9a36fd1d797621`

&rarr; **While the measurement is impacted by the build path, there are other additional factors preventing reproducibility.**


### Filesystem-dependence

The previous build was conducted on a device with an ext4 file system, which is the default selection when installing Ubuntu. We created two VMs with xfs and btrfs file systems, and built the enclave following the steps from above, but at the path extracted in the analysis for location-dependence.

**Retrieved Measurement (xfs):** `c931a5e0551dd866dc16982dd2511eaa73ad203851eb196285d822ea62a79fe`

**Retrieved Measurement (btrfs):** `c931a5e0551dd866dc16982dd2511eaa73ad203851eb196285d822ea62a79fe`

&rarr; **Both btrfs and xfs produce the same binaries, but differing from the binary built on ext4. While the measurement is impacted by the filesystem and build path, there are other additional factors preventing reproducibility.**
