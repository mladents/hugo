---
title: "Get kubeconfig"
date: 2018-07-25T00:57:40+03:00
anchor: "kubeconfig"
weight: 30
---

## Authentication

In order to obtain the id_token, visit the DaiVB Kubernetes Service portal first. Navigate to the following URL: [DaiVB Kubernetes Login](https://dvb-admin-poc-int-ece.daimler.com/dex-kubernetes-auth/)

Once authenticated, follow exactly the steps as outlined and create your kubernetes config file. This file is used by both `kubectl` and `helm` comamnd line tools likewise to access the kubernetes cluster.

## Authorization

Copy the value of your "sub" field from the token details (the beginning of the DaiVB Kubernetes Login page). You need to send this value to your Workspace Operator/Kubernetes Admin in order to get access to the kubernetes resources.    