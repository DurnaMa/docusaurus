# HTTPS for a Web Server on an IP Address (Let's Encrypt)

This guide shows step by step how to get a **free TLS certificate**
for a public IP address (here: `YOUR_IP`, web server on port `8081`)
using Let's Encrypt.

> **Note:** Replace `<YOUR_IP>` in all commands with your own
> public IP address (e.g. `203.0.113.10`).

## Prerequisites

- Public IP address (private ranges like `192.168.x.x` will **not** work)
- Root/sudo access on the server (Linux, e.g. Ubuntu/Debian)
- **Port 80 must be reachable from the internet** (firewall/router rule) –
  Let's Encrypt validates IP certificates only via port 80 or 443
- Important: IP certificates are only valid for **~6 days** → automatic
  renewal is mandatory, and port 80 must stay open permanently

## Step 1: Install Certbot (version ≥ 5.4)

Distribution packages are often too old, so install via Snap:

```bash
sudo apt update
sudo apt install snapd
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Check the version:

```bash
certbot --version
# must be 5.4 or higher
```

## Step 2: Open port 80

```bash
sudo ufw allow 80/tcp
sudo ufw allow 8081/tcp
```

If the server is behind a router: forward port 80 to the server in the router settings.

## Step 3: Request the certificate

If **nothing** is running on port 80 (standalone mode):

```bash
sudo certbot certonly --standalone \
  --preferred-profile shortlived \
  --ip-address <YOUR_IP>
```

If a web server is already running on port 80 (webroot mode):

```bash
sudo certbot certonly --webroot \
  --webroot-path /var/www/html \
  --preferred-profile shortlived \
  --ip-address YOUR_IP
```

Tip: Test with `--staging` first (issues an untrusted test certificate)
to avoid rate limits. Then run the same command without `--staging`.

## Step 4: Certificate file locations

After success, the files are located here:

```
/etc/letsencrypt/live/YOUR_IP/fullchain.pem   (certificate + chain)
/etc/letsencrypt/live/YOUR_IP/privkey.pem     (private key)
```

Certbot does **not** install IP certificates into the web server automatically –
you must add them to the config manually.

## Step 5: Configure the web server (example: Nginx, port 8081)

```nginx
server {
    listen 8081 ssl;
    listen [::]:8081 ssl;
    server_name <YOUR_IP>;

    root /var/www/html;
    index index.html;

    ssl_certificate     /etc/letsencrypt/live/YOUR_IP/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/YOUR_IP/privkey.pem;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

> Warning: `ssl` must appear on **every** `listen` line (including IPv6).
> Keep `root` and `index` at server level, not inside the `location` block –
> otherwise they override each other.

Test the config and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Open in the browser: `https://<YOUR_IP>:8081`

## Step 6: Verify automatic renewal

The Snap installation sets up a timer that renews certificates regularly.
Dry run:

```bash
sudo certbot renew --dry-run
```

To make the web server load the new certificate after each renewal,
create a deploy hook:

```bash
echo 'systemctl reload nginx' | sudo tee /etc/letsencrypt/renewal-hooks/deploy/reload-webserver.sh
sudo chmod +x /etc/letsencrypt/renewal-hooks/deploy/reload-webserver.sh
```

## Common issues

| Problem | Cause / solution |
|---|---|
| Validation fails | Port 80 not reachable from outside (check firewall/router) |
| `unknown profile` error | Certbot too old → install version ≥ 5.4 |
| Certificate expired after 6 days | Renewal failed → run `sudo certbot renew` manually, check logs in `/var/log/letsencrypt/` |
| Browser warning despite certificate | Issued with `--staging` → request again without `--staging` |

## Alternative

For less hassle: point a free domain (e.g. DuckDNS) to the IP
and get a regular 90-day certificate. This avoids the 6-day renewal cycle.