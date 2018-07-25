---
title: "Helm"
date: 2018-07-25T00:57:40+03:00
anchor: "helm"
weight: 15
---
## Setup helm locally

- For the base setup, see:
- Additionally, install the following plugins
-- [helm secrets](helm plugin install https://github.com/futuresimple/helm-secrets)


## Helm app installation script

- If there is need to encrypt some values from Helmcharts installation folder, you can do it using Ansible vault command.
    1.  Make new yaml file (e.g. values.secrets.yaml)
    2.  Cut/Paste all sensitive values from current config(s) to a newly created file. Save changes. NOTE: If some sensitive value is part of the section/block of values, copy the whole section/block to new yaml file.
    3.  Encrypt new yaml file using ansible-vault command:
        ANSIBLE_VAULT_PASSWORD_FILE=~/.daivb_ansible_vault ansible-vault encrypt name_of_value_secret_file.yaml (e.g. values.secrets.yaml)
    4.  Install helm application using helm-installer.sh script.
        
    Usage:
        ./helm-installer.sh -c "helm install -n app_name app_name -f ./app_name/name_of_values_file1.yaml [ -f ./app_name/name_of_values_file2.yaml ]" \
        -e ./app_name/name_of_value_secret_file.yaml
        
        Arguments:
            -e : encrypted file name [optional, if file exists]
            -c : helm installation command [mandatory], put command between " " !!!

        E.g. nginx-helm app from helmcharts:
                regular values: values1.yaml
                encrypted values: values1.enc.yaml 
        Install Command: ./helm-installer.sh -c "helm install -n nginx-helm nginx-helm -f ./nginx-helm/values1.yaml" -e ./nginx-helm/values1.enc.yaml
