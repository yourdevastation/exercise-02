# Dockerized Deployment with GitLab CI and Ansible

This repository automates deployment of containerized frontend and backend applications using Ansible, Docker Compose, and GitLab CI. The apps are proxied through NGINX and securely configured with secrets and runtime environment via Ansible playbooks.

## Requirements

Make sure you have the following installed:

- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [Git](https://git-scm.com/downloads)
- Optional: WSL2 (Windows Subsystem for Linux) if you are on Windows.
- Optional: [Docker](https://docs.docker.com/get-docker/), [Docker Compose](https://docs.docker.com/compose/install/) if you want to run the apps in containers locally.

## Project Structure

The project is based on the [express42 Ansible repository layout](https://github.com/express42/ansible-repertory/tree/master#directory-structure):

```text
.
├── ansible.cfg                   # Ansible configuration
├── environments
│   └── dev
│       ├── group_vars            # Environment-specific variables
│       └── host_vars             # Per-host variables
├── roles
│   ├── common                    # Role for common tasks
│   │   └── tasks
│   │       └── main.yml
│   └── deploy                    # Core deployment logic
│       ├── handlers
│       ├── tasks
│       │   ├── main.yml          # Main entry point for the role
│       │   ├── run.yml           # Tasks to run the application
│       │   └── setup.yml         # Environment setup tasks
│       └── templates
│           ├── docker-compose.yml.j2
│           └── nginx.conf.j2
├── molecule
│   ├── resources
│   └── scenario_name
├── playbooks
├── site.yml                      # Entry point for provisioning
├── requirements.yml
├── requirements.txt
├── Vagrantfile                   # Local VM environment
└── README.md
```

## Usage

### GitLab CI/CD

This project is designed to be used with GitLab CI/CD pipelines. The CI/CD configuration files are located in the `frontend` and `backend` repositories. The pipelines automate the build and deployment of the applications.

### Local Development

```bash
git clone https://github.com/yourdevastation/exercise-02.git
cd exercise-02
git checkout dev
```

You should provide all variables when running the playbook.

## Running with Vagrant

For local development, you can use Vagrant to set up a virtual machine with all dependencies installed. This is useful for testing the Ansible playbooks and running the applications locally.

```bash
vagrant up
```

Write ip address of the VM to environments/local/hosts file:

```text
[local]
<your_vm_ip> ansible_ssh_private_key_file=~/.ssh/id_rsa ansible_user=vagrant
```

You can run the Ansible playbook to provision the VM:

```bash
ansible-playbook -i environments/local/hosts -e ...
```

## Deployment Logic

The deploy role performs the following:

1. Installs Docker and Docker Compose
2. Creates system user (deploy_user) and adds to Docker group
3. Logs in to GitLab container registry using CI credentials
4. Renders and places docker-compose.yml and nginx.conf templates
5. Stop and remove existing containers
6. Pulls the latest Docker images for the frontend and backend applications
7. Starts the Docker Compose stack
8. Cleans up secrets on the host machine

## Accessing the App

After successful deployment, services are available through NGINX on port 80 of the target host. You can test connectivity with:

```bash
curl http://<your_vm_ip>:<nginx_listen_port>/
```

Container ports and service names are defined dynamically based on environment and Git commit SHA.
Secrets are passed securely to containers via mounted files and removed after deployment completes.

## License

This project is licensed under the MIT License.
