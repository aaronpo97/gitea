# Gitea config

# nginx
First follow the http only nginx conf

then run

# Install certbot
sudo dnf install certbot python3-certbot-nginx

# Create webroot directory
sudo mkdir -p /var/www/certbot

# Get certificate
sudo certbot certonly --webroot \
    -w /var/www/certbot \
    -d git.example.com \
    --email example@gmail.com \
    --agree-tos \
    --no-eff-email

then change the config over to the https nginx config


