# SSH Key and Git Configuration Guide

## Linux Setup

### Step 1: Generate SSH Keys

Create SSH keys for personal and work GitHub accounts.

```bash
ssh-keygen -t rsa -b 4096 -C "your_personal_email@example.com" -f ~/.ssh/id_rsa_personal
ssh-keygen -t rsa -b 4096 -C "your_work_email@example.com" -f ~/.ssh/id_rsa_work
```

### Step 2: Configure SSH

Edit the SSH config file to define different hosts for each account.

```bash
nano ~/.ssh/config
```

### Step 3: Add Configurations

Add the following configuration to the `config` file:

```plaintext
# Personal GitHub Account
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_personal

# Work GitHub Account
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work
```

### Step 4: Set Permissions

Set the correct permissions for SSH keys and the config file.

```bash
chmod 600 ~/.ssh/id_rsa_personal
chmod 600 ~/.ssh/id_rsa_work
chmod 600 ~/.ssh/id_ed25519
chmod 600 ~/.ssh/config
```

### Step 5: Copy SSH Key for GitHub

To add your SSH key to GitHub, display the public key and copy it.

```bash
cat ~/.ssh/id_rsa_personal.pub
```

### Step 6: Set URL for Git Remote

Set the Git remote URL for your repository.

```bash
git remote set-url origin github-personal:Kalay-Shop/server.git
```

### Step 7: Clone Repositories

Clone repositories for both personal and work accounts.

```bash
git clone github-work:Work-Repo/server.git
git clone github-personal:Personal-Repo/server.git
```

---

## Windows Setup

### Step 1: Generate SSH Key

Generate the SSH key for the personal GitHub account.

```bash
ssh-keygen -t rsa -b 4096 -C "devidol.mm@gmail.com" -f C:\Users\<USERNAME>\.ssh\id_rsa_personal
```

### Step 2: Open SSH Directory

Navigate to the `.ssh` directory.

```bash
cd C:\Users\<USERNAME>\.ssh
```

### Step 3: Edit SSH Config

Open the config file in a text editor.

```plaintext
notepad config
```

### Step 4: Add SSH Configurations

Add the following configuration to the `config` file:

```plaintext
# Personal GitHub Account
Host github-personal
    HostName github.com
    User git
    IdentityFile C:\Users\<USERNAME>\.ssh\id_rsa_personal

# Work GitHub Account
Host github-work
    HostName github.com
    User git
    IdentityFile C:\Users\<USERNAME>\.ssh\id_rsa_work

# AWS Account
Host hashingthang-aws
    HostName 52.74.44.124
    User ubuntu
    IdentityFile ~/.ssh/hashingthang-aws.pem
```

### Step 5: Rename Config File

If the config file was created as `config.txt`, rename it to `config`.

```plaintext
ren config.txt config
```

### Step 6: Copy SSH Key

To add your SSH key to GitHub, display the public key and copy it.

```bash
type id_rsa_work.pub
```

---

## AWS Account Setup

1. Move the AWS key file to the SSH directory.

    ```bash
    move C:\app\hashingthang-aws.pem %USERPROFILE%\.ssh\
    ```

2. Set permissions for the AWS key file.

    ```bash
    chmod 400 ~/.ssh/hashingthang-aws.pem
    ```

---

## Example GitHub SSH Setup

For accessing GitHub with SSH, use the following format:

```plaintext
git@github.com:DevIdol/flutter_mvvm.git
```

Set the remote URL for the repository.

```bash
git remote set-url origin github-personal:DevIdol/flutter_mvvm.git
```

---

## Moving Files in Ubuntu

Use the following command to move a MySQL database file to your directory on an Ubuntu server.

```bash
sudo mysql -u root -p pmm_db < /var/www/pmm/pmm_db.sql
```

---

