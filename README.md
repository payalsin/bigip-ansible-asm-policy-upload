# bigip-ansible-asm-policy-upload
Ansible role to upload a ASM policy, activate it and assign it to a Virtual Server

This is a workflow to
* Create a LTM profile (to add ASM rules to it)
* Upload and active the ASM policy
* Add a rule to enable ASM policy in the LTM policy
* Create node members
* Create pool
* Create virtual server (attach ASM policy created above to the VS)
   
## Requirements
* This role requires Ansible 2.5
* BIG-IP is licensed
* Packages to be installed
  - pip install f5-sdk
  - pip install bigsuds
  - pip install netaddr
* ASM policy exists in the files folder of the rule
 (In this role the ASM policy present is valid for only BIG-IP version 12.1.2)
 (Different versions of BIG-IP will need an ASM policy corresponding to it's release) 

## Role Variables
The variables that can be passed to this role and a brief description about them are as follows.

```
#Pool memeber information
pool_members:
- port: "80"
  host: "192.168.68.140"
- port: "80"
  host: "192.168.68.141"
  
vip_name: "WAF-Enabled-VIP"
vip_ip: "10.168.68.77"
vip_port: "80"
```

## Example Playbook
```
- name: Configure http service
  hosts: bigip
  gather_facts: false
  connection: local
    
  #Environment variables defined
  environment:
    F5_SERVER: "{{ inventory_hostname }}"
    F5_USER: "{{ username }}"
    F5_PASSWORD: "{{ password }}"
    F5_SERVER_PORT: "443"
    F5_VALIDATE_CERTS: "False"
    
  roles:
  - { role: payalsin.bigip-ansible-asm-policy-upload }

```

## Credential storage

Because this role includes usage of credentials to access your BIG-IP, I recommend that you supply these variables in an ansible-vault encrypted file.

This can be supplied out-of-band of this role

Steps:
- Store your vault password in a file - '~/.vault_pass.txt'
- Execute playbook as follows - ansible-vault encrypt <<variable_filename>> --vault-password-file ~/.vault_pass.txt

For more information refer to: http://docs.ansible.com/ansible/latest/playbooks_vault.html

## Certificate validation
To validate the SSL certificates of the BIG-IP REST API
- set validate_certs: true
- Generate a public private key pair
- Store the public key on BIG-IP (https://support.f5.com/csp/article/K13454#bigipsshdaccept)

## Credits
https://github.com/F5Networks/f5-ansible
