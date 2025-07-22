# Python Sample App with PostgreSQL Provisioning via Ansible and Vagrant

This repository demonstrates provisioning of a Python web application along with a PostgreSQL 12 database using Ansible and Vagrant. It uses community roles and a custom role to deploy and configure the services on a local virtual machine.

## Requirements

Make sure you have the following installed:

- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [Git](https://git-scm.com/downloads)
- Optional: WSL2 (Windows Subsystem for Linux) if you are on Windows.

## Project Structure

The project is based on the [express42 Ansible repository layout](https://github.com/express42/ansible-repertory/tree/master#directory-structure):

```text
.
├── ansible.cfg                   # Ansible configuration file
├── environments
│   └── dev
│       ├── group_vars
│       │   ├── all
│       │   │   └── vault.yml     # Encrypted credentials
│       │   ├── all.yml           # Default variables for all hosts
│       │   └── app.yml           # Variables for the application
│       ├── hosts                 # Inventory file
│       └── host_vars
├── molecule
│   ├── resources
│   └── scenario_name
├── playbooks
├── roles
│   ├── ANXS.postgresql         # Community role for PostgreSQL
│   ├── jdauphant.nginx         # Community role for Nginx
│   ├── nginx                   # Custom role for Nginx configuration
│   │   └── tasks
│   │       └── main.yml
│   ├── postgresql              # Custom role for PostgreSQL configuration
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── vars
│   │       └── main.yml
│   └── python-sample-app       # Custom role for the Python sample app
│       ├── handlers
│       │   └── main.yml
│       ├── tasks
│       │   └── main.yml
│       └── templates
│           └── app.service.j2
├── requirements.txt
├── requirements.yml
├── site.yml                    # Main playbook
├── README.md
├── LICENSE
└── Vagrantfile                 # Vagrant configuration file
```

## Usage

### Clone the Repository

```bash
git clone https://github.com/yourdevastation/exercise-02.git
cd exercise-02
git checkout dev
```

## Ansible Vault

Sensitive variables such as database credentials are stored in an encrypted vault file:

```bash
ansible-vault encrypt environments/local/group_vars/vault.yml
```

To simplify decryption during playbook execution, store the vault password in a local file:

```bash
echo "vaultpass" > .vault_password.txt
```

Make sure to reference this file in ansible.cfg:

```ini
[defaults]
vault_password_file = .vault_password.txt
```

Never commit `.vault_password.txt` to version control — it is excluded via `.gitignore`.

To view vault content:

```bash
ansible-vault view environments/local/group_vars/vault.yml
```

## Running the VM

To create and provision the VM:

```bash
vagrant up
```

This will automatically apply the Ansible playbook and install all components.

To re-run provisioning manually:

```bash
vagrant provision
```

Alternatively, you can apply the playbook directly:

```bash
ansible-playbook site.yml
```

## Accessing Services

### PostgreSQL

PostgreSQL 12 is configured via the ANXS.postgresql role with custom settings generated using [PGTune](https://pgtune.librasoft.by/). Parameters stored in `roles/postgresql/vars/main.yml`. DB is exposed on port 5432:

```bash
psql -h 192.168.56.3 -U your_user -d your_db
```

Replace credentials with values from `environments/local/group_vars/vault.yml` and `environments/local/group_vars/app.yml`. (Default user is `worker`, password is `worker`, database is `app`.)

Superuser access is granted to the `postgres` user and restricted to the local machine. (Check `pg_hba.conf` for details.)

### Python Sample App

The Python web app is deployed via a custom role and listens on port 5000. You can access it at:

```bash
curl http://192.168.56.3:5000/
```

Port 5000 is forwarded to the host machine, allowing you to access the app from your browser:

```bash
curl http://localhost:5000/
```

### Nginx

NGINX proxy was enabled via the jdauphant.nginx role and is configured to forward requests to the Python app. It listens on port 80:

```bash
curl http://192.168.56.3/
```

## After Reboot

Both the PostgreSQL database and Python application are configured to start automatically after the VM is restarted.

```bash
vagrant halt
vagrant up
```

Check app service status:

```bash
systemctl status app.service
```

## License

This project is licensed under the MIT License.
