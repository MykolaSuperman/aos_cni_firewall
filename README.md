# AosCore CNI Firewall

A CNI (Container Network Interface) plugin for [AosCore](https://github.com/aosedge) that provides firewall capabilities for container network traffic using iptables.

## Description

The `aos-firewall` plugin is a meta CNI plugin that implements network firewall rules for containers. It allows fine-grained control over inbound and outbound network connections by:

- Restricting inbound connections based on ports and protocols (input access rules)
- Controlling outbound connections to specific destinations (output access rules)
- Managing iptables chains for each container

This plugin is designed to work as a chained CNI plugin and must be used in conjunction with other CNI plugins that configure the container's network interface.

## Features

- **Input Access Control**: Define allowed inbound connections by specifying ports and protocols
- **Output Access Control**: Restrict outbound connections to specific destination IPs and ports
- **Unique Container Isolation**: Each container gets its own iptables chain identified by UUID
- **Public Connection Control**: Configurable option to allow or deny general internet access
- **Persistent State Management**: Maintains plugin state across container lifecycle
- **CNI Specification Compliant**: Implements ADD, DEL, and CHECK commands

## Configuration Example

```json
{
  "cniVersion": "1.0.0",
  "name": "mynet",
  "type": "aos-firewall",
  "uuid": "container-unique-id",
  "iptablesAdminChainName": "AOS_CHAIN_NAME",
  "allowPublicConnections": false,
  "runtimeStatePath": "/run/containers/cni/aos-firewall/aos_chains.conf",
  "inputAccess": [
    {
      "port": "8080",
      "protocol": "tcp"
    }
  ],
  "outputAccess": [
    {
      "dstIp": "10.0.0.1",
      "dstPort": "443",
      "proto": "tcp",
      "srcIp": ""
    }
  ],
  "prevResult": { }
}
```

## Building

To build the plugin:

```bash
go build -o bin/aos-firewall ./plugins/meta/aos-firewall
```

## Running Tests

The project uses [Ginkgo](https://github.com/onsi/ginkgo) and [Gomega](https://github.com/onsi/gomega) for testing.

### Run all tests

```bash
go test ./...
```

### Run tests with verbose output

```bash
go test -v ./...
```

### Run tests using Ginkgo CLI

If you have Ginkgo installed:

```bash
ginkgo -r
```

### Run specific test suite

```bash
go test ./plugins/meta/aos-firewall
go test ./plugins/meta/aos-firewall/firewall
```

## Requirements

- Go 1.22.0 or higher
- Linux with iptables support
- Root privileges for iptables manipulation
