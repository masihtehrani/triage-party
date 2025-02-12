# Triage Party 🎉

`NOTE: This is not an officially supported Google product`

Triage Party is a tool for triaging incoming GitHub issues for large open-source projects, built with the GitHub API.

![screenshot](screenshot.png)

Triage Party focuses on reducing response latency for incoming GitHub issues and PR's, and ensure that conversations are not lost in the ether. It was built from the [Google Container DevEx team](https://github.com/GoogleContainerTools)'s experience contributing to popular open-source projects, such as [minikube](https://github.com/kubernetes/minikube), [Skaffold](https://github.com/GoogleContainerTools/skaffold/), and [Kaniko](https://github.com/GoogleContainerTools/kaniko/).

Triage Party is a stateless Go web application, configured via YAML. While it has been optimized for Google Cloud Run deployments, it's deployable anywhere due to its low memory footprint: even on a Raspberry Pi.

## Features

* Queries that are not possible on GitHub:
  * conversation state (`tag: recv`, `tag: send`)
  * how long since a project member responded (`responded: +15d`)
  * duration (`updated: +30d`)
  * regexp (`label: priority/.*`)
  * reactions (`reactions: >=5`)
  * comment popularity (`comments-per-month: >0.9`)
  * duplicate detection
  * ... and more!
* Multi-player mode: Supports up to 20 simultaneous players in group triage
* Easily open an entire group of issues into browser tabs (must accept pop-up dialog)
* Queries across multiple repositories
* "Shift-Reload" for live data pull

## Triage Party in production

See these fine examples in the wild:

* [kubernetes/minikube](http://tinyurl.com/mk-tparty)
* [GoogleContainerTools/skaffold](http://tinyurl.com/skaffold-tparty)
* [jetstack/cert-manager](https://triage.build-infra.jetstack.net/)

## Requirements

* [GitHub API token with read access](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)
* Go v1.14 or higher

## Try it!

Store a GitHub token some place on disk:

`echo YOUR_GENERATED_TOKEN > $HOME/.github-token`

Run:

```shell
go run cmd/server/main.go \
  --github-token-file=$HOME/.github-token \
  --config examples/generic-kubernetes.yaml \
  --repos kubernetes/sig-release
```

You will now see a constant stream of output as Triage Party is pulling content from GitHub. The first time a new repository is used, it will require some time (~45s in this case) to download the necessary data before minikube will render pages.

The site is now available at [http://localhost:8080/](http://localhost:8080/), but will initially block page loads until content has been locally cached. After the first run, pages are rendered from memory within ~5ms.

## Usage Tips

Triage Party can be configured to accept any triage workflow you can imagine. Here are some tips:

* Use the drop-down labelled `Solo` on the top-right to enable multi-player mode
* Use the blue `box with arrow` icon to open issues/pull requests into a new tab
  * The first time you click this, your browser will block pop-ups!
  * The notification to allow-popups may be hidden in the URL bar.
* Rules work best when there is a documented resolution to remove it from the list
* Pages work best if the process is defined so that the page is empty when triage is complete
* If an non-actionable issue is shown as part of a daily or weekly triage, step back to tune your rules and/or define an appropriate resolution.

## Configuration

YAML-based. See the [configuration guide](docs/config.md).

## Deployments

Triage Party can be deployed on anything from [Google Cloud Run](https://cloud.google.com/run) to [https://kubernetes.io/](Kubernetes), or even a [Raspberry Pi](https://www.raspberrypi.org/) running [https://www.netbsd.org/](NetBSD).

See the [deployment guide](docs/deploy.md) for more information.
