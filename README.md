# Ansible Docker Containerization

Ansible playbook that installs Docker on a remote Ubuntu server, builds a Dockerfile with a web and database server, and runs a containerized Apache.

## What this covers
- Installing Docker (`docker.io`) and enabling the service with Ansible
- Adding a user to the `docker` group
- Writing a Dockerfile (Ubuntu base, Apache2, MariaDB)
- Copying the Dockerfile to the remote host and building it
- Running a container with a published port (`8080:80`)

## Lab environment
- Control node: Ubuntu workstation running Ansible
- Managed node: Ubuntu server (VirtualBox, host-only network)

## Repository contents
| File | Purpose |
|------|---------|
| `ansible.cfg` | Ansible configuration |
| `inventory` | Managed host |
| `dockerfile.yml` | Playbook: install Docker, build image, run container |
| `Dockerfile` | Ubuntu image with Apache2 and MariaDB |

## Usage
```bash
ansible-playbook --ask-become-pass dockerfile.yml
```

## Verification
- `docker --version` reports 20.10.21 (Ubuntu `docker.io` package).
- `systemctl status docker` shows the service active and socket-activated.
- Browsing to `http://<host>:8080` shows the Apache2 default page served from the container.

## Notes and next steps
- The playbook builds the Dockerfile without a tag, then pulls and runs `ubuntu/apache2` from Docker Hub. The page on port 8080 therefore comes from the pulled image, not the one built here. Tagging the build (`docker build -t <name> .`) and running that image would close the loop.
- The Dockerfile installs MariaDB, but the entrypoint starts only Apache, so the database never runs. Splitting web and database into separate containers (for example with Docker Compose) is the better design.
- The `community.docker` modules (`docker_image`, `docker_container`) and the `user` module would replace the `shell`/`command` tasks and make the playbook idempotent.
