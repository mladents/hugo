---
title: "Overview"
date: 2018-07-25T00:57:40+03:00
anchor: "overview"
weight: 10
---

## Quick getting started

1. Get client certificate
2. Install stunnel and deploy config file
3. Login to [dex-k8s-authentication](https://dvb-admin-poc-int-ece.daimler.com/dex-kubernetes-auth/) and install certificates/create kubeconfig
4. Authorization
    1. If you are an workspace admin user: Provide the value of the sub claim to the kubernetes support 
    2. If you are a regular user: Provide the value of the sub claim to the workspace leads and ask them to add your subject to the specific role bindings 

## Overview

We provide access to the kubernetes API over the Internet, protected by two factors:
- An OpenID connect id_token, authenticated through the Daimler username/password (1.st factor), or some of the other provided login options
- A client certificate (2.nd factor)

Authenticating the id_token in the first step does not provide access to the API yet. Apart from the login process, a specific user ID also needs authorization to the kubernetes resources. Kindly refer to the description in the section "Authorization"

### stunnel

As the kubernets commnand line tools to not support multiple factors for authentication, we tunnel the HTTPS traffic through an additional TLS tunnel. Refer to the seperate documentation in "Setting up stunnel" on how to obtain a client certificate and how to setup stunnel

### Authentication

See the section "Get Kubeconfig" on how to authenticate and setup you local kubernetes config.

### Authorization

An authenticated id_token and an stunnel connection does not automatically guarantee access to kubernetes resources. The authenticated user id needs to be explicitly added to the set of roles controlling access to the namespace in which the access resource exist. These roles are usually managed by the namespace administrators in the form a kubernetes resources called `Roles` and `RoleBindings`. Please check back with teh namespace administrators to grant access.