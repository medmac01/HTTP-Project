# Project 3 : HyperText Transfer Protocol 🌎 (Apache2) 

# Index
- Part I : Apache2 server configuration
- Part II: HTTP and DNS
- Part III: Secure a repository
- Part IV: Configuration with .htaccess
- Part IV: Personal Directories

# Part I : Apache2 server configuration
  In this section we will confirgure our apache2 server for its first use.

1- **Installation**:
  We install the apache2 package using apt or yum by the following command `apt-get install apache2`.
  
2- **Configuration**:
  After installing the package, configuration files are created in `/etc/apache2/. It looks something like this:
  
  ```
-rw-r--r-- 1 root root  7224 Aug 12 05:37 apache2.conf
drwxr-xr-x 2 root root  4096 Sep  8 05:42 conf-available
drwxr-xr-x 2 root root  4096 Sep  8 05:42 conf-enabled
-rw-r--r-- 1 root root  1782 Aug  8  2020 envvars
-rw-r--r-- 1 root root 31063 Aug  8  2020 magic
drwxr-xr-x 2 root root 12288 Sep  8 05:42 mods-available
drwxr-xr-x 2 root root  4096 Sep  8 05:42 mods-enabled
-rw-r--r-- 1 root root   320 Aug  8  2020 ports.conf
drwxr-xr-x 2 root root  4096 Dec 13 09:13 sites-available
drwxr-xr-x 2 root root  4096 Dec  1 03:22 sites-enabled
  ```
  - `sites-available` : contains the configuration files of the available sites
  - `sites-enabled` : contains symbolic links to configurations, in site-available, activated sites
  - `conf-available` : contains configuration files for other available services
  - `conf-enabled` : contains symbolic links to the configurations, in conf-available, of other activated services
  - `mods-available` : contains the configuration files of the available Apache modules
  - `mods-enabled` : contains symbolic links to configurations, in mods-available, enabled modules

3- **Website Configuration**
Our websites files will be stored in `/var/www/`. So we will create two new folders for our two websites using the following command : `mkdir medmachrouh monsefber`.

By default, a website's main page is named `index.html`. So we will create an `index.html` file for each of our websites.

The next step is to configure the websites with apache. To do so, we create two new configuration files (Virtualhosts) `/etc/apache2/sites-available`. (We can duplicate the default file).

The configuration file similarly to the following:

![2  virtual host](https://user-images.githubusercontent.com/56129562/145835609-3e837517-527e-4fac-b238-9b6b13ef0a9c.png)

  - `ServerName 10.0.2.15` : This virtual host will only be called for the address `10.0.2.15`.
  - `ServerAlias 10.0.2.15`: We can specify here the subdomains for which this virtual host can be called.
  - `<Directory "/var/www/medmachrouh.me">`: Defines certain rules for the directory of the Virtual host.
  - `DocumentRoot "/var/www/medmachrouh.me"`: We will place the site files in the directory `/var/www/medmachrouh.me`.

We apply the same configuration for the second website.

Now, for the website to work we have to enable its vhost. To do so, we use the following command: `a2ensite medmachrouh.me.conf`

4-**Testing**:
After restarting the apache2 service, we type the address `10.0.2.15` in a browser and our website appears. ✅

![f  test 1](https://user-images.githubusercontent.com/56129562/145837273-1ca2581e-988f-400d-8235-e6b57d4838f9.png)
