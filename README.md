# README

**PLEASE CONSIDER USING ALTERNATIVE WAYS OF CREATING YOUR KEPTN INTEGRATION**.

There are more ways to integrate with your service that don't require creating a service from this template, see https://keptn.sh/docs/0.17.x/integrations/how_integrate/ for more details.

Examples:

* Webhooks: https://keptn.sh/docs/0.17.x/integrations/webhooks/
* Job-Executor-Service: https://github.com/keptn-sandbox/job-executor-service

In addition, creating your own integration based on the service-template has been reduced to just using [Keptn's go-utils]( https://github.com/keptn/go-utils/)
and is based on [**the example provided in go-utils**](https://github.com/keptn/go-utils/tree/master/examples/go-sdk).

---

This is a Keptn Service Template written in GoLang. Follow the instructions below for writing your own Keptn integration.

Quick start:

1. In case you want to contribute your service to keptn-sandbox or keptn-contrib, make sure you have read and understood the [Contributing Guidelines](https://github.com/keptn-sandbox/contributing).
1. Click [Use this template](https://github.com/keptn-sandbox/keptn-service-template-go/generate) on top of the repository, or download the repo as a zip-file, extract it into a new folder named after the service you want to create (e.g., simple-service) 
1. **Required**: Run GitHub workflow `One-time repository initialization` to tailor deployment files and go modules to the new instance of the keptn service template. This will create a Pull Request containing the necessary changes, review it, adjust if necessary and merge it.
1. Figure out whether your Kubernetes Deployment requires [any RBAC rules or a different service-account](https://github.com/keptn-sandbox/contributing#rbac-guidelines), and adapt [chart/templates/serviceaccount.yaml](chart/templates/serviceaccount.yaml) accordingly for the roles.
1. Last but not least: Remove this intro within the README file and make sure the README file properly states what this repository is about

---

# keptn-service-template-go
![GitHub release (latest by date)](https://img.shields.io/github/v/release/keptn-sandbox/keptn-service-template-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/keptn-sandbox/keptn-service-template-go)](https://goreportcard.com/report/github.com/keptn-sandbox/keptn-service-template-go)

This implements a keptn-service-template-go for Keptn. If you want to learn more about Keptn visit us on [keptn.sh](https://keptn.sh)

## Compatibility Matrix

*Please fill in your versions accordingly*

| Keptn Version* | [Keptn-service-template-go version](https://github.com/keptn-sandbox/keptn-service-template-go/releases) |
|:--------------:|:--------------------------------------------------------------------------------------------------------:|
|     0.13.x     |                                                  0.13.0                                                  |
|     0.14.x     |                                                  0.14.0                                                  |
|     0.17.x     |                                                  0.17.0                                                  |


\* This is the Keptn version we aim to be compatible with. Other versions should work too, but there is no guarantee.

**Note**: Versions compatible with Keptn 0.14.x and newer are not backward compatible due to a change in NATS cluster name
(see https://github.com/keptn/keptn/releases/tag/0.14.1 for more info about the breaking change).

## Installation

The *keptn-service-template-go* can be installed as a part of [Keptn's uniform](https://keptn.sh).

### Deploy in your Kubernetes cluster

To deploy the current version of the *keptn-service-template-go* in your Keptn Kubernetes cluster use the [`helm chart`](chart/Chart.yaml) file,
for example:

```console
helm install -n keptn keptn-service-template-go chart/
```

This should install the `keptn-service-template-go` into the `keptn` namespace, which you can verify using

```console
kubectl -n keptn get deployment keptn-service-template-go -o wide
kubectl -n keptn get pods -l run=keptn-service-template-go
```

### Up- or Downgrading

Adapt and use the following command in case you want to up- or downgrade your installed version (specified by the `$VERSION` placeholder):

```console
helm upgrade -n keptn --set image.tag=$VERSION keptn-service-template-go chart/
```

### Uninstall

To delete a deployed *keptn-service-template-go*, use the file `deploy/*.yaml` files from this repository and delete the Kubernetes resources:

```console
helm uninstall -n keptn keptn-service-template-go
```

## Development

Development can be conducted using any GoLang compatible IDE/editor (e.g., Jetbrains GoLand, VSCode with Go plugins).

It is recommended to make use of branches as follows:

* `main`/`master` contains the latest potentially unstable version
* releases are handled via git tags (and GitHub releases)
* create a new branch for any changes that you are working on, e.g., `feature/my-cool-stuff` or `bug/overflow`
* once ready, create a pull request from your branch back to the `main`/`master` branch

When writing code, it is recommended to follow the coding style suggested by the [Golang community](https://github.com/golang/go/wiki/CodeReviewComments).

### Where to start

Please read the documentation provided by [Keptn/go-utils](https://github.com/keptn/go-utils/tree/master/examples/go-sdk), as it explains how to integrate with Keptn.

To better understand all variants of Keptn CloudEvents, please look at the [Keptn Spec](https://github.com/keptn/spec).
 
If you want to look at handler implementations, you can take a look at the [go-utils based example](https://github.com/keptn/go-utils/blob/master/examples/go-sdk/handler.go), 
as well as the concrete implementations within the [handler/](handler/) folder, e.g.:
* [action-triggered](handler/action_triggered_event_handler.go)
* [get-sli-triggered](handler/get_sli_triggered_event_handler.go)

### Common tasks

* Build the binary: `go build -ldflags '-linkmode=external' -v -o keptn-service-template-go`
* Run tests: `go test -race -v ./...`
* Build the docker image: `docker build . -t keptn-sandbox/keptn-service-template-go:dev` (Note: Ensure that you use the correct DockerHub account/organization)
* Run the docker image locally: `docker run --rm -it -p 8080:8080 keptn-sandbox/keptn-service-template-go:dev`
* Push the docker image to DockerHub: `docker push keptn-sandbox/keptn-service-template-go:dev` (Note: Ensure that you use the correct DockerHub account/organization)
* Get logs using `kubectl`: `kubectl -n keptn logs deployment/keptn-service-template-go -f`
* Watch the deployed pods using `kubectl`: `kubectl -n keptn get pods -l run=keptn-service-template-go`
* Deploy the service using [Skaffold](https://skaffold.dev/): `skaffold run --default-repo=your-docker-registry --tail` (Note: Replace `your-docker-registry` with your container image registry (defaults to ghcr.io/keptn-sandbox/keptn-service-template-go); also make sure to adapt the image name in [skaffold.yaml](skaffold.yaml))


### Testing Cloud Events

We have dummy cloud-events in the form of [RFC 2616](https://ietf.org/rfc/rfc2616.txt) requests in the [test-events/](test-events/) directory. These can be easily executed using third party plugins such as the [Huachao Mao REST Client in VS Code](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

## Automation

### GitHub Actions: Automated Pull Request Review

This repo uses [reviewdog](https://github.com/reviewdog/reviewdog) for automated reviews of Pull Requests. 

You can find the details in [.github/workflows/reviewdog.yml](.github/workflows/reviewdog.yml).

### GitHub Actions: Unit Tests

This repo has automated unit tests for pull requests. 

You can find the details in [.github/workflows/CI.yml](.github/workflows/CI.yml).

### GH Actions/Workflow: Build Docker Images

This repo uses GH Actions and Workflows to test the code and automatically build containers and upload it to `ghcr.io`.

## How to release a new version of this service

It is assumed that the current development takes place in the `main`/`master` branch (either via Pull Requests or directly).

Once you're ready, go to the Actions tab on GitHub, select Pre-Release or Release, and run the action.


## License

Please find more information in the [LICENSE](LICENSE) file.
