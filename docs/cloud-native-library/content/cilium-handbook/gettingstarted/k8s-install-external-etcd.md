::: {.only}
not (epub or latex or html)

WARNING: You are looking at unreleased Cilium documentation. Please use
the official rendered version released here: <https://docs.cilium.io>
:::

Installation with external etcd[]{#admin_install_daemonset} {#k8s_install_etcd}
===========================================================

This guide walks you through the steps required to set up Cilium on
Kubernetes using an external etcd. Use of an external etcd provides
better performance and is suitable for larger environments.

Should you encounter any issues during the installation, please refer to
the `troubleshooting_k8s`{.interpreted-text role="ref"} section and / or
seek help on `slack`{.interpreted-text role="ref"}.

When do I need to use a kvstore?
--------------------------------

Unlike the section `k8s_quick_install`{.interpreted-text role="ref"},
this guide explains how to configure Cilium to use an external kvstore
such as etcd. If you are unsure whether you need to use a kvstore at
all, the following is a list of reasons when to use a kvstore:

> -   If you are running in an environment where you observe a high
>     overhead in state propagation caused by Kubernetes events.
> -   If you do not want Cilium to store state in Kubernetes custom
>     resources (CRDs).
> -   If you run a cluster with more pods and more nodes than the ones
>     tested in the `scalability_guide`{.interpreted-text role="ref"}.

::: {#ds_deploy}
:::

You will also need an external etcd version 3.1.0 or higher.

Configure Cilium
----------------

When using an external kvstore, the address of the external kvstore
needs to be configured in the ConfigMap. Download the base YAML and
configure it with `Helm`{.interpreted-text role="term"}:

Deploy Cilium release via Helm:

::: {.parsed-literal}

helm install cilium \\

:   \--namespace kube-system \\ \--set etcd.enabled=true \\ \--set
    \"etcd.endpoints\[0\]=http://etcd-endpoint1:2379\" \\ \--set
    \"etcd.endpoints\[1\]=http://etcd-endpoint2:2379\" \\ \--set
    \"etcd.endpoints\[2\]=http://etcd-endpoint3:2379\"
:::

If you do not want Cilium to store state in Kubernetes custom resources
(CRDs), consider setting `identityAllocationMode`:

    --set identityAllocationMode=kvstore

### Optional: Configure the SSL certificates

Create a Kubernetes secret with the root certificate authority, and
client-side key and certificate of etcd:

``` {.shell-session}
kubectl create secret generic -n kube-system cilium-etcd-secrets \
    --from-file=etcd-client-ca.crt=ca.crt \
    --from-file=etcd-client.key=client.key \
    --from-file=etcd-client.crt=client.crt
```

Adjust the helm template generation to enable SSL for etcd and use https
instead of http for the etcd endpoint URLs:

::: {.parsed-literal}

helm install cilium \\

:   \--namespace kube-system \\ \--set etcd.enabled=true \\ \--set
    etcd.ssl=true \\ \--set
    \"etcd.endpoints\[0\]=https://etcd-endpoint1:2379\" \\ \--set
    \"etcd.endpoints\[1\]=https://etcd-endpoint2:2379\" \\ \--set
    \"etcd.endpoints\[2\]=https://etcd-endpoint3:2379\"
:::
