# SGX Arweave v0.0.1

The following instructions were executed on Ubuntu 20.04 LTS with Docker installed.


## Reference Measurement

Available online [here](https://github.com/Safeheron/sgx-arweave-cpp/blob/e19fddfe8a166bd824c01cb8856f2a93a3a6d9c9/doc/MRENCLAVE_TABLE.md):

<body><table>

<tr>
<td>Operating System</td>
<td colspan="2">Ubuntu 18.04 LTS</td>
<td colspan="2">Ubuntu 20.04 LTS</td>
</tr>

<tr>
<td>gcc/g++</td>
<td colspan="2">Ubuntu 9.4.0-1ubuntu1~18.04</td>
<td colspan="2">Ubuntu 9.4.0-1ubuntu1~20.04.1</td>
</tr>

<tr>
<td>SGX SDK</td>
<td colspan="2">sgx_linux_x64_sdk_2.17.101.1.bin</td>
<td colspan="2">sgx_linux_x64_sdk_2.17.101.1.bin</td>
</tr>

<tr>
<td>SGX Driver</td>
<td colspan="2">sgx_linux_x64_driver_1.41.bin</td>
<td colspan="2">sgx_linux_x64_driver_1.41.bin</td>
</tr>

<tr>
<td>OpenSSL(to compile sgxssl)</td>
<td colspan="2">openssl-1.1.1q.tar.gz</td>
<td colspan="2">openssl-1.1.1q.tar.gz</td>
</tr>

<tr>
<td>Linker ld</td>
<td>intel linker ld</td>
<td>system linker ld</td>
<td>intel linker ld</td>
<td>system linker ld</td>
</tr>

<tr>
<td>MRENCLAVE</td>
<td>a757d204629982442b0701be19dee11e
1443c1abf9d8d28008304b002eea235e</td>

<td>3a34e115f8f0cfa1b9bea466b86b9769
72d024176b5be3888223825160cab36b</td>

<td>bc88619ffd1acdcbca9ecc9e9c397450
d3b41535a86eea5dc84d82f8a525fb1d</td>

<td>a13b0c8829d6c1b2071dda8d7d446658
cc84acf9930ac13c51190891debb85d3</td>
</tr>

</table></body>


## Build (unguided)

We followed the official [build documentation](https://github.com/Safeheron/sgx-arweave-cpp/blob/e19fddfe8a166bd824c01cb8856f2a93a3a6d9c9/doc/MRENCLAVE_TABLE.md) on a machine with Ubuntu 20.04.

**Prerequisites:**

```bash
sudo apt update && sudo apt upgrade
sudo apt install -y wget build-essential

# SGX driver
sudo apt install -y dkms
wget https://download.01.org/intel-sgx/sgx-linux/2.17/distro/ubuntu20.04-server/sgx_linux_x64_driver_1.41.bin
chmod +x sgx_linux_x64_driver_1.41.bin
./sgx_linux_x64_driver_1.41.bin

# SGX SDK
wget https://download.01.org/intel-sgx/sgx-linux/2.17.1/distro/ubuntu20.04-server/sgx_linux_x64_sdk_2.17.101.1.bin
chmod +x sgx_linux_x64_sdk_2.17.101.1.bin
./sgx_linux_x64_sdk_2.17.101.1.bin   //Two questions will be asked, answer as follows
source /opt/intel/sgxsdk/environment

wget https://github.com/protocolbuffers/protobuf/releases/download/v3.14.0/protobuf-cpp-3.14.0.tar.gz
tar -xvf protobuf-cpp-3.14.0.tar.gz
cd protobuf-3.14.0
./configure
make -j$(nproc)
sudo make install
sudo ldconfig
sudo cp -r src/google/protobuf/util/* /opt/intel/sgxsdk/include/tprotobuf/google/protobuf/
sudo cp -r src/google/protobuf/util/* /opt/intel/sgxsdk/include/tprotobuf/google/protobuf/util/
cd ..

# CPPRESTSDK
sudo apt-get install -y libcpprest-dev

# glog
sudo apt-get install libgflags-dev
sudo apt install -y git-all

wget https://github.com/Kitware/CMake/releases/download/v3.29.0/cmake-3.29.0-linux-x86_64.sh
mkdir /opt/cmake
sh cmake-3.29.0-linux-x86_64.sh --prefix=/opt/cmake --skip-license
echo 'export PATH=/opt/cmake/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

git clone https://github.com/google/glog.git
cd glog
mkdir build && cd build
cmake .. 
make
make install

# SGX SSL
git clone https://github.com/intel/intel-sgx-ssl.git
cd intel-sgx-ssl/
git checkout 4b4295673f3c2d2f6b13b0c56b193618fbac7f9d
cd openssl_source/
wget https://github.com/openssl/openssl/releases/download/OpenSSL_1_1_1o/openssl-1.1.1o.tar.gz --no-check-certificate
cd ../Linux
make all test
sudo make install

# Boost
sudo apt-get install libboost-system-dev

sudo sudo apt update
sudo sudo apt install -y libgtest-dev

# Safeheron Cryptosuites for SGX
sudo apt-get install autoconf automake libtool curl make g++ unzip
git clone --recurse-submodules https://github.com/safeheron/safeheron-crypto-suites-cpp.git
cd safeheron-crypto-suites-cpp
mkdir build && cd build
cmake .. -DENABLE_TESTS=ON
make -j
sudo make install
cd ..
mkdir build-sgx && cd build-sgx
cmake .. -DPLATFORM=SGX
make -j
sudo make install

# Safeheron TSS
git clone --recurse-submodules https://github.com/Safeheron/tss-rsa-cpp-sgx.git
cd tss-rsa-cpp-sgx 
mkdir build && cd build
cmake ..
make
```

&rarr; *build fails*

*Note: The TSS build fails because there are some clashes with the include directives of TSS and the cryptosuites. Tried to build v1.0.0 instead which is the one from the time of the latest commit in TSS repo. However, this fails when trying to download some of the submodules which are not available.*


## Build (guided)

*The developers updated the repository and build instructions after acceptance. However, we could not verify reproducibility for the updated repository, as the file with the reference hashes was removed as part of the update, so it no longer meets our criteria for the evaluation. Either way, the project falls into the "not reproducible" category.*


### &rarr; NOT reproducible (guided scenario)
