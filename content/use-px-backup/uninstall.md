---
title: Uninstall PX-Backup
description: Upgrade your version of PX-Backup
keywords: upgrade,
weight: 11
hidesections: true
disableprevnext: true
scrollspy-container: false
type: common-landing
---

If you're running PX-Backup as a Helm-based install, you can uninstall it using Helm.

1. Enter the following `helm delete` command with the `--namespace` flag and the namespace your PX-Backup cluster is install on:

    ```text
    helm delete px-backup --namespace px-backup
    ```

2. Cleanup the secrets and PVCs created by PX-Backup:

    ```text
    kubectl delete namespace px-backup
    ```