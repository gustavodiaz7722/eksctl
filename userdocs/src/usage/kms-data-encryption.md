# KMS Envelope Encryption for EKS clusters
Amazon Elastic Kubernetes Service (Amazon EKS) provides default envelope encryption for all Kubernetes API data in EKS clusters running Kubernetes version 1.28 or higher. This provides a managed, default experience that implements defense-in-depth for your Kubernetes applications and doesn’t require any action on your part. Amazon EKS uses [AWS Key Management Service (KMS)](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) with [Kubernetes KMS provider v2](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/#configuring-the-kms-provider-kms-v2) for this additional layer of security with an [AWS owned key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-owned-cmk), and the option for you to bring your own [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) (CMK) from AWS KMS. 

Envelope encryption in Amazon EKS is now a default configuration that is enabled in all clusters running Kubernetes version 1.28 or higher. The AWS KMS integration is established by the Kubernetes API server managed by AWS. This means you do not need to configure any permissions to start using KMS encryption for your cluster.


## Creating a cluster with a customer managed key(CMK)
By default create cluster EKS API will provide envelope encryption with an AWS-managed key . If you wish to bring your own KMS key for envelope encryption, use the following example below to create a cluster using a CMK. 
```yaml
# kms-cluster.yaml
# A cluster with CMK encryption enabled for all kubernetes API data, using customer managed key
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: kms-cluster
  region: us-west-2
managedNodeGroups:
- name: ng
# more config
kubernetesDataEncryption:
  # Customer managed key used for envelope encryption of all Kubernetes API data in EKS clusters running Kubernetes version 1.28 or higher
  keyARN: arn:aws:kms:us-west-2:<account>:key/<key>
```
```shell
$ eksctl create cluster -f kms-cluster.yaml
```
## Enabling CMK encryption on an existing cluster
To use a CMK on a cluster that currently uses an AWS-managed key, you can use the [aws cli]() - Run
```shell
$ aws
$ eksctl utils enable-secrets-encryption -f kms-cluster.yaml
```
???+ note
    Once KMS encryption is enabled with a CMK, it cannot be disabled or reverted to using an AWS-managed key.