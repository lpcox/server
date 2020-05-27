# Contributing Guidelines

Welcome to CovidShield. Thank you for considering contributing to COVID Shield!

We’d love to get your issues (if you find any bugs) and PRs (if you have any fixes)!

- [Code of Conduct](#code-of-conduct)
- [Reporting Security Issues](#reporting-security-issues)
- [Contributing](#contributing)
  - [Contributing Documentation](#contributing-documentation)
  - [Contributing Code](#contributing-code)
- [Running CovidShield](#running-covidshield)
  - [Using Docker-Compose](#using-docker-compose)
  - [Using Dev](#using-dev)
  - [From Scratch](#from-scratch)
- [Testing](#testing)
- [Metrics and Tracing](#metrics-and-tracing)

## Code of Conduct

First, please review this document and the [Code of Conduct](CODE_OF_CONDUCT.md).

## Reporting Security Issues

COVID Shield takes security very seriously. In the interest of coordinated disclosure,
we request that any potential vulnerabilities be reported privately in accordance with
our [security policy](SECURITY.md).

## Contributing

### Contributing Documentation

If you'd like to contribute a documentation or static file change, please
feel free to fork the project in GitHub and open a PR from that fork against
this repository.

### Contributing Code

If you'd like to contribute code changes, the following steps will help you
setup a local development environment. If you're a Shopify employee, `dev up`
will install the above dependencies and `dev {build,test,run,etc.}` will work
as you'd expect.

Once you're happy with your changes, please fork the repository and push your
code to your fork, then open a PR against this repository.

## Running CovidShield

### Using Docker-Compose

1. Fork https://github.com/CovidShield/server to your account
1. Clone your fork of CovidShield/server repo locally by running `git clone https://github.com/<username>/server.git`
1. Enter the repo directory `cd server`
1. Run `docker-compose up`

Note: It is normal to see a few errors from the retrieval service exiting initially while the MySQL database is instantiated

### Using Dev

If you're at Shopify, running `dev up` will configure the server and database for you. 
You can run `make && dev server` to start the server.

### From Scratch

#### Prerequisites

* Go (tested with 1.14)
* Ruby (tested with 2.6.5)
* Bundler
* [protobuf](https://developers.google.com/protocol-buffers/) (tested with libprotoc 3.11.4)
* [protoc-gen-go](https://github.com/golang/protobuf) (may only be needed to change `proto/*`)
* libsodium
* docker-compose
* MySQL

#### Building

Run `make` or `make release` to build a release version of the servers.

#### Running

```bash
# example...
export DATABASE_URL="root@tcp(localhost)/covidshield"
export KEY_CLAIM_TOKEN=thisisatoken=302

./key-retrieval migrate-db

PORT=8000 ./key-submission
PORT=8001 ./key-retrieval
```

Note that 302 is a [MCC](https://www.mcc-mnc.com/): 302 represents Canada.

## Testing

If you're at Shopify, `dev up` will configure the database for you. If not
you will need to point to your database server using the environment variables
(note that the database will be clobbered so ensure that you don't point to a
production database):

```shell
$ export DB_USER=<username>
$ export DB_PASS=<password>
$ export DB_HOST=<hostname>
$ export DB_NAME=<test database name>
```

Then, ensure the appropriate requirements are installed:

```shell
$ bundle install
```

Finally, run:
```shell
$ make test
```

## Metrics and Tracing

CovidShield uses [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-go) to configure the metrics and tracing for the server, both the key retrieval and key submission.

### Metrics

Currently, the following options are supported for enabling Metrics:
* standard output
* prometheus

Metrics can be enabled by setting the `METRIC_PROVIDER` variable to either `stdout`, or `prometheus`.

If you want to use Prometheus, please see the additional configuration requirements below.

#### Prometheus 

In order to use Prometheus as a metrics solution, you'll need to be running it in your environment. 

You can follow the instructions [here](https://prometheus.io/docs/prometheus/latest/installation/) for running Prometheus. 

You will need to edit the configuration file, `prometheus.yml` to add an additional target so it actually polls the metrics coming from the CovidShield server:

```
...
    static_configs:
    - targets: ['localhost:9090', 'localhost:2222']
```

### Tracing 

Currently, the following options are supported for enabling Tracing:
* standard output
* prometheus

Tracing can be enabled by setting the `TRACER_PROVIDER` variable to either `stdout`, or `prometheus`.






