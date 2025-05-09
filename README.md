# cpp_setup

# **Complete Guide: Installing `protoc` and `grpc_cpp_plugin` on Linux**

This document provides step-by-step instructions for installing the **Protocol Buffers Compiler (`protoc`)** and the **gRPC C++ Plugin (`grpc_cpp_plugin`)** on a Linux system, including **offline installation** methods.

---

## **1. Install `protoc` (Protocol Buffers Compiler)**
### **Method 1: Install from Package Manager (Online)**
```bash
sudo apt update
sudo apt install -y protobuf-compiler libprotobuf-dev libprotoc-dev
```
**Verify Installation:**
```bash
protoc --version
# Should output: `libprotoc x.y.z`
```

### **Method 2: Install Pre-Built Binary (Offline)**
1. Download the latest `protoc` binary from:  
   [https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases)  
   (Choose `protoc-<version>-linux-x86_64.zip`)

2. Extract and install:
   ```bash
   unzip protoc-*-linux-x86_64.zip -d protoc
   sudo cp protoc/bin/protoc /usr/local/bin/
   sudo cp -r protoc/include/* /usr/local/include/
   sudo chmod +x /usr/local/bin/protoc
   ```
3. Verify:
   ```bash
   protoc --version
   ```

### **Method 3: Compile from Source (Offline)**
1. Download source code:
   ```bash
   wget https://github.com/protocolbuffers/protobuf/releases/download/v21.12/protobuf-all-21.12.tar.gz
   tar -xzf protobuf-all-21.12.tar.gz
   cd protobuf-21.12
   ```
2. Compile and install:
   ```bash
   ./configure --prefix=/usr/local
   make -j$(nproc)
   sudo make install
   sudo ldconfig
   ```
3. Verify:
   ```bash
   protoc --version
   ```

---

## **2. Install `grpc_cpp_plugin` (gRPC C++ Plugin)**
### **Method 1: Install `grpc_cpp_plugin` via `grpc-cli` (Online)**
```bash
sudo apt install -y grpc-cli
```
**Verify:**
```bash
which grpc_cpp_plugin
# Should output: `/usr/bin/grpc_cpp_plugin`
```

### **Method 2: Build from Source (Offline)**
1. Clone and build gRPC:
   ```bash
   git clone --recurse-submodules -b v1.46.3 https://github.com/grpc/grpc
   cd grpc
   mkdir -p cmake/build
   cd cmake/build
   cmake ../.. -DgRPC_INSTALL=ON -DgRPC_BUILD_TESTS=OFF
   make -j$(nproc)
   sudo make install
   ```
2. Locate `grpc_cpp_plugin`:
   ```bash
   find /usr/local -name "grpc_cpp_plugin"
   # Typically: `/usr/local/bin/grpc_cpp_plugin`
   ```

### **Method 3: Manually Copy `grpc_cpp_plugin` (Offline)**
If you already have `grpc_cpp_plugin` on another machine:
```bash
scp /usr/local/bin/grpc_cpp_plugin user@remote-server:/usr/local/bin/
sudo chmod +x /usr/local/bin/grpc_cpp_plugin
```

---

## **3. Verify Full Setup**
### **Test Code Generation**
1. Create a test `.proto` file (`test.proto`):
   ```proto
   syntax = "proto3";
   service TestService {
     rpc TestMethod (TestRequest) returns (TestResponse);
   }
   message TestRequest { string input = 1; }
   message TestResponse { string output = 1; }
   ```
2. Generate C++ gRPC code:
   ```bash
   protoc --grpc_out=. --cpp_out=. --plugin=protoc-gen-grpc=`which grpc_cpp_plugin` test.proto
   ```
3. Check generated files:
   ```bash
   ls test.pb.cc test.pb.h test.grpc.pb.cc test.grpc.pb.h
   ```

---

## **Troubleshooting**
| Issue | Solution |
|-------|----------|
| `protoc: command not found` | Ensure `/usr/local/bin` is in `$PATH` |
| `grpc_cpp_plugin not found` | Manually specify path: `--plugin=protoc-gen-grpc=/path/to/grpc_cpp_plugin` |
| Missing dependencies (`libprotobuf.so`) | Install `libprotobuf-dev` or compile from source |

---

## **Summary**
| Component | Online Install | Offline Install |
|-----------|---------------|----------------|
| `protoc` | `sudo apt install protobuf-compiler` | [Download binary](https://github.com/protocolbuffers/protobuf/releases) |
| `grpc_cpp_plugin` | `sudo apt install grpc-cli` | Build from source or copy manually |

This guide covers **all installation methods**, including **air-gapped setups**. Let me know if you need further assistance! 🚀
