# Oasis Sapphire ParaTime 1.2.0


## Build (unguided)

Following the instructions for the [runtime build](https://github.com/oasisprotocol/sapphire-paratime/blob/main/runtime/README.md) on Ubuntu 24.04 LTS with Docker installed.

1. Install prerequisites:

    ```bash
    sudo apt install -y rustup build-essential
    rustup show
    cargo install --locked fortanix-sgx-tools sgxs-tools

    sudo apt install -y build-essential clang cmake pkg-config libclang-dev libc6-dev gcc-multilib
    ```

2. Download the source code:

    ```bash
    # Checkout repository
    git clone https://github.com/oasisprotocol/sapphire-paratime.git
    cd sapphire-paratime/
    docker run -t -i -v ./sapphire-paratime:/src ghcr.io/oasisprotocol/runtime-builder:main /bin/bash
    ```

3. Build SGX binary:

    ```bash
    cd src/runtime
    rustup show
    cargo build --release --target x86_64-fortanix-unknown-sgx --locked
    cargo elf2sgxs --release
    ```

4. Extract measurement:

    ```bash
    # install the required tooling from the Oasis SDK
    cd /
    go install github.com/oasisprotocol/oasis-sdk/tools/orc@latest

    # bundle the binaries and metadata
    cd src/runtime
    openssl genrsa -3 3072 > private.pem
    openssl rsa -in private.pem -pubout > public.pem
    orc init target/x86_64-fortanix-unknown-sgx/release/sapphire-paratime --sgx-executable target/x86_64-fortanix-unknown-sgx/release/sapphire-paratime.sgxs
    orc sgx-gen-sign-data sapphire-paratime.orc
    orc sgx-gen-sign-data sapphire-paratime.orc > sigstruct.sha256.bin
    openssl pkeyutl -sign \
        -in sigstruct.sha256.bin \
        -inkey private.pem \
        -out sigstruct.sha256.sig \
        -pkeyopt digest:sha256
    orc sgx-set-sig sapphire-paratime.orc sigstruct.sha256.sig public.pem

    # Show metadata
    orc show sapphire-paratime.orc
    # Bundle:         sapphire-paratime.orc
    # Name:           sapphire-paratime
    # Runtime ID:     000000000000000000000000000000000000000000000000f80306c9858e7279
    # Version:        1.2.0
    # Components:
    # - ronl
    #   Executable:     ronl.elf
    #   TEE kind:       sgx
    #   SGXS:           ronl.sgx
    #   SGXS MRENCLAVE: 19140dc5596e0d8d9ada8f8b1c6145ff242a0189e49c2ae97a711166522dddf7
    #   SGXS signature: ronl.sgx.sig
    #   SGXS SIGSTRUCT:
    #     Build date:       2026-03-02 00:00:00 +0000 UTC
    #     MiscSelect:       00000000
    #     MiscSelect mask:  FFFFFFFF
    #     Attributes flags: 0000000000000004
    #       - 64-bit mode
    #     Attributes XFRM:  0000000000000003
    #     Attributes mask:  FFFFFFFFFFFFFFFD FFFFFFFFFFFFFFFC
    #     MRENCLAVE:        19140dc5596e0d8d9ada8f8b1c6145ff242a0189e49c2ae97a711166522dddf7
    #     MRSIGNER:         59fdf7b1353ed3b8ad2960db0e54ccdfe270823c88eaccc802ec6c0d9be9bffa
    #     ISV product ID:   0
    #     ISV SVN:          0
    # Digests:
    #   ronl.sgx => f90f652e10578731e4f5e8d8924581c357e7edb4fbfdebc39d1edf4651a4c7f7
    #   ronl.sgx.sig => e446b4338d117cf25125dccd7069ae46775981653669d3e15924c6d70448dbf7
    #   ronl.elf => 605751d1ddaa09ccfc2bee892b31e347967b34fb9e068e700d88361928062ccb
    ```

**Retrieved measurement:** `19140dc5596e0d8d9ada8f8b1c6145ff242a0189e49c2ae97a711166522dddf7`


## Reference Measurement

1. Download the reference bundle:

    ```bash
    # outside the container
    mkdir oasis_reference
    cd oasis_reference/
    wget https://github.com/oasisprotocol/sapphire-paratime/releases/download/v1.2.0/sapphire-paratime.orc
    wget https://github.com/oasisprotocol/sapphire-paratime/releases/download/v1.2.0/localnet-sapphire-paratime.orc
    cd ..
    ```

2. Restart the container and mount the new file:

    ```bash
    docker run -t -i -v ./sapphire-paratime:/src -v ./oasis_reference:/home ghcr.io/oasisprotocol/runtime-builder:main /bin/bash
    ```

3. Extract the measurement:
    ```bash
    cd /
    go install github.com/oasisprotocol/oasis-sdk/tools/orc@latest

    cd /home
    orc show sapphire-paratime.orc
    # Bundle:         sapphire-paratime.orc
    # Name:           sapphire-paratime
    # Runtime ID:     000000000000000000000000000000000000000000000000f80306c9858e7279
    # Version:        1.2.0
    # Components:
    # - ronl
    #   Executable:     ronl.elf
    #   TEE kind:       sgx
    #   SGXS:           ronl.sgx
    #   SGXS MRENCLAVE: e889fd2342de725cec0d85027fab9ed621fd570b2140432bf983dc65d01bb60e
    #   SGXS signature: ronl.sgx.sig
    #   SGXS SIGSTRUCT:
    #     Build date:       2025-12-17 00:00:00 +0000 UTC
    #     MiscSelect:       00000000
    #     MiscSelect mask:  FFFFFFFF
    #     Attributes flags: 0000000000000004
    #       - 64-bit mode
    #     Attributes XFRM:  0000000000000003
    #     Attributes mask:  FFFFFFFFFFFFFFFD FFFFFFFFFFFFFFFC
    #     MRENCLAVE:        e889fd2342de725cec0d85027fab9ed621fd570b2140432bf983dc65d01bb60e
    #     MRSIGNER:         ff7286273527bfe0a55891473bac3b3f33fce4fb9aebf9f1f7c45d6d2a3ee7e0
    #     ISV product ID:   0
    #     ISV SVN:          0
    # Digests:
    #   ronl.elf => 602d341203bead7c09dc2421e6c04a5f720420a17d64a6d5a86a5072e8588355
    #   ronl.sgx => 79c45fc747e42932a4b18f0156152965a159915d7235677b8cf28578c6bccbe5
    #   ronl.sgx.sig => 08710caec96e102fa58c1d572eef427b010c7c96a35c0d5000531e14991d1337
    ```

**Reference measurement:** `e889fd2342de725cec0d85027fab9ed621fd570b2140432bf983dc65d01bb60e`


### &rarr; NOT reproducible (unguided scenario)
