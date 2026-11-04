# MongoDB

The following instructions were executed on Ubuntu 24.04 LTS with Docker installed. Note that the build requires an SGX capable machine or VM.


## Reference Measurement

```bash
docker pull enclaive/mongodb-sgx:latest

docker run -it --device /dev/sgx_enclave --device /dev/sgx_provision enclaive/mongodb-sgx:latest
# Attributes:
#     mr_enclave:  f9df5d816a5884b4ffe8eb3cf6d44ec7c2611c3916de3242ad0b5ce02fbf9456
#     mr_signer:   fe2e0fd23973a6128bb7deb2c8d1b6b7973e81365a4930dfebe92fa1ce9a04fe
#     isv_prod_id: 1
#     isv_svn:     1
#     attr.flags:  0000000000000004
#     attr.xfrm:   0000000000000007
#     mask.flags:  ffffffffffffffff
#     mask.xfrm:   fffffffffff9ff1b
#     misc_select: 00000000
#     misc_mask:   ffffffff
#     modulus:     bb751af8545d1a7c25d29dbe0dc97604...
#     exponent:    3
#     signature:   2fa7f39cd5ceeaf9c483e8af4a583e43...
#     date:        2022-08-05
```

**Reference measurement:** `f9df5d816a5884b4ffe8eb3cf6d44ec7c2611c3916de3242ad0b5ce02fbf9456`


## Build (unguided)

Following the [build instructions](https://github.com/enclaive/enclaive-docker-mongodb-sgx/tree/464f22f619fd729cc2669315ec872aabc2042305#build-the-image):

```bash
docker build -t enclaive/mongodb-sgx:latest 'https://github.com/enclaive/enclaive-docker-mongodb-sgx.git#main'

docker run -it --device /dev/sgx_enclave --device /dev/sgx_provision enclaive/mongodb-sgx:latest
# Attributes:
#     mr_enclave:  9c1194c15572c2021e74b8749da8acd3d33dab4bb362252f3b13767edcdb499d
#     mr_signer:   fe2e0fd23973a6128bb7deb2c8d1b6b7973e81365a4930dfebe92fa1ce9a04fe
#     isv_prod_id: 1
#     isv_svn:     1
#     attr.flags:  0000000000000004
#     attr.xfrm:   0000000000000007
#     mask.flags:  ffffffffffffffff
#     mask.xfrm:   fffffffffff9ff1b
#     misc_select: 00000000
#     misc_mask:   ffffffff
#     modulus:     bb751af8545d1a7c25d29dbe0dc97604...
#     exponent:    3
#     signature:   e8840124ec69dc75109793ab97986c8c...
#     date:        2026-04-01
# Gramine is starting. Parsing TOML manifest file, this may take some time...
```

**Retrieved measurement:** `9c1194c15572c2021e74b8749da8acd3d33dab4bb362252f3b13767edcdb499d` &rarr; *mismatch*


## Build (guided)

*The developers informed us that the build is not reproducible due convenience shortcuts preventing reproducibility.*


### &rarr; NOT reproducible (guided scenario)
