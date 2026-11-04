# Cosmian KMS 5.17.0

We followed the [official build instructions](https://github.com/Cosmian/kms/tree/5.17.0?tab=readme-ov-file#-packaging-debrpmdmg-and-hashes) on Ubuntu 24.04 LTS.


## Reference Measurement

1. Download the references (`*.sha256` files) from the [GitHub release](https://github.com/Cosmian/kms/releases/tag/5.17.0):

    ```bash
    wget https://github.com/Cosmian/kms/releases/download/5.17.0/cosmian-kms-cli-fips-static-openssl_5.17.0_amd64.deb.sha256
    wget https://github.com/Cosmian/kms/releases/download/5.17.0/cosmian-kms-cli-fips-static-openssl_5.17.0_x86_64.rpm.sha256
    wget https://github.com/Cosmian/kms/releases/download/5.17.0/cosmian-kms-cli-non-fips-static-openssl_5.17.0_amd64.deb.sha256
    wget https://github.com/Cosmian/kms/releases/download/5.17.0/cosmian-kms-cli-non-fips-static-openssl_5.17.0_x86_64.rpm.sha256
    wget https://github.com/Cosmian/kms/releases/download/5.17.0/cosmian-kms-server-fips-static-openssl_5.17.0_amd64.deb.sha256
    wget https://github.com/Cosmian/kms/releases/download/5.17.0/cosmian-kms-server-fips-static-openssl_5.17.0_x86_64.rpm.sha256
    wget https://github.com/Cosmian/kms/releases/download/5.17.0/cosmian-kms-server-non-fips-static-openssl_5.17.0_amd64.deb.sha256
    wget https://github.com/Cosmian/kms/releases/download/5.17.0/cosmian-kms-server-non-fips-static-openssl_5.17.0_x86_64.rpm.sha256
    ```
   
2. Extract the reference measurements by running `cat *.sha256`.

**Reference measurements:**

| Package | Measurement |
|---------|-------------|
| `cosmian-kms-cli-fips-static-openssl_5.17.0_amd64.deb` | `d6e4c2da54bfe23d177c6c745c698a8335232738e03e15038ef1dbab9bfeb951`| 
| `cosmian-kms-cli-fips-static-openssl_5.17.0_x86_64.rpm` | `8597a10307a9ff9adf0b230ce2fcf00c69a834d65ddd22f566881cff1ecfab64` |
| `cosmian-kms-cli-non-fips-static-openssl_5.17.0_amd64.deb` | `422468987b5aaf8e46b1f68101a2912c26df0381b79b69454fda6ae35ee8ae17` |
| `cosmian-kms-cli-non-fips-static-openssl_5.17.0_x86_64.rpm` | `00754621f4b15a3a59d4bb0d15c1c60499316d8141678ee4ecd127fc0b456fd7` |
| `cosmian-kms-server-fips-static-openssl_5.17.0_amd64.deb` | `1f45825fe490877920574f6e5ebc47a3f8d63e5454eed9374d6af37640b791a5` |
| `cosmian-kms-server-fips-static-openssl_5.17.0_x86_64.rpm` | `1b445a3be996ac7a6c92599cbeef9e13f8d9b23339e2a30da03bba0654920880` |
| `cosmian-kms-server-non-fips-static-openssl_5.17.0_amd64.deb` | `90eddf89804897fe18619685a694a837bbc49ddbe6ccebeaeef3249c6c4caea0` |
| `cosmian-kms-server-non-fips-static-openssl_5.17.0_x86_64.rpm` | `7c47044276ea431b048cef07012000a4ba40bd613fa6d1d0671e60bf0fd60dba` |

## Build (unguided) 

1. Instll Nix:

    ```bash
    # Install Nix
    sh <(curl --proto '=https' --tlsv1.2 -L https://nixos.org/nix/install) --daemon
    # say yes for every question 
    source /nix/var/nix/profiles/default/etc/profile.d/nix-daemon.sh
    ```

2. Build with Nix following the [official build instructions](https://github.com/Cosmian/kms/tree/5.17.0?tab=readme-ov-file#-packaging-debrpmdmg-and-hashes):

    ```bash
    git clone https://github.com/Cosmian/kms.git
    cd kms
    git checkout 5.17.0
    git submodule update --init --recursive
    bash .github/scripts/nix.sh package
    ```

3. Extract measurements by running `cat result-deb-*/*.sha256` and `cat result-rpm-*/*.sha256`.

**Retrieved measurements:**

| Package | Measurement |
|---------|-------------|
| `cosmian-kms-cli-fips-static-openssl_5.17.0_amd64.deb` | `d6e4c2da54bfe23d177c6c745c698a8335232738e03e15038ef1dbab9bfeb951` |
| `cosmian-kms-server-fips-static-openssl_5.17.0_amd64.deb` | `1f45825fe490877920574f6e5ebc47a3f8d63e5454eed9374d6af37640b791a5` |
| `cosmian-kms-cli-non-fips-static-openssl_5.17.0_amd64.deb`| `422468987b5aaf8e46b1f68101a2912c26df0381b79b69454fda6ae35ee8ae17` |
| `cosmian-kms-server-non-fips-static-openssl_5.17.0_amd64.deb` | `90eddf89804897fe18619685a694a837bbc49ddbe6ccebeaeef3249c6c4caea0` |
| `cosmian-kms-cli-fips-static-openssl_5.17.0_x86_64.rpm` | `8597a10307a9ff9adf0b230ce2fcf00c69a834d65ddd22f566881cff1ecfab64` |
| `cosmian-kms-server-fips-static-openssl_5.17.0_x86_64.rpm` | `1b445a3be996ac7a6c92599cbeef9e13f8d9b23339e2a30da03bba0654920880` |
| `cosmian-kms-cli-non-fips-static-openssl_5.17.0_x86_64.rpm` | `00754621f4b15a3a59d4bb0d15c1c60499316d8141678ee4ecd127fc0b456fd7` |
| `cosmian-kms-server-non-fips-static-openssl_5.17.0_x86_64.rpm` | `7c47044276ea431b048cef07012000a4ba40bd613fa6d1d0671e60bf0fd60dba` |


### &rarr; reproducible (unguided scenario)
