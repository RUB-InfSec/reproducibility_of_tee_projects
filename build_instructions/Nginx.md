# Nginx-SGX

The following instructions were executed on Ubuntu 24.04 LTS with Docker installed.


## Reference Measurment

```bash
docker pull enclaive/nginx-sgx
docker create --name tmp enclaive/nginx-sgx:latest
docker cp tmp:/ ref
# references in ref/manifest/nginx.manifest.sgx 

# [[sgx.trusted_files]]
# uri = "file:/entrypoint/sbin/nginx"
# sha256 = "f11221c10ded4831f8d79483968a00cbbbea23a6640af3ef505bae00465a9dc8"
```

**Reference measurement:** `f11221c10ded4831f8d79483968a00cbbbea23a6640af3ef505bae00465a9dc8`


## Build (unguided)

Following the [build instructions](https://github.com/enclaive/enclaive-docker-nginx-sgx/tree/1372c154096f9b03a40918dd75f5b8e89a690484#build-the-image):

```bash
docker build -t enclaive/nginx-sgx:latest 'https://github.com/enclaive/enclaive-docker-nginx-sgx.git#main'
docker create --name tmp enclaive/nginx-sgx:latest
docker cp tmp:/ build

# [[sgx.trusted_files]]
# uri = "file:/entrypoint/sbin/nginx"
# sha256 = "f609daa882588bec974b67938ff1142d07b3dbbf422f6ec887a4c9588d8ca7ea"
```

**Retrieved measurement:** `f609daa882588bec974b67938ff1142d07b3dbbf422f6ec887a4c9588d8ca7ea` &rarr; *mismatch*


## Build (guided)

*The developers informed us that the build is not reproducible due to self-signed certificates generated at build time and convenience shortcuts.*


### &rarr; NOT reproducible (guided scenario)