
---

### 📘 `README.md` — Ansible Control Node (GCP) to Managed Node (AWS)

````markdown
# Ansible: GCP Control Node to AWS Managed Node (Ping Test)

This guide describes how to configure Ansible on a **GCP VM (control node)** to connect and run tasks on an **AWS EC2 instance (managed node)** using SSH and an inventory file.

---

## ✅ Prerequisites

- GCP VM (Ubuntu) with public or private internet access.
- AWS EC2 instance (Ubuntu) with:
  - A public IP address.
  - SSH access allowed from the GCP IP (security group rule).
- SSH private key file (`ansible.pem`) downloaded from AWS for EC2 access.
- User `ubuntu` must match the EC2 AMI default (e.g., Ubuntu AMI = `ubuntu`).

---

## 🔧 Setup Steps

### 1. Install Ansible on GCP VM (Control Node)

```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
sudo apt install python3 python3-pip -y
````

### 2. Set Up SSH Key Permissions

Place the AWS EC2 private key in a secure path, e.g., `/etc/ansible/ansible.pem`, and set permissions:

```bash
sudo chmod 600 /etc/ansible/ansible.pem
```

---

### 3. Create Inventory File (`inventory.yaml`)

```yaml
virtualmachines:
  hosts:
    vm01:
      ansible_host: <AWS_EC2_PUBLIC_IP>
      ansible_user: ubuntu
      ansible_ssh_private_key_file: /etc/ansible/ansible.pem
      ansible_connection: ssh
```

Replace `<AWS_EC2_PUBLIC_IP>` with your actual EC2 instance IP address.

---

### 4. Run Inventory Check

```bash
ansible-inventory -i inventory.yaml --list
```

---

### 5. Test SSH Connection with `ping` Module

```bash
ansible -i inventory.yaml virtualmachines -m ping
```

Expected output:

```json
vm01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

### 6. Run a Sample Playbook

Create a file `playbook.yaml`:

```yaml
- name: My first play
  hosts: virtualmachines
  tasks:
    - name: Ping my hosts
      ansible.builtin.ping:

    - name: Print message
      ansible.builtin.debug:
        msg: Hello World
```

Run it:

```bash
ansible-playbook -i inventory.yaml playbook.yaml
```

---

## 🧪 Sample Command History (Reference)

```bash
sudo apt update
sudo apt install ansible
chmod 600 /etc/ansible/ansible.pem
ansible-inventory -i inventory.yaml --list
ansible -i inventory.yaml virtualmachines -m ping
ansible-playbook -i inventory.yaml playbook.yaml
```

---

## ✅ Tips

* Ensure port **22 (SSH)** is open on the EC2 security group.
* Use the correct private key associated with your EC2 instance.
* Avoid tabs in YAML; use **2 spaces** for indentation.

---

## 📂 File Structure

```
/etc/ansible/ansible.pem       # SSH private key
/root/inventory.yaml           # Ansible inventory
/root/playbook.yaml            # Ansible playbook
```

---

## 🛡️ Security

* Keep `ansible.pem` permissions strict (`chmod 600`).
* Never commit private keys to source control.

```

Let me know if you want this exported as a `.md` file or adjusted for multiple hosts or Windows targets.
```
