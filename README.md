
---

# Hysteria Proxy Deployment with Ansible & Docker

This project automates the setup of a **Hysteria v2 Proxy** using **Ansible**, **Docker**, and **Cloudflare DNS**. It supports both proxy server setup and secure client configuration, with flexible parameters defined in group variables.

🔗 **GitHub Repo:** [https://github.com/mafshar85/iqan](https://github.com/mafshar85/iqan)

---

## 📁 Project Structure

* `playbooks/docker_setup.yml` – Installs Docker and prerequisites.
* `playbooks/hysteria_proxy.yml` – Deploys the Hysteria proxy server using Docker.
* `playbooks/hysteria_client.yml` – Generates a local client config at `~/hysteria`.

---

## 🔧 Configuration

Before running the playbooks, edit the following files to match your environment:

### 1. `inventory/group_vars/all/general.yml`

Set your domain and optional parameters:

```yaml
# Main domain
main_domain: example.com

# Project directory
project_dir: /opt/services

# Hysteria Docker image version
image_tag: v2.6.2

# Proxy domain (subdomain for Hysteria)
proxy_domain: "cookie.{{ main_domain }}"

# Port Hysteria listens on
hysteria_port: 2211

# Enable iptables redirect from 443 to hysteria_port
redirect_443_to_port: true
```

---

### 2. `inventory/group_vars/all/vault.yml`

Store secrets here (you can encrypt with `ansible-vault`):

```yaml
hysteria_password_vault: "your_secure_password"
```

---

### 3. `roles/hysteria_proxy/defaults/main/vault.yml`

Set your Cloudflare API credentials:

```yaml
# These reference values in your vault
API_TOKEN_VAULT: REPLACE_WITH_YOUR_CLOUDFLARE_API_TOKEN"
ZONE_ID_VAULT: REPLACE_WITH_YOUR_CLOUDFLARE_ZONE_ID"
```

---

## 🚀 Usage

### Step 1: Install Docker (if not already installed)

```bash
ansible-playbook -i inventory/hosts.yml playbooks/docker_setup.yml
```

---

### Step 2: Deploy the Hysteria Proxy Server

```bash
ansible-playbook -i inventory/hosts.yml playbooks/hysteria_proxy.yml
```

---

### Step 3: Generate Client Configuration (locally)

```bash
ansible-playbook -i inventory/hosts.yml playbooks/hysteria_client.yml --ask-become-pass
```

> 💡 **Note:** You will be prompted for your **local machine's password** (not the remote server). 

The config will be created in `~/hysteria`.
See usage instructions in `Hint.txt`.

---

## 🌐 Connecting to the Proxy

Once deployed, the Hysteria client can be started with:

```bash
hysteria client -c ~/hysteria/client.yaml
```

To set up proxying in your terminal:

```bash
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080
```


---

## 📌 Notes

* Make sure your domain’s DNS is managed via Cloudflare.
* Ports are configurable and can be restricted to localhost using `redirect_443_to_port`.
* All passwords and tokens should be stored securely using Ansible Vault.

---

