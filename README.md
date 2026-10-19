# srlinux-protobufs

Protobuf (`.proto`) definitions for Nokia SR Linux's external gRPC interfaces that are not covered by existing open standards (e.g. OpenConfig gNMI, gNOI).

This repository currently contains:

- **YANG Modeled Actions (`YANGAction` service)** — a generic gRPC interface for executing YANG `action` statements (RFC 7950 §7.15) defined on SR Linux, whether Nokia-shipped or (in a future release) customer-defined via the NDK. See `yang_action/yang_action.proto` for the full definition.

As additional external proto interfaces are introduced, they will be added as sibling subdirectories within each release directory (see **Repository layout** below) rather than as separate repositories.

## Repository layout

```
latest_srlinux_<major>.<minor>/
  yang_action/
    yang_action.proto
```

Each `latest_srlinux_<major>.<minor>/` directory contains the proto definitions as shipped in that SR Linux release. Additional interfaces, when introduced, will appear as additional subdirectories alongside `yang_action/`.

## Branching and tagging

This repository follows the same convention used in [`srlinux-yang-models`](https://github.com/nokia-steward/srlinux-yang-models):

- **Branches:** `vXX.YY` (e.g. `v26.10`)
- **Tags:** `vXX.YY.Z` (e.g. `v26.10.1`) — note: no `R`
- **Directories:** `latest_srlinux_XX.YY` (e.g. `latest_srlinux_26.10`)

## Release retention

The `main` branch contains `latest_srlinux_XX.YY` directories for only the last four major releases. Older releases are not deleted from git history — they remain fully available by checking out the corresponding tag or branch — they are simply pruned from `main`'s working tree once superseded.

## Relationship to OpenConfig gNOI

The `YANGAction` service is a **Nokia-defined, proprietary interface**. It follows design conventions similar to OpenConfig's gNOI services (proto package `gnoi.yang_action.v1`, use of `types.Path`, JSON-IETF encoding) as a model-driven complement to them, but **it has not been submitted to, or accepted by, the OpenConfig project.** It is not an OpenConfig-endorsed or OpenConfig-governed service. The package name and overall naming are provisional and may change if and when a contribution to OpenConfig is pursued in the future; consumers should not assume naming stability on that front.

## External dependencies

`yang_action.proto` imports `gnoi/types/types.proto` from the OpenConfig [`gnoi`](https://github.com/openconfig/gnoi) project. This dependency is not vendored in this repository; compile against the upstream `openconfig/gnoi` source directly.

The `types.Path` message has been stable since its initial commit and is not expected to change. Validated against [`openconfig/gnoi` `v0.8.0`](https://github.com/openconfig/gnoi/releases/tag/v0.8.0); any current or future `gnoi` release should work equally well.

## Building

This proto does not set a `go_package` option. When generating Go bindings, supply the import path explicitly:

```
protoc --proto_path=. --proto_path=<path-to-openconfig-gnoi> \
  --go_out=. --go_opt=Myang_action/yang_action.proto=github.com/nokia-steward/srlinux-protobufs/yang_action \
  --go-grpc_out=. --go-grpc_opt=Myang_action/yang_action.proto=github.com/nokia-steward/srlinux-protobufs/yang_action \
  yang_action/yang_action.proto
```

## License

© 2026 Nokia. Licensed under the Apache License 2.0. See [LICENSE](./LICENSE).

SPDX-License-Identifier: Apache-2.0
