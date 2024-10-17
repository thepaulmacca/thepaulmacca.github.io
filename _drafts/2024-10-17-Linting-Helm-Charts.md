---
title: Linting Helm Charts with Chart-Testing
date: 2024-10-17 08:00:00 +0000
categories: [Kubernetes, Helm]
tags: [kubernetes, helm, linting]
---

When developing charts, it can be easy to make a mistake with YAML indentation or miss something out. Linting them should be a part of your chart development and testing processes.

In this post, I'm going to talk about the Helm [chart-testing](https://github.com/helm/chart-testing) tool, but first lets look at the linter that's included with the Helm client.

## Helm Lint

The Helm client includes a `helm lint` command.
