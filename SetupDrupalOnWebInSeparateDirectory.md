Installing Drupal into an existing website and running it from its own subdirectory (/drupal) can be done smoothly using Composer. Since you have shell access and Composer installed, follow these steps:
#### Note: For some webhosts that have PHP restrictions, you may need to change the PHP version to "8.3 'native' " to get the installer to work via composer.

## Step 1: Navigate to Your Website’s Root Directory
Open a terminal or SSH into your server.
Navigate to the root directory of your website (where the main site files are stored).
```
cd /path/to/your/website/root
```
## Step 2: Create the Drupal Directory and Navigate into It
Inside your website’s root, create the drupal subdirectory:
```
mkdir drupal
cd drupal
```
## Step 3: Install Drupal Using Composer
Run the following Composer command to install the Drupal core files in the drupal directory:
```
composer create-project drupal/recommended-project .
```
The . at the end ensures that the files are installed directly into the drupal folder rather than creating an extra subdirectory.
Composer will set up Drupal along with its dependencies. When it finishes, you’ll see the full Drupal installation in the drupal directory.
Note that at the time this document was created, Drupal v11 was available but not quite ready for "prime time".  To force composer to install the latest subrev of Drupal v10, use the following command:
```
composer create-project drupal/recommended-project:^10 .
```
## Step 4: Set Up Your Web Server to Recognize the /drupal Directory
If you want your main website at example.com to access Drupal at example.com/drupal:
#### Apache: 
Add a .htaccess file in the drupal directory if it’s not already there (it should come with the installation). Apache will then recognize the directory correctly.

#### Nginx: 
In your Nginx configuration, add a location block for /drupal if needed.

## Step 5: Set Up the Database
Log in to your database management tool (e.g., phpMyAdmin) or use the command line to create a new database for your Drupal installation.

For example, in MySQL:
```
mysql -u yourusername -p

CREATE DATABASE drupal_database;
CREATE USER 'drupal_user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON drupal_database.* TO 'drupal_user'@'localhost';
FLUSH PRIVILEGES;
```
Note down your database credentials (database name, username, password) for later use during the Drupal installation.

## Step 6: Run the Drupal Installer
Open your web browser and go to http://yourdomain.com/drupal/web.
The Drupal installer will start and guide you through the setup process:
Choose Language.
Select Installation Profile (usually “Standard”).
Configure Database: Enter the database name, username, and password you set up in Step 5.
Complete the remaining steps, including configuring your site name, site email, and administrative account.
## Step 7: Set Up Rewrite Rules (for Pretty URLs)
Drupal requires URL rewrites to enable clean URLs. Here’s how to do it:

Apache: In the Drupal .htaccess file, make sure the RewriteBase directive points to the /drupal subdirectory. Find and uncomment (or add) the following line:
```
RewriteBase /drupal
```
Nginx: Add a location block in your Nginx config:
```
location /drupal {
    try_files $uri /drupal/index.php?$query_string;
}
```
## Step 8: Test Your Installation
Visit http://yourdomain.com/drupal to see your Drupal site up and running.
Log in using the admin credentials you set up and start configuring your Drupal site.
And that's it! You should now have Drupal running in a subdirectory called /drupal within your existing website. 

## Step 9: Redirect access from document root
#### For Apache
In the Drupal .htaccess file, add the following to redirect queries from http://yourdomain.com/drupal to http://yourdomain.com/drupal/web, where the actual documents live:
```
RewriteRule ^$ /drupal/web/ [L,R=301]
```
#### For Nginx
Open the Nginx Configuration file for your site (usually located in /etc/nginx/sites-available/your-site).

Modify the root directive to point to the web directory:
```
root /path/to/your/website/root/drupal/web;
```
Restart Nginx to apply the changes:
```
sudo systemctl restart nginx
```
## Step 10: Install Drush via Composer
While considered optional, it is highly recommended that you install and use Drush to manage your Drupal installation.
Open a terminal in your Drupal project root (where your composer.json file is located).
Run this command to install Drush:
```
composer require drush/drush
```
Once installed, Drush can be run from the vendor/bin directory:
```
vendor/bin/drush status
```
## Step 11: Confirm Drush Installation
To confirm Drush is accessible, you can run:
```
vendor/bin/drush --version
```
## Step 12: Add Drush to PATH
To permanently add the location of Drush to the path.
#### A. Find the Correct File
The file to edit depends on your shell:

Bash: ~/.bashrc or ~/.bash_profile
Zsh: ~/.zshrc
Other shells: Check your shell's documentation.
#### B. Edit the File
Open the file in a text editor, e.g.:
```
nano ~/.bashrc
```
Add the following line at the end:
```
export PATH="/home/accountname/public_html/drupal/vendor/bin:$PATH"
```
Save and exit the editor (e.g., in Nano: Ctrl+O, Enter, then Ctrl+X).

#### C. Apply the Changes
Reload the configuration file:
```
source ~/.bashrc
```
or, if you're editing ~/.bash_profile:
```
source ~/.bash_profile
```
Verify the change:
```
echo $PATH
```
