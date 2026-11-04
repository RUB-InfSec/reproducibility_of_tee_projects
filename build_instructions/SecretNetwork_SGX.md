# Secret Network v1.14.0

The following instructions were executed on Ubuntu 20.04 LTS with Docker installed.


## Reference Measurement

1. Download mainnet release package:

    ```bash
    wget https://github.com/scrtlabs/SecretNetwork/releases/download/v1.14.0/secretnetwork_1.14.0_mainnet_goleveldb_amd64.deb
    ```

2. Extract binaries:

    ```bash
    dpkg-deb -xv secretnetwork_1.14.0_mainnet_goleveldb_amd64.deb .
    ```

3. Extract enclave measurement:

    ```bash
    sgx_sign dump -enclave ./usr/lib/librust_cosmwasm_enclave.signed.so -dumpfile librust_cosmwasm_enclave.sigstruct.hex
    cat librust_cosmwasm_enclave.sigstruct.hex
    # metadata->enclave_css.body.enclave_hash.m:
    # 0xfc 0x78 0xc1 0xf0 0x81 0x0c 0x19 0xc9 0xf7 0xd8 0x2d 0x9f 0x07 0x47 0xac 0xb3 
    # 0xe5 0x1c 0x34 0x68 0xc4 0x2f 0x4f 0x37 0x53 0x14 0x30 0x09 0xd6 0x7d 0x00 0x24 
    ```

**Reference measurement:** `fc78c1f0810c19c9f7d82d9f0747acb30xe51c3468c42f4f3753143009d67d0024`


## Build (unguided)

Following the instructions for [Docker environments](https://github.com/scrtlabs/SecretNetwork/tree/v1.14.0#docker-dev-environments).

1. Get source code:

    ```bash
    git clone https://github.com/scrtlabs/SecretNetwork
    cd SecretNetwork
    git checkout v1.14.0
    git submodule init
    git submodule update --remote
    ```

2. Compile and extract the mainnet binaries:

    ```bash
    touch api_key.txt
    touch spid.txt
    make build-mainnet
    cd build
    dpkg-deb -xv secretnetwork_1.14.0_amd64.deb .
    ```

3. Extract enclave measurement:

    ```bash
    sgx_sign dump -enclave ./usr/lib/librust_cosmwasm_enclave.signed.so -dumpfile librust_cosmwasm_enclave.sigstruct.hex
    cat librust_cosmwasm_enclave.sigstruct.hex
    # metadata->enclave_css.body.enclave_hash.m:
    # 0x66 0x2e 0xa0 0xa0 0x4c 0x8f 0x8d 0x4d 0x28 0xdd 0x2b 0xad 0x7b 0xa0 0xfa 0xef 
    # 0x7b 0xc8 0xfc 0xb5 0x2b 0xb4 0xae 0xf0 0x82 0xb7 0x99 0x73 0x2e 0x34 0xe8 0xe7  
    ```

**Retrieved measurement:** `662ea0a04c8f8d4d28dd2bad7ba0faef7bc8fcb52bb4aef082b799732e34e8e7` &rarr; *mismatch*


## Build (guided)

*The [repository](https://github.com/scrtlabs/SecretNetwork#build-from-source) states that production builds must copy the enclave from the most recent release due to non-reproducible builds, which was also confirmed by the developers.*


### &rarr; NOT reproducible (guided scenario)
