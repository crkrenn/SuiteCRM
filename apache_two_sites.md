Yes, the steps outlined in your guide are generally applicable for setting up multiple sites on an Ubuntu server with Apache 2. While the exact versions of Ubuntu and Apache might introduce some minor differences, the fundamental process remains consistent. Here are the steps adjusted for clarity and to ensure compatibility with the latest versions of Ubuntu (e.g., Ubuntu 24.04) and Apache 2.4:

### Preflight
- **Login as root** or use `sudo` for each command if you're logged in as a non-root user.

### Step 1: Make a Directory for Each Site

```sh
sudo mkdir -p /var/www/crm.democracygps.org/public_html
sudo mkdir -p /var/www/crm-test.democracygps.org/public_html
```

### Step 2: Set Folder Permissions

```sh
sudo chmod -R 755 /var/www
```

### Step 3: Set up an Index Page

For each domain, create a simple index page:

```sh
sudo emacs /var/www/crm.democracygps.org/public_html/index.html
```

Add the following content:

```
Testing for crm.democracygps.org
```

Save and quit by hitting the `Escape` button and typing `:wq`.

Repeat for the second domain:

```sh
sudo emacs /var/www/crm-test.democracygps.org/public_html/index.html
```

Add the following content:

```
Testing for crm-test.democracygps.org
```

### Step 4: Copy the Config File for Each Site

```sh
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/crm.democracygps.org.conf
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/crm-test.democracygps.org.conf
```

### Step 5: Edit the Config File for Each Site

Edit the configuration file for each domain:

```sh
sudo emacs /etc/apache2/sites-available/crm.democracygps.org.conf
```

Modify the file to look like this:

```apache
<VirtualHost *:80>
    ServerAdmin admin@democracygps.org
    ServerName crm.democracygps.org
    ServerAlias www.crm.democracygps.org
    DocumentRoot /var/www/crm.democracygps.org/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Save and quit (`:wq`). Repeat for the second domain:

```sh
sudo emacs /etc/apache2/sites-available/crm-test.democracygps.org.conf
```

Modify the file to look like this:

```apache
<VirtualHost *:80>
    ServerAdmin admin@democracygps.org
    ServerName crm-test.democracygps.org
    ServerAlias www.crm-test.democracygps.org
    DocumentRoot /var/www/crm-test.democracygps.org/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### Step 6: Enable Your Config File

Disable the default site configuration:

```sh
sudo a2dissite 000-default.conf
```

Enable the new site configurations:

```sh
sudo a2ensite crm.democracygps.org.conf
sudo a2ensite crm-test.democracygps.org.conf
```

Restart Apache to apply the changes:

```sh
sudo systemctl restart apache2
```

### Step 7: Verify Apache Configurations

1. **Editing `/etc/hosts` file on your local machine**:
   - Add entries for the new domains, pointing them to your server’s IP address:

   ```sh
   sudo emacs /etc/hosts
   ```

   Add the following lines:

   ```
   your_server_ip crm.democracygps.org
   your_server_ip www.crm.democracygps.org
   your_server_ip crm-test.democracygps.org
   your_server_ip www.crm-test.democracygps.org
   ```

2. **Updating DNS settings for your domains**:
   - Make sure that the DNS A records for `crm.democracygps.org` and `crm-test.democracygps.org` point to your server’s IP address.

After making these changes, you should be able to visit `http://crm.democracygps.org` and `http://crm-test.democracygps.org` in a web browser and see the respective index.html pages.

### Additional Tips

- Ensure that your firewall allows HTTP traffic (port 80).
- For HTTPS support, you can enable SSL and use tools like Certbot to obtain and install SSL certificates.
- If you encounter any issues, check the Apache error logs for more information:

```sh
sudo tail -f /var/log/apache2/error.log
```

Following these steps should help you set up multiple sites on an Ubuntu 24.04 server running Apache 2.4.



The `default-ssl.conf` configuration file is for HTTPS traffic (port 443), and it serves the content from `/var/www/html` directory. If you want to serve your domains (`crm.democracygps.org` and `crm-test.democracygps.org`) over HTTPS, you will need to create separate SSL virtual host configurations for each domain, and disable the default SSL configuration.

### Step-by-Step Instructions:

1. **Duplicate the SSL Configuration for Each Site**

   **For `crm.democracygps.org`:**
   ```sh
   sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/crm.democracygps.org-ssl.conf
   ```

   **For `crm-test.democracygps.org`:**
   ```sh
   sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/crm-test.democracygps.org-ssl.conf
   ```

2. **Edit the SSL Configuration Files**

   **For `crm.democracygps.org`:**
   ```sh
   sudo emacs /etc/apache2/sites-available/crm.democracygps.org-ssl.conf
   ```

   Modify it to look like this:
   ```apache
   <VirtualHost *:443>
       ServerAdmin admin@democracygps.org
       ServerName crm.democracygps.org
       ServerAlias www.crm.democracygps.org
       DocumentRoot /var/www/crm.democracygps.org/public_html

       ErrorLog ${APACHE_LOG_DIR}/crm.democracygps.org_error.log
       CustomLog ${APACHE_LOG_DIR}/crm.democracygps.org_access.log combined

       SSLEngine on
       SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
       SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key

       <FilesMatch "\.(?:cgi|shtml|phtml|php)$">
           SSLOptions +StdEnvVars
       </FilesMatch>
       <Directory /usr/lib/cgi-bin>
           SSLOptions +StdEnvVars
       </Directory>
   </VirtualHost>
   ```

   **For `crm-test.democracygps.org`:**
   ```sh
   sudo emacs /etc/apache2/sites-available/crm-test.democracygps.org-ssl.conf
   ```

   Modify it to look like this:
   ```apache
   <VirtualHost *:443>
       ServerAdmin admin@democracygps.org
       ServerName crm-test.democracygps.org
       ServerAlias www.crm-test.democracygps.org
       DocumentRoot /var/www/crm-test.democracygps.org/public_html

       ErrorLog ${APACHE_LOG_DIR}/crm-test.democracygps.org_error.log
       CustomLog ${APACHE_LOG_DIR}/crm-test.democracygps.org_access.log combined

       SSLEngine on
       SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
       SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key

       <FilesMatch "\.(?:cgi|shtml|phtml|php)$">
           SSLOptions +StdEnvVars
       </FilesMatch>
       <Directory /usr/lib/cgi-bin>
           SSLOptions +StdEnvVars
       </Directory>
   </VirtualHost>
   ```

3. **Enable the New SSL Sites**

   Enable the new SSL sites with the following commands:
   ```sh
   sudo a2ensite crm.democracygps.org-ssl.conf
   sudo a2ensite crm-test.democracygps.org-ssl.conf
   ```

4. **Disable the Default SSL Site**

   Disable the default SSL site:
   ```sh
   sudo a2dissite default-ssl.conf
   ```

5. **Restart Apache**

   Restart Apache to apply the changes:
   ```sh
   sudo systemctl restart apache2
   ```

### Summary

Now, you should have individual SSL configurations for `crm.democracygps.org` and `crm-test.democracygps.org`, each pointing to their respective document roots. This will ensure that the correct content is served over both HTTP and HTTPS for each domain. If you have real SSL certificates for these domains, replace the `SSLCertificateFile` and `SSLCertificateKeyFile` directives with the paths to your actual certificate and key files.
