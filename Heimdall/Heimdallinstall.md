
sudo apt update
sudo apt install php8.1 php8.1-sqlite3 php8.1-zip git
sudo mkdir /var/www/heimdall
sudo git clone https://github.com/linuxserver/Heimdall.git /var/www/heimdall
cd /var/www/heimdall
sudo php artisan key:generate
sudo chown -R www-data:www-data /var/www/heimdall
sudo chmod -R 755 /var/www/heimdall/

sudo a2enmod rewrite
sudo a2ensite heimdall.conf
sudo systemctl restart apache2.service && sudo systemctl status apache2.service 
