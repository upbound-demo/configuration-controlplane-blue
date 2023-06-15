# Configuration For ControlPlane Blue

This repository contains the configuration for a specific control plane called
`controlplane-blue`. It contains specific information such as IAM role ARN and
paths to the secret store that are allocated for this control plane. If you'd
like to use this in another control plane, you need to fork and make the necessary
changes there.

The content is roughly as the following:
* [`crossplane.yaml`](crossplane.yaml): Contains the configuration dependencies
  that will be installed to the control plane. 
  * **NOTE**: You cannot install this as `Configuration` package unless the
    packages it depends on can be pulled. You need to authorize the Crossplane
    package manager and the cluster it's being deployed to for pulling from
    private repositories or make sure to add all private packages to the list with
    `imagePullSecret` reference and have the `Secret` ready in your control plane.
* [`.up`](.up/): This folder includes all the manifests that would be installed
  when you use this repository as a root configuration. It has the config objects
  such as `ProviderConfig`, `ControllerConfig`, `EnvironmentConfig`, secret
  store configurations as well as observed resources, i.e. managed resources that
  have `Observe` management policy.

**NOTE**: You cannot use this package in an upstream Crossplane cluster as is
since the providers it depends on reference a `ControllerConfig` and expect it
to be present. It's stored in `.up/extension-root/configs/aws` folder which is
deployed only if you are using Universal Crossplane from Upbound.

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
  configuration:
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