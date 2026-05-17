# EC2 Setup (Ubuntu) — Quickstart

This guide shows a minimal, repeatable flow to launch an AWS EC2 instance (Ubuntu), prepare it for running the project, and start backend and frontend services.

## 1. AWS console — launch instance

1. Go to EC2 → Launch Instance.
2. Select an AMI: **Ubuntu Server 22.04 LTS** (or Amazon Linux 2).
3. Choose an instance type (e.g., `t3.micro` for testing).
4. Key pair: create or use an existing key pair (download the `.pem` file).
5. Configure security group (see next section).
6. Launch the instance.

## 2. Security group (recommended rules)
- SSH: `TCP 22` from your IP only.
- HTTP: `TCP 80` from 0.0.0.0/0 (if serving via Nginx).
- HTTPS: `TCP 443` from 0.0.0.0/0 (for SSL).
- App ports: `TCP 3000` and `TCP 5000` from trusted IPs only (or keep them private behind Nginx).

Tip: Restrict access where possible; do not open DB ports publically.

## 3. SSH to the instance

```bash
chmod 400 my-key.pem
ssh -i my-key.pem ubuntu@<EC2_PUBLIC_IP>
```

## 4. System preparation

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl build-essential
# Install Node.js 18.x (recommended)
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
node -v && npm -v
```

Optional: install `ufw` and allow SSH:

```bash
sudo apt install -y ufw
sudo ufw allow OpenSSH
sudo ufw enable
```

## 5. Deploy project code

```bash
cd /opt
sudo mkdir myapp && sudo chown $USER:$USER myapp
cd myapp
git clone <repo-url> .
```

### Backend

```bash
cd backend
cp .env.example .env
# Edit .env to set MONGO_URI, PORT, etc.
npm install
# Run with pm2 for production
npm install -g pm2
pm2 start npm --name backend -- run start
pm2 save
```

### Frontend

If frontend is a Vite/React app served with `npm run dev`, prefer building static files and serving via Nginx:

```bash
cd frontend
cp .env.example .env
# Update VITE_API_URL to point to backend (http://localhost:5000)
npm install
npm run build
# Now configure Nginx to serve files from frontend/dist
```

## 6. (Optional) Nginx reverse proxy

Install Nginx and configure a site to proxy `/api` to the backend and serve frontend files:

```bash
sudo apt install -y nginx
sudo systemctl enable --now nginx
# Example: copy build into /var/www/myapp and create /etc/nginx/sites-available/myapp
```

## 7. SSL (Let's Encrypt)

If you have a domain, use Certbot to obtain certificates and enable HTTPS:

```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
```

## 8. Maintenance & tips
- Use an IAM role if the instance needs AWS permissions.
- Use `pm2` or systemd services to keep processes running after reboot.
- Keep sensitive secrets out of the repo; use AWS Secrets Manager or SSM Parameter Store for production.

---
If you want, I can:
- Commit this file to the repo.
- Add an example `nginx` site config.
- Add a `systemd` service file for the backend.

Tell me which of these you'd like next.
