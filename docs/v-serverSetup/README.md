# V-Server Setup Documentation

This document describes the setup of a cloud V-Server (Ubuntu), including SSH key authentication, disabling password logins, installing and configuring the Nginx web server, and configuring Git/GitHub on the server.

import GithubLinkAdmonition from '@site/src/components/GithubLinkAdmonition';

<GithubLinkAdmonition
link="https://github.com/DurnaMa/docusaurus"
title="Github Tip"
type="tip"
/>

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [SSH Key Setup](#2-ssh-key-setup)
3. [Disable Password Login](#3-disable-password-login)
4. [Install the Nginx Web Server](#4-install-the-nginx-web-server)
5. [Alternative Nginx Configuration](#5-alternative-nginx-configuration)
6. [Git and GitHub Configuration on the Server](#6-git-and-github-configuration-on-the-server)
7. [Verification / Testing](#7-verification--testing)

---

## 1. Prerequisites

- A cloud V-Server running Ubuntu
- A local machine with SSH installed
- A GitHub account

<!-- TODO: adjust if needed (e.g. Ubuntu version, provider) -->

---

## 2. SSH Key Setup

The SSH key pair is generated on the **local machine**, not on the server.

1. Generate a new key pair on your local computer:

   ```bash
   ssh-keygen -t ed25519 -C "your-email@example.com"
   ```

2. Copy the public key to the server using `ssh-copy-id`:

   ```bash
   ssh-copy-id -i ~/.ssh/id_ed25519.pub user@your-server-ip
   ```

3. Test the login with the SSH key **before** disabling password authentication:

   ```bash
   ssh -i ~/.ssh/id_ed25519 user@your-server-ip
   ```

<!-- TODO: replace user/IP placeholders in your head while testing — do NOT put your real private key or password anywhere in this repo -->

---

## 3. Disable Password Login

Passwords can be a source of security risks, which is why password-based logins should be disabled in favor of authentication via SSH keys.

1. Open the SSH daemon configuration:

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Find the line `#PasswordAuthentication yes` and change it to:

   ```
   PasswordAuthentication no
   ```

3. Save the file and exit.
4. Restart the SSH service to apply the changes:

   ```bash
   sudo systemctl restart ssh.service
   ```

5. Verify that password login is no longer possible:

   ```bash
   ssh -o PubkeyAuthentication=no user@your-server-ip
   ```

   This login attempt should be rejected.

---

## 4. Install the Nginx Web Server

Install Nginx on the Ubuntu cloud VM:

```bash
sudo apt update
sudo apt install nginx -y
```

Check the service status:

```bash
systemctl status nginx.service
```

After installation, the default Nginx welcome page should be visible when opening the server's IP address in a browser.

---

## 5. Alternative Nginx Configuration

The goal is to serve an alternative HTML page instead of the default Nginx start page.

1. Create the directory and the HTML file:

   ```bash
   sudo mkdir -p /var/www/alternatives/
   sudo nano /var/www/alternatives/alternate-index.html
   ```

   ```html
   <!doctype html>
   <html>
   <head>
     <meta charset="utf-8">
     <title>Hello, Nginx!</title>
   </head>
   <body>
     <h1>Hello, Nginx!</h1>
     <p>I have just configured our Nginx web server on Ubuntu Server!</p>
   </body>
   </html>
   ```

2. Add a site configuration under `/etc/nginx/sites-enabled/`:

   ```bash
   sudo nano /etc/nginx/sites-enabled/alternatives
   ```

   ```nginx
   server {
       listen 8081;
       listen [::]:8081;

       root /var/www/alternatives;
       index alternate-index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

3. Test and reload Nginx:

   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```

4. Open `http://your-server-ip:8081` in your browser to see the alternative start page.

---

## 6. Git and GitHub Configuration on the Server

Configure Git on the server with the same user data as on your local machine / GitHub account:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

Verify the configuration:

```bash
git config --global --list
```

To interact with GitHub repositories from the server, generate a **separate** SSH key pair on the server:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Copy the public key and add it to GitHub under **Settings → SSH and GPG keys → New SSH key**:

```bash
cat ~/.ssh/id_ed25519.pub
```

Test the connection to GitHub:

```bash
ssh -T git@github.com
```

<!-- TODO: run these steps on your server if not done yet — this checklist item is still open -->

---

## 7. Verification / Testing

- [x] Login with SSH key works
- [x] Login with username/password is rejected
- [x] `sudo nginx -t` reports a valid configuration
- [x] The alternative HTML page is reachable in the browser
- [ ] `ssh -T git@github.com` confirms GitHub authentication

<!-- TODO: check the last box after completing section 6 -->