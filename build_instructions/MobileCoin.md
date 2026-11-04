# MobileCoin v6.1.1

The following instructions were executed on Ubuntu 20.04 LTS with Docker installed.


## Reference Measurements

The reference measurements for both TestNet and MainNet are included in the [Tag comments](https://github.com/mobilecoinfoundation/mobilecoin/releases/tag/v6.1.1):

**MainNet reference measurements:**

| Enclave | Measurement |
|---------|-------------|
| `libconsensus-enclave.signed.so` | `82c14d06951a2168763c8ddb9c34174f7d2059564146650661da26ab62224b8a` |
| `libingest-enclave.signed.so` | `34881106254a626842fa8557e27d07cdf863083e9e6f888d5a492a456720916f` |
| `libview-enclave.signed.so` | `2f542dcd8f682b72e8921d87e06637c16f4aa4da27dce55b561335326731fa73` |
| `libledger-enclave.signed.so` | `2494f1542f30a6962707d0bf2aa6c8c08d7bed35668c9db1e5c61d863a0176d1` |

**TestNet reference measurements:**

| Enclave | Measurement |
|---------|-------------|
| `libconsensus-enclave.signed.so` | `ae7930646f37e026806087d2a3725d3f6d75a8e989fb320e6ecb258eb829057a` |
| `libingest-enclave.signed.so` | `4a5daa23db5efa4b18071291cfa24a808f58fb0cedce7da5de804b011e87cfde` |
| `libview-enclave.signed.so` | `44de03c2ba34c303e6417480644f9796161eacbe5af4f2092e413b4ebf5ccf6a` |
| `libledger-enclave.signed.so` | `065b1e17e95f2c356d4d071d434cea7eb6b95bc797f94954146736efd47057a7` |


## Build (unguided)

We followed the [build instructions](https://github.com/mobilecoinfoundation/mobilecoin/blob/v6.1.1/BUILD.md) for Dockerized builds on Ubuntu 20.04.

1. Download the source:

    ```bash
    git clone https://github.com/mobilecoinfoundation/mobilecoin.git
    cd mobilecoin/
    git checkout c18a291261e8829aab0f95b9c1d26774f34baa
    ```

2. Pull the Docker image and build the enclave:

    ```bash
    ./mob prompt --hw
    CARGO_BUILD_JOBS=1 SGX_MODE=HW cargo build --release
    ```

3. Extract the measurements from the signed enclaves:

    ```bash
    sgx_sign dump -enclave target/docker/release/libconsensus-enclave.signed.so -dumpfile libconsensus-enclave.sigstruct.hex
    cat libconsensus-enclave.sigstruct.hex
    # metadata->enclave_css.body.enclave_hash.m:
    # 0x5f 0x67 0xa3 0x43 0x79 0x14 0x8f 0xda 0x3e 0x28 0x68 0x9e 0x94 0x0a 0x33 0xc6 
    # 0xb5 0x4f 0x7c 0x5c 0xa1 0x44 0x39 0xf0 0xe9 0x58 0x84 0xf9 0xf9 0x66 0xb4 0x22 

    sgx_sign dump -enclave target/docker/release/libingest-enclave.signed.so -dumpfile libingest-enclave.sigstruct.hex
    cat libingest-enclave.sigstruct.hex
    # metadata->enclave_css.body.enclave_hash.m:
    # 0x0f 0x2c 0x33 0xe7 0xbf 0x26 0x93 0xb1 0x05 0xe7 0x3a 0xe0 0x57 0x52 0xb8 0x85 
    # 0x97 0x47 0x2d 0xa5 0xa4 0xd1 0xbe 0x3b 0xbc 0x8d 0x40 0xf6 0x5b 0x33 0x84 0x09  

    sgx_sign dump -enclave target/docker/release/libview-enclave.signed.so -dumpfile libview-enclave.sigstruct.hex
    cat libview-enclave.sigstruct.hex
    # metadata->enclave_css.body.enclave_hash.m:
    # 0x36 0xef 0xd1 0x6a 0x57 0x88 0xc7 0x43 0x17 0x01 0x7a 0x57 0x25 0xdd 0xc5 0xff 
    # 0x7a 0x9a 0x71 0x69 0xb5 0xc0 0x59 0x95 0x32 0x43 0x17 0x4f 0x79 0xad 0xa2 0x2b 


    sgx_sign dump -enclave target/docker/release/libledger-enclave.signed.so -dumpfile libledger-enclave.sigstruct.hex
    cat libledger-enclave.sigstruct.hex
    # metadata->enclave_css.body.enclave_hash.m:
    # 0x30 0x92 0x27 0x78 0x5f 0x32 0xfb 0xdd 0x0f 0xcd 0x84 0x96 0x12 0x17 0xea 0x5e 
    # 0x47 0x14 0x68 0x6b 0xff 0xe9 0xaf 0x66 0xf6 0xb8 0x63 0x62 0xf4 0x5a 0x2d 0x4d 
    ```

**Retrieved measurements:**

| Enclave | Measurement |
|---------|-------------|
| `libconsensus-enclave.signed.so` | `5f67a34379148fda3e28689e940a33c6b54f7c5ca14439f0e95884f9f966b422` |
| `libingest-enclave.signed.so` | `0f2c33e7bf2693b105e73ae05752b88597472da5a4d1be3bbc8d40f65b338409` |
| `libview-enclave.signed.so` | `36efd16a5788c74317017a5725ddc5ff7a9a7169b5c059953243174f79ada22b` |
| `libledger-enclave.signed.so` | `309227785f32fbdd0fcd84961217ea5e4714686bffe9af66f6b86362f45a2d4d` |

&rarr; *mismatch (all for both test and mainnet)*


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
strings target/docker/release/libledger-enclave.signed.so | grep mobilenode
```

The build path is included in binary, but the Docker container sets the build path automatically &rarr; inspect the build path in the release binaries:

```bash
mkdir ~/reference
cd ~/reference
wget https://github.com/mobilecoinfoundation/mobilecoin/releases/download/v7.1.0/mainnet-signed.so-v7.1.0.tar.gz
tar xzf mainnet-signed.so-v7.1.0.tar.gz 

sudo apt install binutils
strings mainnet-signed.so-v7.1.0/libledger-enclave.signed.so | grep mobilenode
```

&rarr; **The same build path is used in for both the release build and the one from source, thus cannot be the reason for the measurement mismatch.**


### Filesystem-dependence

The previous build was conducted on a device with an ext4 file system, which is the default selection when installing Ubuntu.We created two VMs with xfs and btrfs file systems, and built the enclave following the steps from above.

**Retrieved measurements (xfs):**

| Enclave | Measurement |
|---------|-------------|
| `libconsensus-enclave.signed.so` | `5f67a34379148fda3e28689e940a33c6b54f7c5ca14439f0e95884f9f966b422` |
| `libingest-enclave.signed.so` | `0f2c33e7bf2693b105e73ae05752b88597472da5a4d1be3bbc8d40f65b338409` |
| `libview-enclave.signed.so` | `36efd16a5788c74317017a5725ddc5ff7a9a7169b5c059953243174f79ada22b` |
| `libledger-enclave.signed.so` | `309227785f32fbdd0fcd84961217ea5e4714686bffe9af66f6b86362f45a2d4d` |

**Retrieved measurements (btrfs):**

| Enclave | Measurement |
|---------|-------------|
| `libconsensus-enclave.signed.so` | `5f67a34379148fda3e28689e940a33c6b54f7c5ca14439f0e95884f9f966b422` |
| `libingest-enclave.signed.so` | `0f2c33e7bf2693b105e73ae05752b88597472da5a4d1be3bbc8d40f65b338409` |
| `libview-enclave.signed.so` | `36efd16a5788c74317017a5725ddc5ff7a9a7169b5c059953243174f79ada22b` |
| `libledger-enclave.signed.so` | `309227785f32fbdd0fcd84961217ea5e4714686bffe9af66f6b86362f45a2d4d` |

&rarr; **Both btrfs and xfs produce the same binaries as the original build which was based on an ext4 file system. Thus, the file system cannot be the reason for the measurement mismatch.**
