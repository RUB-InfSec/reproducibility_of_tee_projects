# Secret VM v0.0.25

We followed the instructions for [full verification](https://docs.scrt.network/secret-network-documentation/secretvm-confidential-virtual-machines/verifying-a-secretvm/full-verification) (accessed: 2026-04-14) on Ubuntu 24.04 LTS.

**Note: The GitHub repository for the Secret VM is no longer available.**


## Build (unguided)

1. Install measurement tools

    - **TDX:** [Tool](https://github.com/scrtlabs/reproduce-mr) for TDX to calculate TDX measurements for SecretVM images.

        ```bash
        go install github.com/scrtlabs/reproduce-mr@latest
        echo 'export PATH=$PATH:$HOME/go/bin' >> ~/.bashrc
        source ~/.bashrc
        ```

    - **SEV-SNP:** 

        ```bash
        pip install sev-snp-measure

        sev-snp-measure --version
        # sev-snp-measure 0.0.12
        ```

2. Download source code:

    ```bash
    git clone https://github.com/scrtlabs/secret-vm-build.git
    cd secret-vm-build/
    git checkout v0.0.25 
    git submodule update --init
    ```

3. Compile the project:

    ```bash
    mkdir -p build
    mkdir -p artifacts
    chmod -R 777 build artifacts scripts
    scripts/build_reproducible.sh
    ```

4. Extract the measurements:

    - **TDX:**

        ```bash
        reproduce-mr -fw artifacts/tdx/ovmf.fd -kernel artifacts/tdx/bzImage -initrd artifacts/tdx/initramfs.cpio.gz  -dockercompose config/docker-compose.yaml -rootfs artifacts/tdx/rootfs-prod.iso -memory 2G -cpu 1 -cmdline "console=ttyS0 loglevel=7 clearcpuid=mtrr,rtmr ro initrd=initrd" --templates ../../ref/reproduce-mr/internal/ --tcbver 7
        ```

    - **SEV-SNP:**

        ```bash
        sev-snp-measure --mode snp --ovmf artifacts/sev/ovmf.fd --kernel artifacts/sev/bzImage --initrd artifacts/sev/initramfs.cpio.gz --vcpus 1 --vcpu-type EPYC
        ```

**Retrieved measurements (TDX):**

| Register | Measurement |
|----------|-------------|
| `MRTD` |`1e305ac8284517f73ada985bfc9fded48b23ed091ba8149678bb10207fb470c7903d7a8ddffa5a7be2a60e349bb75b6e` |
| `RTMR0` | `10b35f76c23b60129901c4d6b7d30b87108f7b0e088b97f4f15426cd39f727739291ea2b9e2ba304809696750f0a652b` |
| `RTMR1` | `cde0e9e871696347a35b33e92c2e70561f749cf203b2b23a55243bdfd6e19a1de2e45bbaaab33a34e43a65f9463423c8` |
| `RTMR2` | `591510d4623ad99f819016c5ba3cbcf162dd83c253fcc1412cfabf17f1376b6bb0b7a49679a5d2f99a47f3ee8f126969` |
| `RTMR3` | `96473da69e7c44e50b3a74ecc97a842ac7f2f2806bb3ed95e66483f8c79ff1af61bee8eec193baa666506bad5a2d85f0` |

**Retrieved measuremnt (SEV-SNP):**
`d5967d2ca1939d1a1004b9f3246da896146ed2c5f5bb99205645d20ad3f84ea680770457e53900c4822bc97717848928`


## Reference Measurement

1. Download release binaries:

    ```bash
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/bzImage-v0.0.25-sev
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/bzImage-v0.0.25-tdx
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/initramfs-v0.0.25-sev.cpio.gz
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/initramfs-v0.0.25-tdx.cpio.gz
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/ovmf-v0.0.25-sev.fd
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/ovmf-v0.0.25-tdx.fd
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/rootfs-dev-v0.0.25-sev.iso
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/rootfs-dev-v0.0.25-tdx.iso
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/rootfs-gpu-dev-v0.0.25-tdx.iso
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/rootfs-gpu-prod-v0.0.25-tdx.iso
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/rootfs-prod-v0.0.25-sev.iso
    wget https://github.com/scrtlabs/secret-vm-build/releases/download/v0.0.25/rootfs-prod-v0.0.25-tdx.iso
    ```

2. Extract measurements:

    - **TDX:**

        ```bash
        # fetch templates
        git clone https://github.com/scrtlabs/reproduce-mr.git
        cd reproduce-mr/
        git checkout d448cc1

        cp </path/to/secret-vm-build>/config/docker-compose.yaml .
        reproduce-mr -fw ovmf-v0.0.25-tdx.fd -kernel bzImage-v0.0.25-tdx -initrd initramfs-v0.0.25-tdx.cpio.gz  -dockercompose docker-compose.yaml -rootfs rootfs-prod-v0.0.25-tdx.iso -memory 2G -cpu 1 -cmdline "console=ttyS0 loglevel=7 clearcpuid=mtrr,rtmr ro initrd=initrd" --templates reproduce-mr/internal/ --tcbver 7
        ```

    - **SEV-SNP:**

        ```bash
        sev-snp-measure --mode snp --ovmf ovmf-v0.0.25-sev.fd --kernel bzImage-v0.0.25-sev --initrd initramfs-v0.0.25-sev.cpio.gz --vcpus 1 --vcpu-type EPYC
        ```

**Reference measurements (TDX):**

| Register | Measurement |
|----------|-------------|
| `MRTD` |`1e305ac8284517f73ada985bfc9fded48b23ed091ba8149678bb10207fb470c7903d7a8ddffa5a7be2a60e349bb75b6e` |
| `RTMR0` | `10b35f76c23b60129901c4d6b7d30b87108f7b0e088b97f4f15426cd39f727739291ea2b9e2ba304809696750f0a652b` |
| `RTMR1` | `2db7eacca93100fb7199b055f55e8fd3a52f2153e25696f937745ff7f254d1be9cf37223237375dec74e685250b273ab` |
| `RTMR2` | `be378f0c4f1180fde14d8bb0a13a9dd430c842b135be23acd7b09cd6181550b6f8adb36588eabdc10be9325d92a8e055` |
| `RTMR3` | `c2c9aab74432e3cfac21c957fbecb81d9d175b5ea0e0bb4adf9834751e8fd7229566ae3cb73cb52d1cfacb37e4678417` |

&rarr; `MRTD`: *match*; `RTMR0`: *match*; `RTMR1`: *mismatch*; `RTMR2`: *mismatch*; `RTMR3`: *mismatch*;

**Reference measuremnt (SEV-SNP):** `d5967d2ca1939d1a1004b9f3246da896146ed2c5f5bb99205645d20ad3f84ea680770457e53900c4822bc97717848928` &rarr; *match*

### &rarr; SEV-SNP build reproducible (unguided scenario)
### &rarr; TDX build NOT reproducible (unguided scenario)
