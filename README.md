# Ansible homelab

Ansible playbooks for the homelab. Jobs run through Semaphore, which supplies
the inventory selected in its UI. The inventory and connection credentials are
intentionally not stored in this repository.

## Repository layout

- `playbooks/` - playbooks to run from Semaphore
- `ansible.cfg` - repository-level Ansible defaults

As the repository grows, add reusable roles under `roles/` and non-secret
inventory variables under `group_vars/` or `host_vars/`.

## Configure Semaphore

1. Add this Git repository as a Semaphore repository and create a project.
2. Add/select the inventory stored in the Semaphore container for that project.
   Semaphore should pass that inventory to Ansible when a task template runs.
3. Configure SSH keys, become credentials, and any vault password as Semaphore
   credentials; do not commit secrets to this repository.
4. Create a task template that runs `playbooks/ping-linux.yml` to check access
   to the Linux hosts.

Use these inventory groups when organizing hosts in Semaphore:

- `linux_hosts` - the four Linux computers
- `docker_hosts` - the Linux Docker host
- `openwrt` - the OpenWrt router

Add each host to the groups that match its intended use. For example, if the
Docker host should receive general Linux playbooks, include it in `linux_hosts`
as well as `docker_hosts`.

The initial connectivity playbook targets `linux_hosts`. It uses Ansible's
`ping` module, which requires Python on the managed host; it deliberately does
not target OpenWrt because OpenWrt installations commonly lack Python. Add a
router-specific playbook and any required Ansible collections when router
configuration is introduced.

## Run locally (optional)

Install Ansible in your chosen environment, then run the Linux connectivity
check with an inventory file outside this repository:

```sh
ansible-playbook -i /path/to/hosts.yml playbooks/ping-linux.yml
```

The local inventory must define the `linux_hosts` group. Do not add the
Semaphore-managed inventory or credentials to Git.
