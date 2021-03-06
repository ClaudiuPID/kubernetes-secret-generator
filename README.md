# Automatically generated secrets for Kubernetes

This repository contains a custom Kubernetes controller that can automatically create
random secret values. This may be used for auto-generating random credentials for
applications run on Kubernetes.

## Security note

Older versions (>= 1.0.0) of this controller used the `math/rand` package for generating secrets, which is deterministic and not cryptographically secure (see #1 for more information). If you're already running this controller and want to regenerate all potentially compromised secrets, start the controller with the `-regenerate-insecure` flag (note that you will need to manually re-create any Pods using these secrets, though). When using the `kubectl apply` command from below, the new flag will be added to your Deployment automatically.

## Deployment

The controller can be deployed using [Helm](https://helm.sh):

```shellsession
$ # clone repository
$ git clone https://github.com/mittwald/kubernetes-secret-generator
$ # create helm deployment
$ helm upgrade --install secret-generator ./deploy/chart
```

## Usage

Add the annotation `secret-generator.v1.mittwald.de/autogenerate` to any Kubernetes
secret object. The value of the annotation can be a field name 
(or comma separated list of field names) within the secret; the
SecretGeneratorController will pick up this annotation and add a field [or fields] 
(`password` in the example below) to the secret with a randomly generated string value.

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
    ```
    
-   Regenerate only certain fields
    ```
    $ kubectl annotate secrets --all secret-generator.v1.mittwald.de/regenerate=password1,password2
    ```
