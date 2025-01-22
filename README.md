# Marzban-Guide
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
sudo apt update && sudo apt dist-upgrade && sudo apt install linux-xanmod-x64v3
```
Reboot to switch the kernel:
```
sudo reboot now
```
Probe all modules:
```
sudo depmod -a
```
Enable BBR:
```
sudo nano /etc/sysctl.conf
```
```
net.core.default_qdisc = fq
net.ipv4.tcp_congestion_control = bbr
```
Load the settings:
```
sudo sysctl -p
```
### Install UFW
Install ufw:
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
### Configure Cloudflare
Add DNS record:
```
A  @  yourserveripv4  DNS only
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
sudo nano ~/cloudflare.ini
```
```
dns_cloudflare_api_token = YOUR_API_TOKEN
```
Restrict access to the file:
```
sudo chmod 600 ~/cloudflare.ini
```
Issue a wildcard certificate:
```
sudo certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials ~/cloudflare.ini \
  -d yourdomain.com -d *.yourdomain.com \
  -n --agree-tos --no-eff-email -m your@email.com
```
### Install Marzban
Run the installation script:
```
sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install --database mariadb
```
### Clone Repository
Install git:
```
sudo apt install git
```
Clone the repository:
```
git clone https://github.com/strohsnow/Marzban-Guide ~/Marzban-Guide
```
### Configure Marzban
Update to the latest core:
```
sudo marzban core-update
```
Copy docker-compose.yml file:
```
sudo cp ~/Marzban-Guide/docker-compose.yml /opt/marzban/docker-compose.yml
```
Copy nginx.conf file:
```
sudo cp ~/Marzban-Guide/nginx.conf /opt/marzban/nginx.conf
```
Adjust Nginx configuration:
```
sudo nano /opt/marzban/nginx.conf
```
https://github.com/strohsnow/Marzban-Guide/blob/2058b4708a192124a7eba658a07e663aa52c6e06/nginx.conf#L39
https://github.com/strohsnow/Marzban-Guide/blob/2058b4708a192124a7eba658a07e663aa52c6e06/nginx.conf#L41-L42
https://github.com/strohsnow/Marzban-Guide/blob/2058b4708a192124a7eba658a07e663aa52c6e06/nginx.conf#L54
https://github.com/strohsnow/Marzban-Guide/blob/2058b4708a192124a7eba658a07e663aa52c6e06/nginx.conf#L57
Adjust Marzban environment variables:
```
sudo nano /opt/marzban/.env
```
```
UVICORN_HOST = "127.0.0.1"
UVICORN_PORT = 8000
DASHBOARD_PATH = "/SECRET_DASHBOARD_PATH/"
XRAY_JSON = "/var/lib/marzban/xray_config.json"
XRAY_SUBSCRIPTION_URL_PREFIX = "https://yourdomain.com"
XRAY_EXECUTABLE_PATH = "/var/lib/marzban/xray-core/xray"
XRAY_ASSETS_PATH = "/var/lib/marzban/xray-core"
```
### Configure Xray
Copy xray_config.json file:
```
sudo cp ~/Marzban-Guide/xray_config.json /var/lib/marzban/xray_config.json
```
Generate Xray keys:
```
sudo /var/lib/marzban/xray-core/xray x25519
```
Adjust Xray configuration:
```
sudo nano /var/lib/marzban/xray_config.json
```
https://github.com/strohsnow/Marzban-Guide/blob/2058b4708a192124a7eba658a07e663aa52c6e06/xray_config.json#L29
https://github.com/strohsnow/Marzban-Guide/blob/2058b4708a192124a7eba658a07e663aa52c6e06/xray_config.json#L31
### Install WARP
Get WARP+ license (optional):
```
https://t.me/warpplus
```
Install WARP:
```
cd && bash <(curl -fsSL https://gitlab.com/fscarmen/warp/-/raw/main/menu.sh) w
```
### Final
Restart Marzban:
```
sudo marzban restart
```
Add dashboard admin user:
```
sudo marzban cli admin create --sudo
```
Login into the dashboard
```
https://yourdomain.com/SECRET_DASHBOARD_PATH
```
