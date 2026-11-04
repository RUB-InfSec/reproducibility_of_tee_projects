# Asylo 0.30.0

We follow the instructions provided on [Github](https://github.com/google/asylo#running-the-hello_world-example) and build on Ubuntu 22.04 LTS inside the pre-built Docker.

**Note: Asylo has been archived by the owner (Google) on Apr 17, 2026. The Docker image to retrieve the reference measurement is no longer available.**

## Reference Measurement

1. Pull and run the Docker image:
   
    ```bash
    docker pull  gcr.io/asylo-framework/asylo
    MY_PROJECT=~/asylo-examples
    git clone https://github.com/google/asylo-examples.git "${MY_PROJECT}"
    docker run -it --rm --device /dev/sgx/enclave --device /dev/sgx/provision     -v bazel-cache:/root/.cache/bazel     -v "${MY_PROJECT}":/opt/my-project     -w /opt/my-project     gcr.io/asylo-framework/asylo
    ```

2. Build and extract the measurement:

    ```bash
    bazel build //hello_world:hello_world_sgx_hw
    cat /opt/my-project/metadata_info.txt
    # metadata->enclave_css.body.enclave_hash.m:
    # 0xbb 0x65 0x8e 0xdd 0x31 0x82 0xf0 0xab 0xc7 0xb3 0x2f 0x8a 0x77 0x55 0x07 0x06 
    # 0x1e 0x5b 0xa7 0x7c 0xa2 0x00 0xa9 0xfd 0xc8 0x21 0xe9 0x06 0x75 0xdc 0x3d 0xf9 
    ```

**Reference Measurement:** `bb658edd3182f0abc7b32f8a775507061e5ba77ca200a9fdc821e90675dc3df9` 


## Built from Source

1. Clone the repo from GitHub and build the Docker image:

   ```bash
    git clone https://github.com/google/asylo.git
    cd asylo/asylo/distrib/toolchain
    docker build -t my-asylo-image .
    docker run -it --rm \  -v bazel-cache:/root/.cache/bazel -v "$MY_PROJECT":/opt/my-project -w /opt/my-project my-asylo-image
    ```

2. Build and extract the mrenclave
    ```bash
    bazel build //hello_world:hello_world_sgx_hw
    sgx_sign=$(find $(bazel info execution_root) -name sgx_sign_tool -executable | head -1)
    $sgx_sign dump -enclave bazel-bin/hello_world/hello_enclave_sgx_hw.so -dumpfile /tmp/dump.txt
    grep -A 2 enclave_hash /tmp/dump.txt
    # metadata->enclave_css.body.enclave_hash.m:
    # 0x64 0xb2 0x41 0x98 0x93 0xfb 0xa4 0x5b 0x6a 0x0c 0x83 0xfa 0xb6 0x53 0x4d 0x9d 
    # 0xf9 0x7b 0xe4 0xb0 0x52 0x9a 0x8b 0xa5 0x7e 0xf1 0x60 0x68 0x98 0x56 0x5f 0xa3 
    ```

**Retrieved Measurement:** `64b2419893fba45b6a0c83fab6534d9df97be4b0529a8ba57ef1606898565fa3` 


### &rarr; NOT reproducible (unguided scenario)
