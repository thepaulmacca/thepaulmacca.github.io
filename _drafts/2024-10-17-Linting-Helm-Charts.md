---
title: Linting Helm Charts with Chart-Testing
date: 2024-10-17 08:00:00 +0000
categories: [Kubernetes, Helm]
tags: [kubernetes, helm, linting]
---

When developing charts, it can be easy to make a mistake with YAML indentation or miss something out. Linting them should be a part of your chart development and testing processes.

In this post, I'm going to talk about the Helm [chart-testing](https://github.com/helm/chart-testing) tool, but first lets look at the linter that's included with the Helm client.

## Helm Lint

The Helm client includes a linter. To use it, use the `lint` command on a chart as a directory or a packaged archive. In the below example, I have a chart called `demo` in a `charts` directory:

```yaml
➜ helm lint charts/demo
==> Linting charts/demo
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
```

No errors ✅

> You'll see the above info message when creating a default chart with `helm create`. The missing icon won't affect the operation of the chart, but it will affect the way it is displayed in UIs. You can use the `--quiet` flag to print only warnings and errors if you prefer.
{: .prompt-info }

### Feedback Levels

Helm provides 3 levels of actionable feedback about charts:

- `INFO`
  - Charts can be installed (exit code 0)
- `ERROR`
  - Linter encounters things that will cause the chart to fail installation(non-zero exit code)
- `WARNING`
  - Encounters issues that break with convention or recommendation (exit code 0 by default, but can be set to non-zero with the `--strict` flag)

### Kubernetes API Version Checks

Helm version `3.14.0` introduced a `--kube-version` flag for capabilities and deprecation checks. If you were using something like [pluto](https://github.com/FairwindsOps/pluto) for this previously, you can now replace it with this instead:

`helm lint charts/demo --kube-version 1.31`
