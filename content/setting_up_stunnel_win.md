---
title: "Setting up stunnel - Windows"
date: 2018-07-25T00:57:40+03:00
anchor: "stunnel windows"
weight: 21
---

## Overview

## Download and install stunnel

stunnel can be downloaded as binary for the most common operation systems, or it can be compiled from source. You will find the most current version on the project website: [stunnel](https://www.stunnel.org/). Install stunnel using win32 installer (accepting default values!!!). Installation folder should be: `C:\Program Files (x86)\stunnel`


## Download and install kubectl

Recommended way of kubectl installation on Windows is using Chocolatey installer: 

```bash
choco install kubernetes-cli
```  

Useful links: [Chocolatey installation](https://chocolatey.org/install) and [Install kubectl with Chocolatey](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-chocolatey-on-windows)


## Obtain client certificate

Request a client certificate here [Todo: Integrate Self Service PKI](https://does.not.exist.yet) 
(Work In Progress - Implement user's certs as a part of DaiVB Kubernetes Login page)

stunnel expects the client certificate in the form of a pem file, containing certificate and private key (can be encrypted). Here's a good resource explaining on how to generate a pem file in case the PKI provides th credentials in a different format [Stackoverflow](https://stackoverflow.com/questions/9497719/extract-public-private-key-from-pkcs12-file-for-later-use-in-ssh-pk-authenticati) 

HINT: Openssl tool is included in stunnel installation. To convert/export your client certificate, open `Command Prompt`, navigate to `C:\Program Files (x86)\stunnel\bin` and type: 

```bash
openssl pkcs12 -in YOUR_CLIENT_CERT_FILE.pfx -nocerts -out daivb-kube-client-key.pem
openssl pkcs12 -in YOUR_CLIENT_CERT_FILE.pfx -nokeys -clcerts -out daivb-kube-client-cert.pem
```  

## Setup stunnel

1. During installation, stunnel creates a folder structure in `C:\Program Files (x86)\stunnel`. Open Powershell console (as Admin!!!) and navigate to config folder (`C:\Program Files (x86)\stunnel\config`)
2. Place the pem files containing your client cert/private key in folder `C:\Program Files (x86)\stunnel\config`
3. Create a self-signed certificate with CN=localhost. In Powershell console (started as Admin!!!) type these commands:

```bash
$stunnel_config='C:\Program Files (x86)\stunnel\config'

$env:Path += ";C:\Program Files (x86)\stunnel\bin\"

openssl req -new -newkey rsa:2048 -days 365 -subj '/CN=localhost' -nodes -x509 -keyout $stunnel_config\localhost.key -out $stunnel_config\localhost.crt -config $stunnel_config\openssl.cnf
```  
4. Create a config file called `stunnel.conf`. Use Powershell console from previous step and copy/paste this command:

```bash
echo ';Debugging stuff (may be useful for troubleshooting)
debug = 7
;output = stunnel.log

[daivb-kube-tunnel-in]
client = no
accept = localhost:7000
connect = localhost:6000
cert = localhost.crt
key =  localhost.key

[daivb-kube-tunnel-out]
client = yes
accept = localhost:6000
connect = dvb-kubernetes-poc-int-ece.daimler.com:443
cert = daivb-kube-client-cert.pem
key =  daivb-kube-client-key.pem' | Out-File -encoding Utf8 $stunnel_config\stunnel.conf
```  

*Note*: By default, stunnel will start to listen for connections from `kubectl` or `helm` on localhost:7000. If you change this port, remember to also change it in your local kubeconfig file. We suggest to leave this untouched. 
 