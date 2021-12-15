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

> `cp 000-default.conf medmachrouh.me.conf && cp 000-default.conf monsefber.conf`

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


# Part II: HTTP and DNS
In order to make our website's address easy to remember, we will associate it with a domain name which is `medmac.me` in this case.
First, let's create the configuration files for our DNS zone.
> `cp /etc/bind/db.127 /etc/bind/db.2.0.10 && cp /etc/bind/db.local /etc/bind/db.medmac.me`

Our files should look like this:

**`db.medmac.me`** :

![part II forward](https://user-images.githubusercontent.com/56129562/145838255-447d2a3d-40cb-4500-ae6c-47266a624456.png)

**`db.2.0.10`**:
![part II reverse](https://user-images.githubusercontent.com/56129562/145838342-52850c94-e0f6-4707-a03c-4ebd0bad8eb5.png)


🧪 **Testing**:
After restarting the `named` service. We access our website by it's new domain name `medmac.me`, the same website shows up ✅.

![part II test 2](https://user-images.githubusercontent.com/56129562/145838875-2e25febf-d372-4a45-9b06-c9a22bb498cb.png)

Using curl we get the plain index.html file that we created already.

![test2](https://user-images.githubusercontent.com/56129562/145839007-362e0444-2ffb-43fc-8ace-c5229bf1a74e.png)


# Part III: Secure a repository
In this part, we will show how to password protect the repository on our web server.

**1.Creating the password file**

To get started, we need to create the file that will store the passwords needed to access our restricted content, we will use a utility called htpasswd
In this case, we will add the admin user to the file .htpasswd using the following command:
> `htpasswd -c /etc/apache2/.htpasswd admin`


We will be asked to provide a password for the admin account.

After executing the command, an `.htpasswd` will be created in the `/etc/apache2/` folder. When opening the file, here is what it looks like:  `admin:$apr1$MIrlEJvs$y2Y1DBpLCB70B55CC.wdm0`

**2.Configuring Access Control within the Virtual Host Definition**

Our mission here is to edit the Apache configuration and add our password protection to the virtual host file. So we open our configuration file `medmachrouh.me.conf` 

Within this `directory` block, specify that we wish to set up `Basic` authentication. For the `AuthName`, choose a message that will be displayed to the user when prompting for credentials. Use the `AuthUserFile` directive to point Apache to the password file we created (which is `.htpasswd`). Finally, we will require a `valid-user` to access this resource, which means anyone who can verify their identity with a password will be allowed in.

![image](https://user-images.githubusercontent.com/56129562/146222670-e1a2d19e-c648-4e60-b920-2a1e9d3beb3b.png)


**🧪 Testing**

When trying to access our website `medmac.me`, a login prompt shows up. That means we have to provide a user and a password in order to show the content.
![image](https://user-images.githubusercontent.com/56129562/146222101-5474acae-0a80-4a55-850b-bf9a448c7ebd.png)


⚠️ **Warning**: Since we are using basic authentication, the credentials are not encrypted. When logging in, we can easily extract the password from a Wireshark capture as shown below.

![image](https://user-images.githubusercontent.com/56129562/146225030-6c588377-284b-44bf-8f47-0bfdd4572ffe.png)

*To overcome this issue, we have to enable 🔒 **HTTPS** on our web server so that the exchanged traffic will be encrypted and nearly impossible to crack.*

# Part IV: Configuration with .htaccess

The .htaccess files are Apache configuration files, allowing to define rules in a directory and in all its subdirectories (which do not have such a file inside). They can be used to protect a directory with a password, to change the name or extension of the index page, or to prohibit access to the directory.

The .htaccess file is placed in the directory in which it must act. It thus acts on the permissions of the directory that contains it and of all its sub-directories. Another .htaccess file can be placed in a subdirectory of a directory already controlled by an .htaccess file. The .htaccess file in the parent directory remains in “activity” until the functionality is rewritten.

**1.Configuring Apache with .htaccess**
Inside the `<Directory>` block for the `/var/www` directory that holds our document root. We have to turn on `.htaccess` processing by changing the `AllowOverride` from “None” to “All”:

![image](https://user-images.githubusercontent.com/56129562/146226879-6cb00952-cbdb-4b39-82a5-482fb9daf103.png)

After that, we need to create an `.htaccess` file inside the `/var/www/medmachrouh.me` folder. 

Within this file, specify the arguments that we added earlier. The file should look like this:

![image](https://user-images.githubusercontent.com/56129562/146227928-d8bc984f-0ecf-4c32-8a27-dd2439da62f7.png)

We save the file and restart the service.


