# Linux SGX SDK 2.25

The latest version at the time of writing (2.28) does not sign all architectural enclaves. According to the [DCAP documentation](https://github.com/intel/confidential-computing.tee.dcap/blob/dcap_1.22_reproducible/QuoteGeneration/psw/ae/data/prebuilt/README.md), these prebuilt enclaves were signed in version 2.25, which is why we focus on this version.

We followed the official [reproducibility instructions](https://github.com/intel/confidential-computing.sgx/blob/sgx_2.25_reproducible/linux/reproducibility/README.md) on Ubuntu 24.04 LTS with Docker installed.

1. Install Nix:

    ```bash
    curl --proto '=https' --tlsv1.2 -L https://nixos.org/nix/install | sh
    ```

2. Download the reference enclaves from DCAP v1.22 (released on the same day as SGX 1.25):

    ```bash
    wget https://download.01.org/intel-sgx/sgx-dcap/1.22/linux/prebuilt_dcap_1.22.tar.gz
    tar xzf prebuilt_dcap_1.22.tar.gz
    ```

3. Download the source code:

    ```bash
    git clone https://github.com/intel/confidential-computing.sgx.git
    cd confidential-computing.sgx/
    git checkout sgx_2.25_reproducible
    ```

4. Build the artifacts.
 
    ```bash
    cd linux/reproducibility/
    ./build_and_launch_docker.sh --reproduce-type all --code-dir ./code_dir
    cd ../../../
    ```

4. Verify the produced artifacts following the official [verification instructions](https://github.com/intel/confidential-computing.sgx/blob/sgx_2.25/linux/reproducibility/ae_reproducibility_verifier/README.md):

    ```bash
    # generate user_private_key
    openssl genrsa -out my_priv_key.pem -3 3072
    mv my_priv_key.pem user_private_key

    # clone DCAP repo for config files
    git clone https://github.com/intel/confidential-computing.tee.dcap.git
    cd confidential-computing.tee.dcap/
    git checkout sgx_reproducible 

    # PCE
    ./confidential-computing.sgx/linux/reproducibility/ae_reproducibility_verifier/reproducibility_verifier.sh \
        </path/to/extracted/dcap/package>/psw/ae/data/prebuilt/libsgx_pce.signed.so \
        ./confidential-computing.sgx/linux/reproducibility/code_dir/out/ae/pce.so \
        user_private_key \
        ./confidential-computing.sgx/psw/ae/pce/config.xml \
        verification
    # Reproducibility Verification PASSED!

    # QVE
    ./confidential-computing.sgx/linux/reproducibility/ae_reproducibility_verifier/reproducibility_verifier.sh \
        </path/to/extracted/dcap/package>/psw/ae/data/prebuilt/libsgx_qve.signed.so \
        ./confidential-computing.sgx/linux/reproducibility/code_dir/out/ae/qve.so \
        user_private_key \
        confidential-computing.tee.dcap/QuoteVerification/QvE/Enclave/linux/config.xml \
        verification 
    # Reproducibility Verification PASSED!

    # QE3
    ./confidential-computing.sgx/linux/reproducibility/ae_reproducibility_verifier/reproducibility_verifier.sh \
        </path/to/extracted/dcap/package>/psw/ae/data/prebuilt/libsgx_qe3.signed.so \
        ./confidential-computing.sgx/linux/reproducibility/code_dir/out/ae/qe3.so \
        user_private_key \
        confidential-computing.tee.dcap/QuoteGeneration/quote_wrapper/quote/enclave/linux/config.xml \
        verification 
    # Reproducibility Verification PASSED!

    # IDE
    ./confidential-computing.sgx/linux/reproducibility/ae_reproducibility_verifier/reproducibility_verifier.sh \
        </path/to/extracted/dcap/package>/psw/ae/data/prebuilt/libsgx_id_enclave.signed.so \
        ./confidential-computing.sgx/linux/reproducibility/code_dir/out/ae/id_enclave.so \
        user_private_key \
        confidential-computing.tee.dcap/QuoteGeneration/quote_wrapper/quote/id_enclave/linux/config.xml \
        verification 
    # Reproducibility Verification PASSED!

    # TDQE
    ./confidential-computing.sgx/linux/reproducibility/ae_reproducibility_verifier/reproducibility_verifier.sh \
        </path/to/extracted/dcap/package>/psw/ae/data/prebuilt/libsgx_tdqe.signed.so \
        ./confidential-computing.sgx/linux/reproducibility/code_dir/out/ae/tdqe.so \
        user_private_key \
        confidential-computing.tee.dcap/QuoteGeneration/quote_wrapper/tdx_quote/enclave/linux/config.xml \
        verification 
    # Reproducibility Verification PASSED!
    ```

&rarr; *all enclaves pass reproducibility verification (i.e., have the same MRENCLAVE as the reference)*


### &rarr; reproducible (unguided scenario)
