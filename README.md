# Ansible Role: docker

[![CI](https://github.com/tjg-homelab/ansible-role-docker/actions/workflows/ci.yml/badge.svg)](https://github.com/tjg-homelab/ansible-role-docker/actions/workflows/ci.yml)

Installs Docker Engine from Docker's official apt repository on Debian-family
systems (Debian and Ubuntu).

**Scope:** this role installs and enables the Docker Engine only. Deploying
containers (Portainer, application workloads, etc.) is the job of a separate
container-deployment role — keeping "install the engine" and "run containers"
as distinct concerns.

## What this role does

- Removes conflicting distro-packaged Docker packages
- Adds Docker's official apt GPG key and repository (distribution-aware:
  resolves to the Ubuntu or Debian repo path automatically)
- Installs Docker Engine, CLI, containerd, buildx, and the compose plugin
- Optionally enables and starts the Docker service

## Requirements

- Debian 12/13 or Ubuntu 22.04/24.04

## Role Variables

| Variable | Default | Description |
|---|---|---|
| `docker_apt_repo_url` | `https://download.docker.com/linux/<distro>` | Docker apt repo base (auto-derived from the distribution) |
| `docker_apt_repo_component` | `stable` | Repo component (`stable`, `test`, `nightly`) |
| `docker_packages` | docker-ce, cli, containerd.io, buildx, compose | Engine packages to install |
| `docker_conflicting_packages` | distro docker/podman packages | Packages removed before install |
| `docker_manage_service` | `true` | Enable/start the Docker service |
| `docker_service_enabled` | `true` | Enable at boot |
| `docker_service_state` | `started` | Runtime state |

## Example Playbook

```yaml
- hosts: docker_hosts
  roles:
    - role: docker
```

Installing via `requirements.yml`:

```yaml
roles:
  - name: docker
    src: https://github.com/tjg-homelab/ansible-role-docker.git
    version: v1.0.0
```

## Testing

Molecule (Docker driver) installs the engine (service disabled — the test host
is itself a container), checks idempotence, and verifies the apt keyring, repo
file, and CLI availability against Debian 12, Debian 13, and Ubuntu 24.04.

```bash
pip install ansible-core molecule molecule-plugins[docker] docker
ansible-galaxy collection install community.docker ansible.posix
molecule test
```

## License

MIT

## Author

Rodney Nissen ([The Jira Guy](https://thejiraguy.com))
