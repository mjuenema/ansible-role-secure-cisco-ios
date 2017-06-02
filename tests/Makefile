#
# Makefile for testing the ansible-role-secure-cisco-ios role.
#

AP = ansible-playbook --inventory=inventory \
                      --vault-password-file=password.txt \
	       			  --extra-vars=vars.vault \
		     		  --extra-vars=vars.yaml
PLAYBOOK = test.yml

HOSTS = $(shell grep ansible_host inventory | cut -d= -f 2)
					 

all: lint syntax_check list_hosts list_tasks ping run verify

lint:
	echo TODO

syntax_check:
	$(AP) --syntax-check $(PLAYBOOK)

list_hosts:
	$(AP) --list-hosts $(PLAYBOOK)

list_tasks:
	$(AP) --list-tasks $(PLAYBOOK)

ping:
	fping $(HOSTS)
