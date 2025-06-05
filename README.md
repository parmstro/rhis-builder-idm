# rhis-builder-idm

Fork it. Clone it. Configure it. Test it. Change it. Commit it. Create a PR.
***

#### Helping out
I know that most of you have experience with these things, but we also work with people with less experience. So, please bear with us if a lot of items that you know are spelled out. If you have any comments, tips, tricks or suggestions to add to the documentation or README.md file, PRs are greatly appreciated. Let's share the wealth!

If you haven't been there yet, please visit [rhis-builder-provisioner](https://github.com/parmstro/rhis-builder-provisioner) first

See rhis-builder-vault-SAMPLE repo for secrets definitions.
***
Hey, welcome. 

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

All of the following are used in multiple projects, so typically you would defined in rhis-builder-vault.yml.
These are passwords used for deployment. It is strongly recommended that these be changed after deployment and subject to a high quality password rotation and escrow methodology.

* ***ipa_admin_principal_vault:*** This is admin.
* ***ipa_admin_password_vault:*** Make is something complex enough for your environment
* ***ipa_principal_username_vault:*** This is an alias for admin principal, the names are not consistent across underlying ansible collections
* ***ipa_principal_password_vault:*** This is an alias for admin password, the names are not consistent across underlying ansible collections

It would be a strong recommendation for a production environment to keep the following in a separate vault file only available at deployment.

* ***ipa_dm_password_vault:*** This the directory manager password and is a highly sensitive in production environments.

### host_vars for idm.example.ca

There is a sample system defined underneath host_vars. The files in the directory cover most of the the configuration options for the roles defined in rhis-builder-idm. You can model you own deployment after these configurations. Please make sure that you review them, especially the DNS configurations as they will need to be adjusted for your environment. 

### Running phase1_setup.yml

```
ansible-playbook -i inventory -e "vault_path=/path/to/vaultfile" -e "vars_path=/path/to/vars_files --vault-password-file=/home/ansiblerunner/.ansible/vault.txt  
```

This will setup your IdM Primary Server

### Next Steps

Once you are happy with your IdM Primary, it is time to deploy your Satellite system with **[rhis-builder-satellite](https://github.com/parmstro/rhis-builder-satellite)**
