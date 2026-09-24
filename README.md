# Ansible ShopFlow deployment lab

Installs Docker from its APT repository and deploys the ShopFlow application on a fresh Ubuntu 22.04/24.04 host. Requires Ansible, SSH/sudo access and the `community.docker` collection. Monitoring services are optional, not started by default.

## Required ShopFlow revision

Use the ShopFlow portfolio-fix revision that implements relative frontend `/api` URLs, `HTTP_BIND_ADDRESS`, nonduplicating seed and non-root backend. Merge that PR first or set `shopflow_version` to its branch/commit. Older ShopFlow main revisions do not meet this deployment contract. Pin a commit for repeatable deployments.

## Configure and run

```bash
ansible-galaxy collection install -r requirements.yml
cp inventory.ini.example inventory.ini
cp secrets.yml.example secrets.yml
# Edit inventory and secrets. Generate each secret with: openssl rand -hex 32
ansible-vault encrypt secrets.yml
ansible-playbook -i inventory.ini deploy.yml --ask-vault-pass
```

The environment file is mode 0600 and secret-bearing tasks suppress output. Secrets and inventory are ignored by Git. Existing database passwords cannot be changed merely by editing `.env`; rotate them in the database and then update deployment configuration.

The role defaults to loopback HTTP. Access remotely through an SSH tunnel, or explicitly set `shopflow_http_bind=0.0.0.0` after configuring firewall/TLS. The frontend uses relative URLs, so no server IP is patched into source files.

Docker Compose waits for service health and the playbook polls HTTP readiness. Initialization/seed failures stop deployment instead of being ignored. Seeding skips existing demo products on serial reruns; schema creation is not a migration framework. Concurrent deployments are not supported.

Optional variables: `shopflow_user`, `shopflow_dir`, `shopflow_repo`, `shopflow_version`, `shopflow_http_bind`, and `shopflow_services`. To include monitoring, add `prometheus`, `grafana`, `loki`, and `alloy` to the service list. Docker volume data survives reruns; removal of volumes is a separate destructive action.
