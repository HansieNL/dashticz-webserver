# Setting up an NGINX web server with PHP for Dashticz on a Raspberry Pi

NGINX (pronounced *engine x*) is a popular lightweight web server application you can install on the Raspberry Pi to allow it to serve web pages.

## Refresh database of available packages

Ensure that the package manager has up-to-date information about which packages are available:  

```bash
sudo apt-get update
```

You only need to do this occasionally, but it's the most likely solution if subsequent steps fail with messages like:
```
  404  Not Found [IP: 93.93.128.193 80]
```

## Install NGINX

First install the `nginx` package by typing the following command in to the Terminal:

```bash
sudo apt-get install nginx
```

and start the server with:

```bash
sudo /etc/init.d/nginx start
```

## Test the web server

By default, NGINX puts a test HTML file in the web folder. This default web page is served when you browse to `http://192.168.1.10` (whatever the Pi's IP address is) from another computer on the network.

Browse to the default web page from another computer on the network and you should see a page with a NGINX welcome message.

### Changing permissions

Change permissions on the web directory `/var/www/html` by typing the following commands in to the Terminal:

```bash
sudo chown www-data:www-data /var/www/html
sudo chmod 775 /var/www/html
sudo usermod -a -G www-data pi
```

To take effect you have to close and reopen the Terminal. Now you can create directories and add files as user `pi`.


## Install PHP

```bash
sudo apt-get install php-fpm php7.0-curl php7.0-xml
```

### Enable PHP in NGINX

```bash
cd /etc/nginx
sudo nano sites-enabled/default
```

find the line

```
index index.html index.htm index.nginx-debian.html;
```

roughly around line 44 (Press `CTRL + C` in nano to see the current line number)

Add `index.php` after `index` to look like this:

```
index index.php index.html index.htm index.nginx-debian.html;
```

Scroll down until you find a section with the following content:

```
# pass the PHP scripts to FastCGI server
#
# location ~ \.php$ {
```

Edit by removing the `#` characters on the following lines:

```
location ~ \.php$ {
	include snippets/fastcgi-php.conf;
	fastcgi_pass unix:/var/run/php7.0-fpm.sock;
}
```

It should look like this:

```
        # pass the PHP scripts to FastCGI server
        #
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
        #
	#	# With php-fpm (or other unix sockets):
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	#	# With php-cgi (or other tcp sockets):
	#	fastcgi_pass 127.0.0.1:9000;
        }
```

Reload the configuration file

```bash
sudo /etc/init.d/nginx reload
```

### Test PHP

Create a file `index.php`:

```bash
cd /var/www/html/
nano index.php
```

Add some dynamic PHP content:
```php
<?php echo phpinfo(); ?>
```

Save and refresh your browser. You should see a page with the PHP version, logo and current configuration settings.


## Install Git

Install the `git` package by typing the following command in to the Terminal:

```bash
sudo apt-get install git
```

Now you can clone Dashticz’s repositories.


## Clone Dashticz

Install Dashticz master by typing the following command in to the Terminal:

```bash
cd /var/www/html
git clone https://github.com/Dashticz/dashticz --branch master
```

Or if you want to install the beta branch, type the following command in to the Terminal:

```bash
cd /var/www/html
git clone https://github.com/Dashticz/dashticz --branch beta
```

### Loading Dashticz...

Browse to `http://192.168.1.10/dashticz` (whatever the Pi's IP address is) and you should see a Dashticz loading page.

### Update Dashticz

In the future you can simply update Dashticz by typing the following commands in to the Terminal:

```bash
cd /var/www/html/dashticz
git pull
```

### Configure Dashticz

Now it's time to start reading [Dashticz’s documentation](https://dashticz.readthedocs.io) and learn how to configure Dashticz.
