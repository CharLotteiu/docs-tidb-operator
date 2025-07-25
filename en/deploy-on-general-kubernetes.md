---
title: Deploy TiDB on General Kubernetes
summary: Learn how to deploy a TiDB cluster on general Kubernetes.
aliases: ['/docs/tidb-in-kubernetes/dev/deploy-on-general-kubernetes/','/tidb-in-kubernetes/dev/deploy-tidb-enterprise-edition']
---

# Deploy TiDB on General Kubernetes

This document describes how to deploy a TiDB cluster on general Kubernetes.

## Prerequisites

- Meet [prerequisites](prerequisites.md).
- Complete [deploying TiDB Operator](deploy-tidb-operator.md).
- [Configure the TiDB cluster](configure-a-tidb-cluster.md)

## Deploy the TiDB cluster

1. Create `Namespace`:

    {{< copyable "shell-regular" >}}

    ``` shell
    kubectl create namespace ${namespace}
    ```

    > **Note:**
    >
    > A [`namespace`](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) is a virtual cluster backed by the same physical cluster. You can give it a name that is easy to memorize, such as the same name as `cluster_name`.

2. Deploy the TiDB cluster:

    {{< copyable "shell-regular" >}}

    ``` shell
    kubectl apply -f ${cluster_name} -n ${namespace}
    ```

    > **Note:**
    >
    > It is recommended to organize configurations for a TiDB cluster under a directory of `cluster_name` and save it as `${cluster_name}/tidb-cluster.yaml`.

    If the server does not have an external network, you need to download the Docker image used by the TiDB cluster on a machine with Internet access and upload it to the server, and then use `docker load` to install the Docker image on the server.

    To deploy a TiDB cluster, you need the following Docker images (assuming the version of the TiDB cluster is {{{ .tidb_version }}}):

    ```shell
    pingcap/pd:{{{ .tidb_version }}}
    pingcap/tikv:{{{ .tidb_version }}}
    pingcap/tidb:{{{ .tidb_version }}}
    pingcap/ticdc:{{{ .tidb_version }}}
    pingcap/tiflash:{{{ .tidb_version }}}
    pingcap/tiproxy:latest
    pingcap/tidb-monitor-reloader:v1.0.1
    pingcap/tidb-monitor-initializer:{{{ .tidb_version }}}
    grafana/grafana:7.5.11
    prom/prometheus:v2.18.1
    busybox:1.26.2
    ```

    Next, download all these images with the following command:

    {{< copyable "shell-regular" >}}

    ```shell
    docker pull pingcap/pd:{{{ .tidb_version }}}
    docker pull pingcap/tikv:{{{ .tidb_version }}}
    docker pull pingcap/tidb:{{{ .tidb_version }}}
    docker pull pingcap/ticdc:{{{ .tidb_version }}}
    docker pull pingcap/tiflash:{{{ .tidb_version }}}
    docker pull pingcap/tiproxy:latest
    docker pull pingcap/tidb-monitor-reloader:v1.0.1
    docker pull pingcap/tidb-monitor-initializer:{{{ .tidb_version }}}
    docker pull grafana/grafana:7.5.11
    docker pull prom/prometheus:v2.18.1
    docker pull busybox:1.26.2

    docker save -o pd-{{{ .tidb_version }}}.tar pingcap/pd:{{{ .tidb_version }}}
    docker save -o tikv-{{{ .tidb_version }}}.tar pingcap/tikv:{{{ .tidb_version }}}
    docker save -o tidb-{{{ .tidb_version }}}.tar pingcap/tidb:{{{ .tidb_version }}}
    docker save -o ticdc-{{{ .tidb_version }}}.tar pingcap/ticdc:{{{ .tidb_version }}}
    docker save -o tiproxy-latest.tar pingcap/tiproxy:latest
    docker save -o tiflash-{{{ .tidb_version }}}.tar pingcap/tiflash:{{{ .tidb_version }}}
    docker save -o tidb-monitor-reloader-v1.0.1.tar pingcap/tidb-monitor-reloader:v1.0.1
    docker save -o tidb-monitor-initializer-{{{ .tidb_version }}}.tar pingcap/tidb-monitor-initializer:{{{ .tidb_version }}}
    docker save -o grafana-6.0.1.tar grafana/grafana:7.5.11
    docker save -o prometheus-v2.18.1.tar prom/prometheus:v2.18.1
    docker save -o busybox-1.26.2.tar busybox:1.26.2
    ```

    Next, upload these Docker images to the server, and execute `docker load` to install these Docker images on the server:

    {{< copyable "shell-regular" >}}

    ```shell
    docker load -i pd-{{{ .tidb_version }}}.tar
    docker load -i tikv-{{{ .tidb_version }}}.tar
    docker load -i tidb-{{{ .tidb_version }}}.tar
    docker load -i ticdc-{{{ .tidb_version }}}.tar
    docker load -i tiproxy-latest.tar
    docker load -i tiflash-{{{ .tidb_version }}}.tar
    docker load -i tidb-monitor-reloader-v1.0.1.tar
    docker load -i tidb-monitor-initializer-{{{ .tidb_version }}}.tar
    docker load -i grafana-6.0.1.tar
    docker load -i prometheus-v2.18.1.tar
    docker load -i busybox-1.26.2.tar
    ```

3. View the Pod status:

    {{< copyable "shell-regular" >}}

    ``` shell
    kubectl get po -n ${namespace} -l app.kubernetes.io/instance=${cluster_name}
    ```

You can use TiDB Operator to deploy and manage multiple TiDB clusters in a single Kubernetes cluster by repeating the above procedure and replacing `cluster_name` with a different name.

Different clusters can be in the same or different `namespace`, which is based on your actual needs.

> **Note:**
>
> If you need to deploy a TiDB cluster on ARM64 machines, refer to [Deploy a TiDB Cluster on ARM64 Machines](deploy-cluster-on-arm64.md).

## Initialize the TiDB cluster

If you want to initialize your cluster after deployment, refer to [Initialize a TiDB Cluster on Kubernetes](initialize-a-cluster.md).

> **Note:**
>
> By default, TiDB (versions starting from v4.0.2 and released before February 20, 2023) periodically shares usage details with PingCAP to help understand how to improve the product. For details about what is shared and how to disable the sharing, see [Telemetry](https://docs.pingcap.com/tidb/stable/telemetry). Starting from February 20, 2023, the telemetry feature is disabled by default in newly released TiDB versions. See [TiDB Release Timeline](https://docs.pingcap.com/tidb/stable/release-timeline) for details.

## Configure TiDB monitoring

For more information, see [Deploy monitoring and alerts for a TiDB cluster](monitor-a-tidb-cluster.md).

> **Note:**
>
> TiDB monitoring does not persist data by default. To ensure long-term data availability, it is recommended to [persist monitoring data](monitor-a-tidb-cluster.md#persist-monitoring-data). TiDB monitoring does not include Pod CPU, memory, or disk monitoring, nor does it have an alerting system. For more comprehensive monitoring and alerting, it is recommended to [Set kube-prometheus and AlertManager](monitor-a-tidb-cluster.md#set-kube-prometheus-and-alertmanager).

## Collect logs

System and application logs can be useful for troubleshooting issues and automating operations. By default, TiDB components output logs to the container's `stdout` and `stderr`, and log rotation is automatically performed based on the container runtime environment. When a Pod restarts, container logs will be lost. To prevent log loss, it is recommended to [Collect logs of TiDB and its related components](logs-collection.md).
