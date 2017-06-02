### Example playbook

#### Playbook variables

Variables for this playbook are distributed across three different files.

* [`inventory`](inventory) contains the device specific variables.
* `vault.yml` contains variables that must be kept safe.
* [`vars.yml`](vars.yml) contains general variables.

The Vault file `vault.yml` can be edited with the `ansible-vault` command.

```
ansible-vault edit --vault-password-file=password.txt vaultfile.yml
```

Unlike in this example `password.txt` should be kept secret. You may have
a look at [git-crypt](https://www.agwa.name/projects/git-crypt/) for a solution.

#### Running the example playbook

The playbook can be executed as.

```
ansible-playbook --inventory=inventory \
                 --vault-password-file=password.txt \
				 --extra-vars=vars.vault \
				 --extra-vars=vars.yaml \
                 test.yml
```
