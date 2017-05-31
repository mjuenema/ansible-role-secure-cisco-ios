Ansible connection provider settings
====================================

The following settings are neccessary to allow Ansible to SSH into the router/switch
and change to enable-mode. If these variables are undefined the respective values
must be provided as environment variables. For more information check the provider
section of the `ios_config module`_. It is strongly recommended to store the sensitive
variables in an encrypted `Ansible Vault`_.

.. _`ios_config_module`: https://docs.ansible.com/ansible/ios_config_module.html
.. _`Ansible Vault`: http://docs.ansible.com/ansible/playbooks_vault.html

.. code-block:: yaml

   secure_cisco_ios_provider_username:
   secure_cisco_ios_provider_password:
   secure_cisco_ios_provider_authorize: yes
   secure_cisco_ios_provider_auth_pass:
   secure_cisco_ios_provider_timeout: 10
   secure_cisco_ios_provider_port: 22

Currently SSH public-key authentication is not supported by this role.
