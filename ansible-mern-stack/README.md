# ansible-mern-stack

Ansible playbooks and roles to provision a complete **MERN stack lab environment** (MongoDB, Express, React, Node.js).  
This repository is structured for clarity, separating configuration, inventory, and modular roles.

---

## 📂 Project Structure

```
ansible-mern-stack/
├── ansible.cfg                  # Ansible configuration file
├── group_vars/                  # Variables applied to groups of hosts
│   ├── all.yaml                 # Global variables
│   ├── dbservers.yaml           # Variables specific to dbservers
│   ├── webservers.yaml          # Variables specific to webservers
│   └── README_group_vars.md     # Notes on group variables usage
├── images/
│   └── web-result.png           # Screenshot of React app result
├── inventory/
│   └── hosts                    # Ansible inventory file
├── main-playbook.yml            # Main playbook entry point
├── README.md                    # Project documentation (this file)
└── roles/
    ├── common/                  # Common role (packages, users, ssh keys)
    ├── dbserver/                # MongoDB role
    └── webserver/               # Node.js / React role
```

---

## 🚀 Roles

### `common`
- Updates system packages.
- Installs base packages.
- Creates users and groups.
- Deploys SSH keys.

### `dbserver`
- Installs MongoDB from the official repository.
- Configures `mongod.conf` (bind IP, port, security).
- Ensures service is running and enabled.
- Prepares users and databases if required.

### `webserver`
- Installs Node.js and npm.
- Installs global npm packages (`express`, `create-react-app`, `pm2`).
- Initializes a React project in `{{ app_dir }}/{{ app_name }}`.
- Configures React dev server to bind `0.0.0.0`.
- Optionally deploys custom `App.js` and `App.css`.
- Manages the React app process with PM2.

---

## 🖥️ Inventory Example

`inventory/hosts`
```ini
[webservers]
webserver01 ansible_host=192.168.2.9

[dbservers]
dbserver01 ansible_host=192.168.2.8

[all:vars]
ansible_connection=ssh
ansible_user=ansibleadmin
ansible_private_key_file=~/.ssh/id_ed25519
```

---

## ⚙️ Variables

`group_vars/webservers.yaml`
```yaml
app_dir: "/var/www/mern-app"
app_name: "my_app"
deploy_custom_page: true
react_app:
  title: "Custom React app"
  message: "This is a sample React app managed by Ansible!"
```

`group_vars/dbservers.yaml`
```yaml
mongodb_version: "8.0"
mongodb_port: 27017
mongodb_bind_ip: "0.0.0.0"
mongodb_config_path: "/etc/mongod.conf"

mongodb_admin_user: "admin"
mongodb_admin_password: "ChangeMeStrong"
mongodb_app_db: "mern_app"
mongodb_app_user: "mern_user"
mongodb_app_password: "ChangeMeToo"
```

---

## ▶️ Running the Playbook

Run against all hosts:
```bash
ansible-playbook -i inventory/hosts main-playbook.yml
```

Run only webserver role:
```bash
ansible-playbook -i inventory/hosts main-playbook.yml --tags webserver
```

Run only dbserver role:
```bash
ansible-playbook -i inventory/hosts main-playbook.yml --tags dbserver
```

---

## 🌐 Access

- **Web App (React):**  
  Open `http://<webserver_ip>:3000/`

- **Database (MongoDB):**  
  Connect via `mongosh`:  
  ```bash
  mongosh "mongodb://mern_user:ChangeMeToo@<dbserver_ip>:27017/mern_app?authSource=mern_app"
  ```

---

## 📸 Result

<p align="center">
  <img src="images/web-result.png" alt="Web result" width="800">
</p>

---

## 📝 Notes

- Roles are idempotent; re-running should not produce unnecessary changes.
- `deploy_custom_page: true` triggers templating of React components.
- MongoDB repository setup requires a correct codename (e.g., `jammy` for Ubuntu 22.04).

---