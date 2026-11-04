# Azure CCF 5.0.6

CCF currently does not support SGX. For this paper, we used CCF version 5.0.6, which was available for SGX. We used the Docker image to compare the measurement of `libjs_generic.enclave.so.signed`, executing all instructions on Ubuntu 24.04 LTS. Note that the build requires an SGX capable machine or VM.


## Build (Guided)

1. Download the Docker image:
   
    ```bash
    docker pull ghcr.io/microsoft/ccf/ci/sgx:build-25-07-2024
    ```

2. Run the Docker container:
   
    ```bash
    docker run -it --device /dev/sgx_enclave:/dev/sgx_enclave --device /dev/sgx_provision:/dev/sgx_provision -v /dev/sgx:/dev/sgx ghcr.io/microsoft/ccf/ci/sgx:build-25-07-2024 bash
    ```

3. Install CCF inside Docker:
   
    ```bash
    mkdir -p /__w/CCF
    git clone https://github.com/microsoft/CCF.git
    git checkout ccf-5.0.6
    mkdir build
    cd build/
    cmake -GNinja -DCOMPILE_TARGET=sgx -DLVI_MITIGATIONS=ON -DCLIENT_PROTOCOLS_TEST=ON ..
    ninja
    /opt/openenclave/bin/oesign dump --enclave-image /__w/CCF/CCF/build/libjs_generic.enclave.so.signed
    ```

    Output should look like:
   
    ```bash
    === Entry point:
    name=_start
    address=0x00000000169e77

    === SGX Enclave Properties:
    product_id=1
    security_version=1
    debug=0
    xfrm=0000000000000000
    num_heap_pages=524288
    num_stack_pages=1024
    num_tcs=14
    mrenclave=d3fe9b261eb2a58e5869096f5e8bb54822caaaf169408d4c6c8250399836177c
    ```

    Note that the build in the unguided scenario was performed in the container's root directory, yielding a different hash.

**Retrieved measurement:** `d3fe9b261eb2a58e5869096f5e8bb54822caaaf169408d4c6c8250399836177c`


## Reference Measurement

Inside the Docker container, download and extract the release package: 

```bash
wget https://github.com/microsoft/CCF/releases/download/ccf-5.0.6/ccf_sgx_5.0.6_amd64.deb
mkdir deb
dpkg-deb -R ccf_sgx_5.0.6_amd64.deb deb
/opt/openenclave/bin/oesign dump --enclave-image deb/opt/ccf_sgx/lib/libjs_generic.enclave.so.signed
```

Output should look like:

```bash
=== Entry point:
name=_start
address=0x00000000169e77

=== SGX Enclave Properties:
product_id=1
security_version=1
debug=0
xfrm=0000000000000000
num_heap_pages=524288
num_stack_pages=1024
num_tcs=14
mrenclave=d3fe9b261eb2a58e5869096f5e8bb54822caaaf169408d4c6c8250399836177c
```
**Reference measurement:** `d3fe9b261eb2a58e5869096f5e8bb54822caaaf169408d4c6c8250399836177c`


### &rarr; reproducible (guided scenario)
