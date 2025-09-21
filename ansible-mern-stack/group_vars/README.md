# Using group_vars in Ansible

The `group_vars` directory is a special Ansible feature.  
Files inside this folder must match the **group names** defined in your `inventory/hosts`.  
This way, variables are automatically applied to all hosts that belong to that group.

---

## Example

### Inventory (`inventory/hosts`)
```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[dbservers]
db1 ansible_host=192.168.1.20


# Group Variables (group_vars/)
group_vars/
├── all.yaml          # Applies to all hosts
├── webservers.yaml   # Applies only to hosts in [webservers]
└── dbservers.yaml    # Applies only to hosts in [dbservers]

Behavior
	•	Variables in all.yaml → apply to all hosts.
	•	Variables in webservers.yaml → apply only to hosts in [webservers].
	•	Variables in dbservers.yaml → apply only to hosts in [dbservers].