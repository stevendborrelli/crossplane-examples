# Example of Creating an AWS StateMachine

This example defines a StateMachine Platform API and a Composition which creates the following AWS resources using [function-go-templating](https://github.com/crossplane-contrib/function-go-templating).

- An IAM Role
- An AWS CloudwatchLogs Group
- A StateMachine

## Local Testing

Use `crossplane beta render` to render the manifest. Example resources have been saved to the `render/observed` directory to simulate existing Managed Resources.

```shell
crossplane beta render -r examples/helloworld.yaml apis/composition.yaml functions.yaml -o render/observed
```

## Cluster Install

A minimum of Crossplane 1.14.2 should be installed.

Install the Functions and the Providers:

```shell
$ kubectl apply -f functions.yaml -f providers.yaml
function.pkg.crossplane.io/function-go-templating created
function.pkg.crossplane.io/function-auto-ready created
provider.pkg.crossplane.io/upbound-provider-aws-cloudwatch created
provider.pkg.crossplane.io/upbound-provider-aws-cloudwatchlogs created
provider.pkg.crossplane.io/upbound-provider-aws-iam created
provider.pkg.crossplane.io/upbound-provider-aws-sfn created
```

Install the API Definition and Composition. You can ignore the `Warning:` during the initial installation:

```shell
$ kubectl apply -f apis/
composition.apiextensions.crossplane.io/xstatemachine.example.crossplane.io created
compositeresourcedefinition.apiextensions.crossplane.io/xstatemachines.sfn.aws.example.com created
```

Set up Authentication to AWS using the instructions at [AUTHENTICAION.md](https://github.com/crossplane-contrib/provider-upjet-aws/blob/main/AUTHENTICATION.md).

## Create a StateMachine

The `examples` directory contains example StateMachines:

```shell
$ kubectl apply -f examples/helloworld.yaml 
xstatemachine.sfn.aws.example.com/hello-world created
```

You can trace the provisioning of the resources using `crossplane beta trace`:

```shell
crossplane beta trace xstatemachine.sfn.aws.example.com/hello-world

NAME                                SYNCED   READY   STATUS
XStateMachine/hello-world           True     True    Available
├─ Group/hello-world-nxtg6          True     True    Available
├─ Role/hello-world-4jwsj           True     True    Available
└─ StateMachine/hello-world-tt4wv   True     True    Available
```
