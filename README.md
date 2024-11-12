# Marzban-Guide
### Update System
```
sudo apt update && sudo apt dist-upgrade
```
### Install XanMod Kernel
Register the PGP key:
```
wget -qO - https://dl.xanmod.org/archive.key \
    | sudo gpg --dearmor -vo /usr/share/keyrings/xanmod-archive-keyring.gpg
```
Add the repository:
```
echo 'deb [signed-by=/usr/share/keyrings/xanmod-archive-keyring.gpg] http://deb.xanmod.org releases main' \
    | sudo tee /etc/apt/sources.list.d/xanmod-release.list
```
Check platform compatibility:
```
wget -qO - https://dl.xanmod.org/check_x86-64_psabi.sh | awk -f -
```
Install the kernel:
```
sudo apt update && sudo apt install linux-xanmod-x64v3
```
Reboot to switch the kernel:
```
sudo reboot now
```
Probe all modules:
```
sudo depmod -a
```
Change the queuing discipline:
```
echo "net.core.default_qdisc = fq" | sudo tee -a /etc/sysctl.conf
```
Enable BBR:
```
echo "net.ipv4.tcp_congestion_control = bbr" | sudo tee -a /etc/sysctl.conf
```
Load the settings:
```
sudo sysctl -p
```
### Install UFW
Install ufw package:
```
sudo apt install ufw
```
Allow SSH:
```
sudo ufw allow ssh comment 'SSH'
```
Allow HTTP:
```
sudo ufw allow http comment 'HTTP'
```
Allow HTTPS:
```
sudo ufw allow https comment 'HTTPS'
```
Enable UFW:
```
sudo ufw enable
```
Allow Cloudflare IPs:
```
sudo nano /path/to/cloudflare-ufw.sh
```
```sh
#!/bin/sh
for cfip in $(curl -sw '\n' https://www.cloudflare.com/ips-v{4,6}); do
    ufw allow from "$cfip" to any port 8443 comment 'Cloudflare'
done
ufw reload > /dev/null
```
Make the script executable:
```
sudo chmod +x /path/to/cloudflare-ufw.sh
```
Run the script:
```
sudo /path/to/cloudflare-ufw.sh
```
Add a cronjob to run the script weekly:
```
sudo crontab -e
```
```
0 0 * * 1 /path/to/cloudflare-ufw.sh > /dev/null 2>&1
```
### Issue SSL Certificates
Install certbot:
```
sudo apt install python3-certbot-dns-cloudflare
```
Get Cloudflare API token to edit zone DNS:
```
https://dash.cloudflare.com/profile/api-tokens
```
Create a Cloudflare credentials file:
```
sudo nano /path/to/cloudflare.ini
```
```
dns_cloudflare_api_token = YOUR_API_TOKEN
```
Restrict access to the file:
```
sudo chmod 600 /path/to/cloudflare.ini
```
Issue a wildcard certificate:
```
sudo certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials /path/to/cloudflare.ini \
  -d *.yourdomain.com \
  -n --agree-tos --no-eff-email -m your@email.com
```
### Install Nginx
Install the prerequisites:
```
sudo apt install curl gnupg2 ca-certificates lsb-release ubuntu-keyring
```
Import an official nginx signing key:
```
curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null
```
Set up repository for nginx packages:
```
echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/mainline/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list
```
Set up repository pinning: 
```
echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" \
    | sudo tee /etc/apt/preferences.d/99nginx
```
Install nginx
```
sudo apt update && sudo apt install nginx
```
### Configure Nginx
Copy nginx configuration file:
```
sudo cp /path/to/Marzban-Guide/nginx.conf /etc/nginx/nginx.conf
```
Adjust nginx configuration:
```
sudo nano /etc/nginx/nginx.conf
```
Test the configuration:
```
sudo nginx -t
```
Enable nginx service:
```
sudo systemctl enable --now nginx
```
### Install Marzban
Run the installation script:
```
sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install
```
### Configure Marzban
Update to the latest core:
```
sudo marzban core-update
```
Copy docker-compose file:
```
sudo cp /path/to/Marzban-Guide/docker-compose.yml /opt/marzban/docker-compose.yml
```
Adjust marzban environment variables:
```
sudo nano /opt/marzban/.env
```
```
UVICORN_HOST = "127.0.0.1"
UVICORN_PORT = 8000
DASHBOARD_PATH = "/SECRET_DASHBOARD_PATH"
XRAY_JSON = "/var/lib/marzban/xray_config.json"
XRAY_SUBSCRIPTION_URL_PREFIX = "https://yourdomain.com"
XRAY_EXECUTABLE_PATH = "/var/lib/marzban/xray-core/xray"
XRAY_ASSETS_PATH = "/var/lib/marzban/xray-core"
MARIADB_DATABASE = "YOUT_DATABASE_NAME"
MARIADB_ROOT_PASSWORD = "YOUR_ROOT_PASSWORD"
SQLALCHEMY_DATABASE_URL = "mysql+pymysql://root:YOUR_ROOT_PASSWORD@127.0.0.1/YOUR_DATABASE_NAME"
```
Restart marzban:
```
sudo marzban restart
```
Add dashboard admin user:
```
sudo marzban cli admin create --sudo
```
### Configure Xray
Copy xray configuration file:
```
sudo cp /path/to/Marzban-Guide/xray_config.json /var/lib/marzban/xray_config.json
```
Generate xray keys:
```
sudo docker exec marzban xray x25519
```
Adjust xray configuration:
```
sudo nano /var/lib/marzban/xray_config.json
```
Restart marzban:
```
sudo marzban restart
```
