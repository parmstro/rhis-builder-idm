# rhis-builder-idm
The code in this repo builds the Red Hat Identity Management configuration for a Red Hat Infrastructure Standard (RHIS) deployment.  Currently, this deployment assumes the Red Hat Infrastructure Standard Adoption Model (RH_ISAM) and deploys your first node in the architecture, the Identity Management Primary instance. In a later phase of the implementation, the code will deploy Identity Management replicas. At this time we are supporting simple configurations in which all IdM replicas deploy the same configuration of components. Subsequent iterations will provide for more comprehensive deployments. Please open an issue to suggest deployment models that you would like to see.


### What rhis-builder-idm does

rhis-builder-idm makes the following changes to a system installed with an @Base RHEL system build:
- ensures the systems are registered 
  - first system must register directly to the CDN
  - subsequent runs can have replicas built from the Satellite
- configures all IdM prerequisites
- can run an external certificate installation.
- installs IdM Primary Server with DNS, CA, KRA, AD Trust, etc..
- configures the IdM global configuration
- configures IdM ID Ranges if present
- configures IdM Identity Provider references for OAuth
- configures sample Users and Groups
- configures IdM password policy
- configures host groups
- adds hosts (typically those that don't use an IdM client)
- configures HBAC Policy
- configures Sudo Policy
- configures DNS

### Secrets management


### Running phase1_setup.yml

```
ansible-playbook -i inventory -e "vault_path=/path/to/vaultfile" -e "vars_path=/path/to/vars_files --vault-password-file=/home/ansiblerunner/.ansible/vault.txt  
```

This will setup your IdM Primary Server

# Next Steps