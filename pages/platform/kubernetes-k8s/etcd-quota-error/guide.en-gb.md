---
title: ETCD Quotas error, troubleshooting
excerpt: ''
slug: etcd-quota-error
section: FAQ
---

**Last updated March 15<sup>th</sup>, 2021.**

Some customers might encounter troubles with the quota on ETCD storage usage.

## Issue

When managing your Kubernetes cluster, you may encounter the following error:

```log
"Error from server: rpc error: code = Unknown desc = ETCD storage quota exceeded"
```

Each Kubernetes cluster has a dedicated quota on ETCD storage usage,
calculated through the following formula:
*Quota = 10MB + (25MB per node)* (capped to 200MB)

For example, a cluster with 3 `b2-7` servers has a quota of 85MB.

The quota can thus be increased by adding nodes, but will never be decreased (even if all nodes are removed) to prevent data loss.

## Resolution

The error mentioned above states that the cluster's ETCD storage usage has exceeded the quota.
To resolve the situation, resources created in excess need to be deleted.

### Cert-Manager

Most users install cert-manager through Helm, and then move on a bit hastily.

The most common cases of ETCD quota issues come from a bad configuration of [cert-manager](https://cert-manager.io/docs/),
making it continuously create `certificaterequest` resources.

This behaviour will fill the ETCD with resources until the quota is reached.

To detect this situation, you can get the number of `certificaterequest` resources:

```bash
kubectl get certificaterequests -A | wc -l
```

If you have a huge number of certificate requests (+1000 for example), you have found the root cause.

To solve that,we propose this way:

* Stop the operator cert-manager

```bash
kubectl scale deployment --replicas 0 cert-manager
```

* flush all useless certificate request

```bash
kubectl delete certificaterequests --all
```

* Update cert-manager

There is no generic way to do this, but if you use Helm we recommend you to use it for the update.
[Cert Manager official documentation](https://cert-manager.io/docs/installation/kubernetes/)

* Fix the issue

We recommend you to take the following steps to troubleshoot your cert-manager, and to ensure that everything is correctly configured:
[Acme troubleshoot](https://cert-manager.io/docs/faq/acme/)

* Start cert-manager

### Other usecase

If cert-manager is not the root causes, you should turn to the other running operators.

We have found that the following resources can sometimes be generated continuously by existing operators:
> backups.velero.io
ingress.networking.k8s.io
ingress.extensions

If that still  does not cover your case, you can use a tool like [ketall](https://github.com/corneliusweig/ketall) to easily list and count resources in your cluster.

---
