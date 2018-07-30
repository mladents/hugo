---
title: "Setting up stunnel"
date: 2018-07-25T00:57:40+03:00
anchor: "stunnel"
weight: 20
---

## Overview

## Download stunnel and install

stunnel can be downloaded as binary for the most common operation systems, or it can be compiled from source. You will find the most current version on the project website: [stunnel](https://www.stunnel.org/)

## Obtain client certificate

Request a client certificate here [Todo: Integrate Self Service PKI](https://does.not.exist.yet)

stunnel expects the client certificate in the form of a pem file, containing certificate and private key (can be encrypted). Here's a good resource explaining on how to generate a pem file in case the PKI provides th credentials in a different format [Stackoverflow](https://stackoverflow.com/questions/9497719/extract-public-private-key-from-pkcs12-file-for-later-use-in-ssh-pk-authenticati) 

## Setup stunnel

1. Create a directory and navigate to it
2. Place the pem file containing your cert/private key in a file call `daivb-kube-client.pem`
3. Create a self-signed certificate with CN=localhost and place it in `localhost.pem`

```bash
openssl req -new -newkey rsa:2048 -days 365 -subj '/CN=localhost' -nodes -x509 -keyout localhost.key -out localhost.crt
cat localhost.crt localhost.key > localhost.pem
```

4. Create a config file called `stunnel-daivbkube.conf` and add the following content

```text
; Debugging stuff (may be useful for troubleshooting)
foreground = yes
debug = 7
output = stunnel.log


[daivb-kube-tunnel-in]
client = no
accept = localhost:7000
connect = localhost:6000
cert = localhost.pem


[daivb-kube-tunnel-out]
client = yes
accept = localhost:6000
connect = dvb-kubernetes-poc-int-ece.daimler.com:443
cert = daivb-kube-client.pem
key =  daivb-kube-client.pem
```

*Note*: By default, stunnel will start to listen for connections from `kubectl` or `helm` on localhost:7000. If you change this port, remember to also change it in your local kubeconfig file. We suggest to leave this untouched. 
 