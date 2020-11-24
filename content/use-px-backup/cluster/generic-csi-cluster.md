---
title: Kubernetes clusters using generic CSI drivers
linkTitle: Kubernetes with generic CSI drivers
description: 
keywords:
weight: 4
disableprevnext: true
# scrollspy-container: false
---

<!-- 

This is still kubernetes, but using a different storage driver, right? Is it more correct to say that we're talking about Kubernetes clusters with Generic CSI drivers?

-->

PX-Backup supports backup and restore on kubernetes clusters with generic CSI drivers if the driver meets certain prerequisites. 

{{<info>}}
**NOTE:** Portworx features native CSI driver support for cloud providers with their own native CSI driver support, such as AKS, EKS, and GKE. You do not need to follow these instructions to use CSI features on those clusters with PX-Backup. 
{{</info>}}

<!-- 

Not sure I understand what this note is saying. Are you saying that you don't need to follow these instructions for CSI support on one of the cloud clusters we list in this section, because both the providers and Portworx feature native CSI support?

-->

## Prerequisites

Kubernetes prerequisites:

* Kubernetes clusters must be at least version 1.17 to use the Kubernetes CSI Snapshotting Beta feature.
* Kubernetes clusters must have the [snapshot-controller](https://github.com/kubernetes-csi/external-snapshotter/tree/master/deploy/kubernetes/snapshot-controller) version 3.0 or greater.

The generic CSI driver should implement the following:
  
* The CSI driver must include the [CSI snapshots and restores](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) feature.
* The CSI Driver must have the `snapshotter-sidecar` running at version 2.0 or greater.
* THe CSI Driver must have the `external-snapshotter` running at version 2.0 or greater.
* Ideally, the CSI driver should implement `ListSnapshots` for indicating that the snapshot is ready to use on the storage backend.
* For cross-cluster restores, the CSI drivers for both clusters will need to be connected to the same storage backend. In particular, the `snapshotHandle` used on the source cluster must be visible on the destination cluster via CSI `ListSnapshots`.

Portworx has tested and recommends the following CSI drivers for use with your Kubernetes clusters:

* Pure Storage PSO CSI Driver
* Openshift OCS RBD CSI Driver
* DigitalOcean CSI Driver

## Add the cluster to PX-Backup

1. From the home page, select **Add Cluster**:

    ![Add cluster](/img/add-cluster.png)

2. On the **Add Kubernetes Cluster** page, enter the cluster details:

    * The name of the cluster
    * Retrieve the Kubeconfig from your cluster and paste it in the **Kubeconfig** text frame, or select the **Browse** button to upload it from a file.
    * Select the **Others** radio button from the **Kubernetes Service** radio group

    ![Enter the cluster details](/img/enter-other-kubernetes-distributions-cluster-details.png)

3. Select the **Submit** button

<!-- 

    I used the cluster add steps from the on-prem guide, do those work?  

-->