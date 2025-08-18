# Protocol Buffers & gRPC Definitions

[![Go Version](https://img.shields.io/badge/Go-1.22%2B-blue.svg)](https://golang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository serves as the **Single Source of Truth (SSoT)** for all Protocol Buffer (`.proto`) definitions and generated gRPC code for the E-Commerce Microservice Platform.

## 📖 Overview

In a distributed microservice architecture, ensuring that services communicate seamlessly and without type mismatches is critical. This repository solves that problem by providing a central, version-controlled location for all API contracts.

All microservices in the platform consume this repository as a Go module to get the necessary stubs and interfaces for gRPC communication. This guarantees that a `service-order` and a `service-product` are always referring to the exact same version of an API definition.

## 🛠️ Tech Stack

| Category | Technology |
| :--- | :--- |
| **Interface Definition Language** | Protocol Buffers (proto3) |
| **RPC Framework** | gRPC |

## 📁 Repository Structure

The repository is organized by service domain, with all generated Go code located in a central `gen` directory at the root.

```
/
|-- /cart
|   |-- cart.proto
|-- /order
|   |-- order.proto
|-- /product
|   |-- product.proto
|-- /user
|   |-- user.proto
|-- /gen
|   |-- /go
|       |-- /cart
|           |-- cart.pb.go
|           |-- cart_grpc.pb.go
|       |-- /order
|           |-- ...
|       |-- /product
|           |-- ...
|       |-- /user
|           |-- ...
|-- go.mod
|-- go.sum
```

## developer-guide How to Update Definitions

When you need to add or modify an RPC method or a message, follow these steps carefully:

### Prerequisites

You must have the `protoc` compiler and the Go gRPC plugins installed.

1.  **Install `protoc`:** Follow the instructions at [Protocol Buffer Compiler Installation](https://grpc.io/docs/protoc-installation/).
2.  **Install Go Plugins:**
    ```bash
    go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
    go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
    ```

### Steps to Regenerate Code

1.  **Modify a `.proto` file:** Make your desired changes to the relevant `.proto` file (e.g., add a new RPC to `product/product.proto`).

2.  **Run the `protoc` command:** From the root directory of this repository, run the following command to regenerate the Go code for all `.proto` files. This command will place the generated files under the `gen/go` directory, matching the `go_package` option in your `.proto` files.
    ```bash
    protoc --proto_path=. \
      --go_out=./gen/go --go_opt=paths=source_relative \
      --go-grpc_out=./gen/go --go-grpc_opt=paths=source_relative \
      $(find . -name '*.proto')
    ```
    *(Note: The exact command may vary slightly based on your `.proto` files' `go_package` options. The key is that output directories are set to `./gen/go`.)*

3.  **Tidy Go Modules:** Clean up the dependencies.
    ```bash
    go mod tidy
    ```

4.  **Commit and Push:** Commit all changes (`.proto`, `go.mod`, `go.sum`, and the generated `*.go` files) and push them to the `main` branch.
    ```bash
    git add .
    git commit -m "feat(product): Add new GetProductList RPC"
    git push
    ```

5.  **Update Downstream Services:** In each microservice that needs to use the new definitions, update the dependency to pull the latest version:
    ```bash
    go get -u github.com/ogozo/proto-definitions@main
    ```

---
For more information about the overall project architecture, see the [main project README](https://github.com/ogozo/docker-compose-environment/blob/main/README.md).
