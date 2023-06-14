# Configuration For ControlPlane Blue

This repository contains the configuration for `controlplane-blue`. The
content is roughly as the following:
* [`crossplane.yaml`](crossplane.yaml): Contains the configuration dependencies
  that this control plane deploys. This is what's installed when you install
  this package as upstream `Configuration` package using Crossplane or Universal
  Crossplane.
  * **NOTE**: You cannot install this as `Configuration` package unless the
    packages it depends on can be pulled. You need to authorize the Crossplane
    package manager and the cluster it's being deployed to for pulling from
    private repositories since the `dependsOn` array cannot have image pull
    secret reference because that's a runtime configuration that cannot be
    shared across clusters.
* [`.up`](.up/): This folder includes all the manifests that would be installed
  when you use this repository as a control plane source. It has the most
  fine-grained details about all the dependencies declared in `crossplane.yaml`
  as well as settings to configure how the control plane runs providers,
  configurations as well as secret stores, common imported resources etc.

## Getting Started

In your Upbound Space, create your `ControlPlane` pointing to this
repository.

Create a `ControlPlane` pointing to this repository.
```yaml
apiVersion: mxp.upbound.io/v1alpha1
kind: ControlPlane
metadata:
  name: controlplane-blue
spec:
  source:
    # Can have "type: Image" as option in future.
    type: Git
    git:
      url: https://github.com/upbound-demo/configuration-controlplane-blue
      ref:
        branch: main

  # Publishes to an external secret store like Vault.
  publishConnectionDetailsTo:
    name: kubeconfig-controlplane-blue
    configRef:
      name: vault
    metadata:
      labels:
        environment: development
        team: backend
```