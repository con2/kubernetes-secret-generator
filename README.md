![Con2 – Collaboration Hub for Volunteer-Run Conventions in Finland](https://con2.fi/media/uploads/2018/08/23/con2_wide_800.png)

# Con2 distribution of `kubernetes-secret-generator`

This is the Con2 distribution of [`kubernetes-secret-generator`](https://github.com/mittwald/kubernetes-secret-generator), forked to automate & control build process. Other users are recommended to use the upstream.

The only local changes are in `deploy/secret-generator.yaml` to use `con2/kubernetes-secret-generator` instead of the upstream.

To install:

    kubectl apply -f https://raw.githubusercontent.com/con2/kubernetes-secret-generator/con2/deploy/secret-generator-rbac.yaml
    kubectl apply -f https://raw.githubusercontent.com/con2/kubernetes-secret-generator/con2/deploy/secret-generator.yaml


# End of Con2 specific information

Original `README.md` follows.

# Automatically generated secrets for Kubernetes

This repository contains a custom Kubernetes controller that can automatically create
random secret values. This may be used for auto-generating random credentials for
applications run on Kubernetes.

## Security note

Older versions (actually, just 0.0.1) of this controller used the `math/rand` package for generating secrets, which is deterministic and not cryptographically secure (see #1 for more information). If you're already running this controller and want to regenerate all potentially compromised secrets, start the controller with the `-regenerate-insecure` flag (note that you will need to manually re-create any Pods using these secrets, though). When using the `kubectl apply` command from below, the new flag will be added to your Deployment automatically.

## Deployment

```shellsession
$ # Create roles and service accounts
$ kubectl apply -f https://raw.githubusercontent.com/mittwald/kubernetes-secret-generator/master/deploy/secret-generator-rbac.yaml
$ # Create actual deployment
$ kubectl apply -f https://raw.githubusercontent.com/mittwald/kubernetes-secret-generator/master/deploy/secret-generator.yaml
```

## Usage

Add the annotation `secret-generator.v1.mittwald.de/autogenerate` to any Kubernetes
secret object. The value of the annotation can be a field name within the secret; the
SecretGeneratorController will pick up this annotation and add a field (`password` in
the example below) to the secret with a randomly generated string value.

```yaml
apiVersion: v1
kind: Secret
metadata:
  annotations:
    secret-generator.v1.mittwald.de/autogenerate: password
data:
  username: c29tZXVzZXI=
```

## Operational tasks

-   Regenerate all automatically generated passwords:

    ```
    $ kubectl annotate secrets --all secret-generator.v1.mittwald.de/regenerate=true
