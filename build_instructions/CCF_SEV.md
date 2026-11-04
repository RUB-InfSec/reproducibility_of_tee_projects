# Azure CCF 6.0.23

We followed the instructions for [reproducible builds](https://ccf.dev/main/audit/reproducible_build.html) (accessed: 2026-02-26) on Ubuntu 24.04 LTS with Docker installed.


1. Build the packages:

    ```bash
    export CCF_VERSION=6.0.23
    wget https://github.com/microsoft/CCF/releases/download/ccf-${CCF_VERSION}/reproduce-snp.json
    wget https://github.com/microsoft/CCF/releases/download/ccf-${CCF_VERSION}/start_container_and_reproduce_rpm.sh
    chmod +x ./start_container_and_reproduce_rpm.sh
    sudo ./start_container_and_reproduce_rpm.sh reproduce-snp.json
    ```

2. Compare the package measurements:

    ```bash
    wget https://github.com/microsoft/CCF/releases/download/ccf-${CCF_VERSION}/ccf_snp_devel_${CCF_VERSION}_x86_64.rpm
    cmp ./ccf_snp_devel_${CCF_VERSION}_x86_64.rpm ./reproduced/ccf_snp_devel_${CCF_VERSION}_x86_64.rpm
    ```
    
    The output confirms that the packages are reproducible:
    
    ```
    # packages reproducible
    sha256sum ccf_snp_devel_6.0.23_x86_64.rpm 
    # 1e10378aec73ef8928ced17a0a4be1aecc7e0fc9220098b53214a5532991fa75  ccf_snp_devel_6.0.23_x86_64.rpm
    sha256sum reproduced/ccf_snp_devel_6.0.23_x86_64.rpm 
    # 1e10378aec73ef8928ced17a0a4be1aecc7e0fc9220098b53214a5532991fa75  reproduced/ccf_snp_devel_6.0.23_x86_64.rpm
    ```

    **Reference measurement:** `1e10378aec73ef8928ced17a0a4be1aecc7e0fc9220098b53214a5532991fa75`

    **Retrieved measurement:** `1e10378aec73ef8928ced17a0a4be1aecc7e0fc9220098b53214a5532991fa75`


### &rarr; reproducible (unguided scenario)
