# TamaGo 1.26.2

The following instructions were executed on Ubuntu 24.04 LTS

## Reference Measurement

1. Download `ovmf` file for SEV:

    ```bash
    wget http://ftp.de.debian.org/debian/pool/main/e/edk2/ovmf-amdsev_2025.11-5_all.deb
    mkdir ovmf-amdsev
    dpkg -x ovmf-amdsev_2025.11-5_all.deb ovmf-amdsev
    ```

2. Install TamaGo-Go from release:

    ```bash
    wget https://github.com/usbarmory/tamago-go/releases/download/tamago-go1.26.2/tamago-go1.26.2.linux-amd64.tar.gz
    tar xzf tamago-go1.26.2.linux-amd64.tar.gz
    ```

3. Download and build SEV-SNP example:

    ```bash
    git clone https://github.com/usbarmory/tamago-sev-example.git
    cd tamago-sev-example/
    git checkout 457644c6632641ef87a9b7b7f40f179315f5f37c
    TAMAGO=</path/to/tamago/release>/usr/local/tamago-go/bin/go \
    make efi IMAGE_BASE=10000000
    cd ..
    ```

4. Install sev-snp-measure tool:

    ```bash
    sudo apt install -y python3-pip python3.12-venv
    python3 -m venv .venv
    source .venv/bin/activate
    pip install sev-snp-measure
    ```

5. Extract measurement:

    ```bash
    sev-snp-measure \
    --mode snp \
    --vcpus 1 \
    --vcpu-type EPYC-v4 \
    --ovmf ovmf-amdsev/usr/share/ovmf/OVMF.amdsev.fd \
    --kernel tamago-sev-example/tamago-sev-example.efi \
    --append="console=ttyS0" \
    --output-format hex
    ```

**Reference measurement:** `fda4ac7db99639cb14cc008b31a506e4cbb274bd5e14004a2e75779218c4c317435a2460186ad8e0a01f63322625e89b`


## Build (unguided)

1. Download TamaGo-Go source ("latest" is version 1.26.2 at the time of writing):

    ```bash
    wget https://github.com/usbarmory/tamago-go/archive/refs/tags/latest.zip
    unzip latest.zip
    cd tamago-go-latest/src && ./all.bash
    cd ../bin && export TAMAGO=`pwd`/go
    cd ../../
    ```

2. Download and build SEV-SNP example:

    ```bash
    git clone https://github.com/usbarmory/tamago-sev-example.git
    cd tamago-sev-example/
    git checkout 457644c6632641ef87a9b7b7f40f179315f5f37c
    TAMAGO=</path/to/tamago/build>/tamago-go-latest/bin/go \
    make efi IMAGE_BASE=10000000
    cd ..
    ```

4. Extract measurement:

    ```bash
    sev-snp-measure \
    --mode snp \
    --vcpus 1 \
    --vcpu-type EPYC-v4 \
    --ovmf /path/to/ovmf-amdsev/usr/share/ovmf/OVMF.amdsev.fd \
    --kernel tamago-sev-example/tamago-sev-example.efi \
    --append="console=ttyS0" \
    --output-format hex
    ```

**Retrieved measurement:** `fda4ac7db99639cb14cc008b31a506e4cbb274bd5e14004a2e75779218c4c317435a2460186ad8e0a01f63322625e89b`


### &rarr; reproducible (unguided scenario)
