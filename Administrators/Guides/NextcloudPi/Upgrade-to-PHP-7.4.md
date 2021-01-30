## Before you start
- This tutorial was developed for and tested with NextCloudPi 1.34.7 on a RaspberryPI.
- NextCloudPi may behave differently on other systems.
- _Make sure to make a backup of your entire NextCloudPi Instance (config, data, etc.) before you do this._

## Enable SSH
If you already have SSH enabled, you can skip this.

- Open the NextCloudPi management ui by opening the NextCloudPi app.
- Find the "SSH" section under "Networking" in the menu on the left
- Make sure it's active and you know the password
- Login to your NextCloudPi with SSH (e.g. `ssh pi@<server>` on linux)


## Add PHP 7.4 Package Archive
Execute the following commands on your NextCloudPi:

```bash
# Become root
sudo su

# Add the PHP PPA from deb.sury.org
apt-get -y install apt-transport-https lsb-release ca-certificates curl
curl -sSL -o /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
apt-get update

# Install PHP 7.4
apt-get -y install php7.4-fpm php7.4-mysql php7.4-xml php7.4-zip php7.4-mbstring php7.4-gd php7.4-curl php7.4-redis php7.4-intl php7.4-bcmath php7.4-gmp php7.4-imagick
```

## Set up Apache for PHP 7.4
Execute the following commands on your NextCloudPi:

```bash
# Become root
sudo su

# Configure apache for PHP7.4
a2enmod proxy_fcgi setenvif
a2enconf php7.4-fpm

# Disable PHP 7.3 for apache
a2disconf php7.3-fpm

# Symlink NCP PHP configuration
ln -s /etc/php/7.3/fpm/conf.d/90-ncp.ini /etc/php/7.4/fpm/conf.d/90-ncp.ini

# Restart PHP & Apache
systemctl restart php7.4-fpm
systemctl reload apache2
```

## Notes
- It can take a day before updates show up in the apps store