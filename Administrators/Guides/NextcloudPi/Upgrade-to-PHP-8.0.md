## Before you start
- This tutorial was developed for and tested with NextCloudPi 1.35.0 on a RaspberryPI.
- NextCloudPi may behave differently on other systems.
- Nextcloud 21 is required _before_ upgrading to PHP 8.0. 
- This does not work for NextCloudPi Docker. You can't upgrade PHP in a Docker image.
- _Make sure to make a backup of your entire NextCloudPi Instance (config, data, etc.) before you do this._

## Enable SSH
If you already have SSH enabled, you can skip this.

- Open the NextCloudPi management ui by opening the NextCloudPi app.
- Find the "SSH" section under "Networking" in the menu on the left
- Make sure it's active and you know the password
- Login to your NextCloudPi with SSH (e.g. `ssh pi@<server>` on linux)


## Add PHP Package Archive
You can skip this step if you already followed the [Upgrade to PHP 7.4](./Upgrade-to-PHP-7.4) guide.
Execute the following commands on your NextCloudPi:

```bash
# Become root
sudo su

# Add the PHP PPA from deb.sury.org
apt-get -y install apt-transport-https lsb-release ca-certificates curl
curl -sSL -o /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
apt-get update
```


## Install PHP 8.0
Execute the following command on your NextCloudPi:

```bash
# Become root
sudo su

# Install PHP 8.0
apt-get -y install php8.0-fpm php8.0-mysql php8.0-xml php8.0-zip php8.0-mbstring php8.0-gd php8.0-curl php8.0-redis php8.0-intl php8.0-bcmath php8.0-gmp php8.0-imagick imagemagick
```


## Set up Apache for PHP 8.0
Execute the following commands on your NextCloudPi:

```bash
# Configure apache for php8.0
a2enmod proxy_fcgi setenvif
a2enconf php8.0-fpm

# Disable PHP 7.3 for apache if enabled
a2disconf php7.3-fpm
# Disable PHP 7.4 for apache if enabled.
# If you don't have PHP 7.4 installed, this will report an error which you can ignore
a2disconf php7.4-fpm

# Symlink NCP PHP configuration
ln -s /etc/php/7.3/fpm/conf.d/90-ncp.ini /etc/php/8.0/fpm/conf.d/90-ncp.ini

# Restart PHP & Apache
systemctl restart php8.0-fpm
systemctl reload apache2
```

## Check the PHP default version
By default, your NextCloudPi should now be using PHP 8.0.
You can check this by running `php -v`. The output should look like this:
```bash
root@nextcloudpi:/home/pi# php -v
PHP 8.0.3 (cli) (built: Mar  5 2021 08:38:30) ( NTS )
Copyright (c) The PHP Group
Zend Engine v4.0.3, Copyright (c) Zend Technologies
    with Zend OPcache v8.0.3, Copyright (c), by Zend Technologies

```

If it doesn't, you should use `update-alternatives --config php` and set PHP 8.0 as default:
```bash
root@nextcloudpi:/home/pi# update-alternatives --config php
There are 2 choices for the alternative php (providing /usr/bin/php).

  Selection    Path             Priority   Status
------------------------------------------------------------
* 0            /usr/bin/php8.0   80        auto mode
  1            /usr/bin/php7.3   73        manual mode
  2            /usr/bin/php7.4   74        manual mode
  3            /usr/bin/php8.0   80        manual mode

Press <enter> to keep the current choice[*], or type selection number:0
```

## Notes
- It can take a day before app updates show up in the apps store