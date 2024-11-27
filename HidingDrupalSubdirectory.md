To make Drupal appear as if it’s running from the root of your website, even though it’s installed in the /drupal subdirectory, you can use URL rewrites. Here’s how to set it up depending on your web server:

## For Apache
#### Edit the .htaccess file in your website’s root directory to rewrite requests to the /drupal folder:

Create or open the .htaccess file in the root directory of your website.
Add the following rewrite rules:
```
RewriteEngine on
RewriteCond %{REQUEST_URI} !^/drupal/web/
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /drupal/web/$1 [L]
RewriteCond %{REQUEST_URI} ^/$
RewriteRule ^(.*)$ /drupal/web/index.php [L]
```
#### Edit the .htaccess file in the /drupal directory:

Locate the RewriteBase directive and update it to:
```
RewriteBase /drupal
```
#### Edit the settings.php file in the /drupal/web/sites/default:
Locate that file and add the following to it:
$base_url = 'https://ryumaou.com/drupal';

if (isset($GLOBALS['request']) and
'/drupal/web/index.php' === $GLOBALS['request']->server->get('SCRIPT_NAME')) {
$GLOBALS['request']->server->set('SCRIPT_NAME', '/index.php');
}
```
#### Clear Drupal’s Cache:

Log in to your Drupal admin panel, navigate to Configuration > Development > Performance, and click Clear All Caches.
Also, I reccomend that you flush them via drush with the command:
```
drush cr
```

## For Nginx
#### Open your Nginx configuration file (usually located in /etc/nginx/sites-available/your-site).

Add the following location blocks to direct traffic from the root to the /drupal folder:
```
location / {
    try_files $uri $uri/ /drupal/index.php?$query_string;
}

location /drupal {
    internal;
    alias /path/to/your/website/root/drupal;
    try_files $uri /drupal/index.php?$query_string;
}
```
#### Restart Nginx for the changes to take effect:
```
sudo systemctl restart nginx
```
#### Clear Drupal’s cache as described in the Apache steps.

After these changes, visitors to http://yourdomain.com will see Drupal as if it’s in the root directory, even though it’s actually in /drupal. This setup maintains your clean URLs and SEO consistency.
