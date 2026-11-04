# Ternoa-enclaves v0.4.5

The following instructions were executed on Ubuntu 22.04 LTS.


## Reference Measurement

The reference measurement is provided in a file in the release:

```bash
wget https://github.com/capsule-corp-ternoa/ternoa-enclaves/releases/download/v0.4.5-mainnet/MRENCLAVE
cat MRENCLAVE
```
**Reference measurement:** `2cc556498647fe99c892fef29ca9b6d185675841b6723adb1f5934b3371a2b03`


## Build (unguided)

The following instructions were executed on Ubuntu 22.04 LTS with Docker installed.

1. Download source code:

    ```bash
    git clone https://github.com/capsule-corp-ternoa/ternoa-enclaves.git
    cd ternoa-enclaves
    git checkout 87a20faa13bd1c3a3d04dd5cdb094fd2c9c68e7d
    ```

2. Install dependencies:

    ```bash
    # Rust
    sudo apt install clang llvm pkg-config nettle-dev libssl-dev openssl dkms
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

    # Gramine (latest version before release)
    wget https://github.com/gramineproject/gramine/releases/download/v1.5/gramine_1.5_amd64.deb
    sudo apt-get install ./gramine_1.5_amd64.deb 

    # Additional dependencies
    sudo apt install python3-pip -y
    pip install --upgrade pip
    pip install "scalecodec>=1.1.2"
    pip install substrate-interface
    pip install base58

    sudo apt install jq -y

    sudo -- bash -c 'curl --proto "=https" --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y && source "$HOME/.cargo/env" && rustup update'

    sudo apt-get update
    sudo apt-get install gramine -y     # (gramine version 1.8)
    is-sgx-available
    gramine-sgx-gen-private-key
    sudo cp -r ~/.config /root/

3. Copy *.scale files from `ternoa_enclave/artifacts` (release) to `ternoa-enclaves/artifacts`

4. Extract measurements for all chains:

    ```bash
    sudo CHAIN="dev0" ./scripts/build-binary.sh mainnet
    sudo gramine-sgx-sign         --manifest sgx_server.manifest         --output sgx_server.manifest.sgx
    # 5d00dac897680b17f1f26fba422f93d6d97c041a90f836dfcea48bde31121fce

    sudo ./scripts/build-binary.sh alphanet
    sudo gramine-sgx-sign         --manifest sgx_server.manifest         --output sgx_server.manifest.sgx
    # 5dd40b1aeea38b74a6d72025ee8b77417b0e435bfa11abacc4dfb528752b4793

    sudo ./scripts/build-binary.sh dev0
    sudo gramine-sgx-sign         --manifest sgx_server.manifest         --output sgx_server.manifest.sgx
    # 74e218c65e41b0e4f1f8d93a1302ee03e19824a66882d4e3e1e48c8cd2a2520f

    sudo ./scripts/build-binary.sh dev1
    sudo gramine-sgx-sign         --manifest sgx_server.manifest         --output sgx_server.manifest.sgx
    # 20febd40e85f73f0beb0cba9bc3ea6e61c3a4e3599e9bf54490a600a8ba26c15
    ```

**Retrieved measurements:**
- mainnet: `5d00dac897680b17f1f26fba422f93d6d97c041a90f836dfcea48bde31121fce` &rarr; *mismatch*
- alphanet: `5dd40b1aeea38b74a6d72025ee8b77417b0e435bfa11abacc4dfb528752b4793` &rarr; *mismatch*
- dev0: `74e218c65e41b0e4f1f8d93a1302ee03e19824a66882d4e3e1e48c8cd2a2520f` &rarr; *mismatch*
- dev1: `20febd40e85f73f0beb0cba9bc3ea6e61c3a4e3599e9bf54490a600a8ba26c15` &rarr; *mismatch*


## Build (guided)

*The developers informed us that they are aware that the build is influenced by dependencies they have not yet identified.*


### &rarr; NOT reproducible (guided scenario)
