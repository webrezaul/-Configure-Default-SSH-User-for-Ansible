# 🚀 Configure Default SSH User for Ansible

Standardizing server management across the stack by configuring Ansible's **default** SSH user to automate connections.

---

## 📦 Stack / Tech Used

| Technology | Version | Purpose |
|------------|---------|---------|
| Ansible    | `v2.9+` | Configuration management and server automation |
| INI / Config| `N/A`   | Ansible configuration file format (`ansible.cfg`) |
| YAML       | `1.2`   | Playbook serialization format |

---

## 📁 Project Structure

```
.
├── files/
│   └── ansible.cfg      # Resulting default configuration file
├── inventory            # Standard Ansible inventory file
├── playbook.yml         # Playbook to automate the configuration
├── .gitignore           # Git ignore rules
└── README.md            # Project documentation
```

---

## ✅ Prerequisites

Before configuring, ensure you have:
- Ansible installed on the control node / jump host (typically via `yum`).
- A common sudo user (e.g., `javed`) set up on managed hosts.

---

## 🔧 Configuration & Solution

On a fresh `yum`-installed Ansible setup, the default configuration file (`/etc/ansible/ansible.cfg`) might only contain the auto-generated comment header (no active `[defaults]` section).

### Option 1: Automation via Ansible Playbook (Recommended)
You can automate the configuration of the default SSH user using the included playbook. Run the following command:

```bash
ansible-playbook -i inventory playbook.yml
```

This uses the standard `ansible.builtin.ini_file` module to safely and idempotently configure `/etc/ansible/ansible.cfg` without raw shell utilities.

### Option 2: Manual command execution
Alternatively, you can edit `/etc/ansible/ansible.cfg` directly depending on its current state:

- **If no active `[defaults]` section exists**, append it with `remote_user` set:
  ```bash
  sudo sed -i '$a [defaults]\nremote_user = javed' /etc/ansible/ansible.cfg
  ```

- **If `[defaults]` already exists** with a commented-out `#remote_user = root` line, uncomment and modify it:
  ```bash
  sudo sed -i 's/^[[:space:]]*#[[:space:]]*remote_user[[:space:]]*=.*/remote_user = javed/' /etc/ansible/ansible.cfg
  ```

The final configured file matches the template at [files/ansible.cfg](file:///e:/Project/DevOPS/Ansible/Configure%20Default%20SSH%20User%20for%20Ansible/files/ansible.cfg).

---

## 📋 Verification & Validation

To verify the default SSH user configuration:

### 1. Inspect the configuration file:
```bash
cat /etc/ansible/ansible.cfg
```
**Expected Output:**
```ini
[defaults]
remote_user = javed
```

### 2. Confirm Ansible is actively reading this configuration:
```bash
ansible-config dump --only-changed
```
**Expected Output:**
```text
DEFAULT_REMOTE_USER(/etc/ansible/ansible.cfg) = javed
```

### 3. Rule out config overrides (precedence checks):
```bash
# Check if a custom config path is set in env
echo $ANSIBLE_CONFIG  # Should be empty

# Check active configuration file source
ansible-config dump | grep CONFIG_FILE  # Should point to /etc/ansible/ansible.cfg
```

---

## 📝 Notes & Next Steps

- This only sets the *default* user — individual playbooks or host inventories can override it using `ansible_user` or `remote_user`.
- For the full rollout: distribute SSH public keys for `javed` to managed hosts, populate the Ansible inventory, and configure `become`/sudo privileges.

---

## ✍️ Author

- **GitHub**: [webrezaul](https://github.com/webrezaul)
- **Website**: [mdrezaulkarim.com](https://mdrezaulkarim.com)
