# Phala v1263

The following instructions were executed on Ubuntu 20.04 LTS. **Note that the link we retrieved the reference from is no longer valid.**


## Reference Measurement

Reference measurement is the code hash from the last successful [pruntime upgrade](https://phala.subsquare.io/democracy/referenda/60).

**Reference meeasurement:** `c948b508aa5043175b1ba803e8eaffe63482088316ce60312d3805fe4a685d48`


## Build (unguided)

1. Download source code:

    ```bash
    git clone https://github.com/Phala-Network/phala-blockchain.git
    cd phala-blockchain
    git checkout 536f4aacd99815ebf64708eb40890319adb6fb05
    git submodule update --init
    ```

2. Install dependencies:

    ```bash
    # install deps
    sudo apt install -y build-essential pkg-config libssl-dev protobuf-compiler

    # install Rust
    curl https://sh.rustup.rs -sSf | sh

    # REMBER:
    source "$HOME/.cargo/env"

    # install substrate deps
    git submodule update --init
    sh ./scripts/init.sh

    # install llvm-14
    wget https://apt.llvm.org/llvm.sh
    chmod +x llvm.sh
    sudo ./llvm.sh 14
    ```

3. Compile the project following the [build instructions](https://github.com/Phala-Network/phala-blockchain/tree/536f4aacd99815ebf64708eb40890319adb6fb05#native-build).

    ```bash
    cargo build --release
    ```

4. Extract the MRENCLAVE:

    ```bash
    cd standalone/pruntime/gramine-build/
    make signed-sgx-artifacts
    ```

**Retrieved measurement:** `ec8dfea22f40d3dff8bf32e36a32310f8e2b694635c0f2bd3e22205768e68635` &rarr; *mismatch*

### &rarr; NOT reproducible (unguided scenario)