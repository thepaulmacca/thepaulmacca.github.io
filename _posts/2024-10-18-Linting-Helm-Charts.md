---
title: Linting Helm Charts with Chart-Testing
date: 2024-10-18 14:30:00 +0000
categories: [Kubernetes, Helm]
tags: [kubernetes, helm, linting, testing]
---

When developing charts, it can be easy to make a mistake with YAML indentation or miss something out. Linting them should be considered a key part of your chart development and testing processes.

In this post, I'm going to talk about the Helm [chart-testing](https://github.com/helm/chart-testing) tool, but first lets look at the linter that's included with the Helm client.

## Helm Lint

The Helm client includes a linter. To use it, use the `lint` command on a chart as a directory or a packaged archive. In the below example, I have a chart called `demo` in a `charts` directory:

```bash
➜ helm lint charts/demo --strict
==> Linting charts/demo
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
```

> It's considered a good practice to use the `--strict` flag to catch warnings during development and testing.
{: .prompt-tip }

You'll see the above `INFO` message when creating a default chart with `helm create`. The missing icon won't affect the operation of the chart, but it will affect the way it is displayed in UIs. You can use the `--quiet` flag to print only warnings and errors if you prefer.

Let's introduce an error so you can see the output. I've intentionally added an invalid key/value pair to the `values.yaml` file:

```bash
➜ helm lint charts/demo --quiet --strict
==> Linting charts/demo
[ERROR] values.yaml: unable to parse YAML: error converting YAML to JSON: yaml: line 48: did not find expected key
[ERROR] templates/: cannot load values.yaml: error converting YAML to JSON: yaml: line 48: did not find expected key
[ERROR] : unable to load chart
        cannot load values.yaml: error converting YAML to JSON: yaml: line 48: did not find expected key

Error: 1 chart(s) linted, 1 chart(s) failed
```

### Feedback Levels

Helm provides 3 levels of actionable feedback about charts. These are useful to know in case you come across them:

- `INFO` - Charts can be installed (exit code 0)
- `ERROR` - Linter encounters things that will cause the chart to fail installation (non-zero exit code)
- `WARNING` - Encounters issues that break with convention or recommendation (exit code 0 by default, but can be set to non-zero with the `--strict` flag)

### Kubernetes API Version Checks

Helm version [v3.14.0](https://github.com/helm/helm/releases/tag/v3.14.0) introduced a `--kube-version` flag for capabilities and deprecation checks. This already exists in the `helm template` command. If you're using a tool like [pluto](https://github.com/FairwindsOps/pluto), you can now replace it with this instead:

```bash
helm lint charts/demo --strict --kube-version 1.31
```

> Please be aware of the [supported version skew policy](https://helm.sh/docs/topics/version_skew/#supported-version-skew) between Helm and Kubernetes versions if you're wondering why it's not working as intended.
{: .prompt-warning }

That's about as far as you can go with the `helm lint` command. Now let's turn our attention to the chart-testing tool, `ct`.

## Chart-Testing

The Helm [chart-testing](https://github.com/helm/chart-testing) tool (`ct`) provides more advanced testing capabilities, and is meant to be used for linting and testing pull requests. It automatically detects charts changed against the target branch (`main` in this example).

### Lint Command

If you look at the [ct lint](https://github.com/helm/chart-testing/blob/main/doc/ct_lint.md) command, you'll see a lot of options to use. The defaults are sufficient to get started.

#### Check Version Increment

I really like the `--check-version-increment` option. This activates a check for chart version increments, and is useful if you version your chart separate from your application.

Here's an example of what you'll see if you make a change to a chart without bumping the version:

```yaml
Run ct lint --target-branch main
Linting charts...

------------------------------------------------------------------------------------------------------------------------
 Charts to be processed:
------------------------------------------------------------------------------------------------------------------------
 demo => (version: "0.1.0", path: "charts/demo")
------------------------------------------------------------------------------------------------------------------------

Linting chart "demo => (version: \"0.1.0\", path: \"charts/demo\")"
Checking chart "demo => (version: \"0.1.0\", path: \"charts/demo\")" for a version bump...
Old chart version: 0.1.0
New chart version: 0.1.0
Error: failed linting charts: failed processing charts

------------------------------------------------------------------------------------------------------------------------
 ✖︎ demo => (version: "0.1.0", path: "charts/demo") > chart version not ok. Needs a version bump!
------------------------------------------------------------------------------------------------------------------------
failed linting charts: failed processing charts
Error: Process completed with exit code 1.
```

Bump the version, then try again:

```yaml
Run ct lint --target-branch main
Linting charts...

------------------------------------------------------------------------------------------------------------------------
 Charts to be processed:
------------------------------------------------------------------------------------------------------------------------
 demo => (version: "0.2.0", path: "charts/demo")
------------------------------------------------------------------------------------------------------------------------

Linting chart "demo => (version: \"0.2.0\", path: \"charts/demo\")"
Checking chart "demo => (version: \"0.2.0\", path: \"charts/demo\")" for a version bump...
Old chart version: 0.1.0
New chart version: 0.2.0
Chart version ok.
Validating /home/runner/work/chart-testing-demo/chart-testing-demo/charts/demo/Chart.yaml...
Validation success! 👍
```

If you're using the same version for your application and chart, set `--check-version-increment` to `false` to disable this check.

> You can also add `Chart.yaml` schema validation that includes custom schema rules, and additional YAML linting that includes configurable rules (indentation for example). The tool comes with defaults which you can find in the [etc](https://github.com/helm/chart-testing/tree/main/etc) folder of the repo.
{: .prompt-info }

If there's additional linting errors, you'll see something like this:

```yaml

charts/demo/values.yaml
  Error: 22:2 [comments] missing starting space in comment
  Error: 35:112 [trailing-spaces] trailing spaces
  Error: 98:2 [comments] missing starting space in comment
```

Fix those errors and try again. The chart in this demo was created using `helm create`, so maybe they should use their own tool to lint the chart 😅.

You'll see another error now:

```yaml
Run ct lint --target-branch main
Linting charts...

------------------------------------------------------------------------------------------------------------------------
 Charts to be processed:
------------------------------------------------------------------------------------------------------------------------
 demo => (version: "0.2.0", path: "charts/demo")
------------------------------------------------------------------------------------------------------------------------

Linting chart "demo => (version: \"0.2.0\", path: \"charts/demo\")"
Checking chart "demo => (version: \"0.2.0\", path: \"charts/demo\")" for a version bump...
Old chart version: 0.1.0
New chart version: 0.2.0
Chart version ok.
Validating /home/runner/work/chart-testing-demo/chart-testing-demo/charts/demo/Chart.yaml...
Validation success! 👍
Validating maintainers...
Error: failed linting charts: failed processing charts

------------------------------------------------------------------------------------------------------------------------
 ✖︎ demo => (version: "0.2.0", path: "charts/demo") > chart doesn't have maintainers
------------------------------------------------------------------------------------------------------------------------
failed linting charts: failed processing charts
```

Update your `Chart.yaml` file to add maintainers and _then_ it should pass! ✅

### Install Command

Once you've fixed your linting errors, you can then test your changes on a cluster. The [ct install](https://github.com/helm/chart-testing/blob/main/doc/ct_install.md) command allows you to install and test a chart with many different, mutually exclusive configurations.

I wanted to keep it simple in this post - so for more details on the various options available, I'd suggest looking at the docs link above.

For a test cluster, a good option is [kind](https://kind.sigs.k8s.io/) as the cluster can be created and destroyed quickly to help with the feedback loop.

To have a look at a successful lint and test of a chart, have a look at this [workflow run](https://github.com/thepaulmacca/chart-testing-demo/actions/runs/11404343387/job/31733302231?pr=1).

> This will work fine in most basic scenarios - but if you're using more advanced tooling and need to connect to external services for your tests, you should use the cluster in your own environment instead of using kind.
{: .prompt-info }

## Summary

Using `ct` adds much more advanced linting and testing capabilities compared to `helm lint`, so you should seriously consider using it for your chart development and testing scenarios.

If you want to see a full example, please check out this [repo](https://github.com/thepaulmacca/chart-testing-demo). I've used GitHub Actions for my demo, as there's some great actions available to use.

Thanks for reading, and please let me know if you've found it useful!
