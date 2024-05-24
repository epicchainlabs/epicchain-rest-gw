
<p align="center">
  REST server to interact with <a href="https://fs.epicchain.org">EpicChain</a>.
</p>

---

[![Report](https://goreportcard.com/badge/github.com/nspcc-dev/epicchain-rest-gw)](https://goreportcard.com/report/github.com/nspcc-dev/epicchain-rest-gw)
![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/nspcc-dev/epicchain-rest-gw?sort=semver)
![License](https://img.shields.io/github/license/nspcc-dev/epicchain-rest-gw.svg?style=popout)

# epicchain-rest-gw

EpicChain REST Gateway bridges the EpicChain internal protocol and the REST API server.

## Installation

### Building

Before building, ensure you have the following tools:

- go
- make
- git
- curl
- docker

First, clone this repository:

```shell
$ git clone https://github.com/nspcc-dev/epicchain-rest-gw
```

Then run make to build the `bin/epicchain-rest-gw` binary:

```shell
$ make
```

Or build it using docker:

```shell
$ make docker/all
```

#### Generate OpenAPI Boilerplate Code

If you change the [spec file](./spec/rest.yaml), regenerate the server code.

You have several approaches:

1. Run make. It automatically downloads `oapi-codegen` and generates the boilerplate.

```shell
$ make
```

2. Generate code separately:

```shell
$ make generate-server
```

#### Other Targets

Notable make targets:

```
dep             Check and ensure dependencies
image           Build a clean docker image
image-dirty     Build a dirty docker image with host-built binaries
formats         Run all code formatters
lint            Run linters
version         Show current version
generate-server Generate boilerplate by spec
```

### Docker

Alternatively, use a [Docker image](https://hub.docker.com/r/nspccdev/epicchain-rest-gw) provided for released
(and occasionally unreleased) versions of the gateway (`:latest` points to the latest stable release).

## Execution

The REST gateway is not an EpicChain node. To access EpicChain, it uses the node's gRPC interface, and you need to provide a node for it to connect to. This can be done via the `-p` parameter or the `REST_GW_POOL_PEERS_<N>_ADDRESS` and
`REST_GW_POOL_PEERS_<N>_WEIGHT` environment variables (the gateway supports multiple EpicChain nodes with weighted load balancing).

If launching the REST gateway in a bundle with [epicchain-dev-env](https://github.com/nspcc-dev/epicchain-dev-env), get
an IP address of the node in the output of the `make hosts` command
(with `s0*.epicchain.devenv` name).

These two commands are functionally equivalent, running the gateway with one backend node (and otherwise default settings):

```shell
$ epicchain-rest-gw -p 192.168.130.72:8080
$ REST_GW_POOL_PEERS_0_ADDRESS=192.168.130.72:8080 epicchain-rest-gw
```

It's also possible to specify the URI scheme (grpc or grpcs) when using `-p`:

```shell
$ epicchain-rest-gw -p grpc://192.168.130.72:8080
$ REST_GW_POOL_PEERS_0_ADDRESS=grpcs://192.168.130.72:8080 epicchain-rest-gw
```

## Configuration

Everything available as a CLI parameter can also be specified via environment variables. For specifics, refer to `--help`. If you prefer a config file, use it in YAML format. See [config](./config/config.yaml) and [defaults](./docs/gate-configuration.md) for examples.

```shell
$ epicchain-rest-gw --config config.yaml
```

## Docs

View the Swagger specification using the following URL
(assuming you ran rest-gw on `localhost:8090`):

- http://localhost:8090/v1/docs

## Known Issues

Using `%` in path parameters [leads to an error](https://github.com/nspcc-dev/epicchain-rest-gw/issues/195). For example, getting an object by attribute:
- `/v1/get_by_attribute/A9pnctophx5PFt4eUd8d2aa6otjgnQZNzyvt3HCeygFD/cat%25jpeg/cat%25jpeg`

  > `cat%25jpeg` is already a URL-escaped string, original value is `cat%jpeg`.

Also, when you run rest-gw, you will see a line similar to:
```shell
...
2024/02/12 13:21:31 Serving epicchain rest gw at http://192.168.130.72:8080
```
You will find an auto-redirect to the specification page via this link.

### Migrating from HTTP Gateway

See [docs/migration-from-http.md](./docs/migration-from-http.md).

## Contributing

Feel free to contribute to this project after reading the [contributing guidelines](CONTRIBUTING.md).

Before starting to work on a topic, create a new issue first, describing
the feature/topic you plan to implement.

## License

This project is licensed under the GNU GPL v3 License -
see the [LICENSE](LICENSE) file for details.