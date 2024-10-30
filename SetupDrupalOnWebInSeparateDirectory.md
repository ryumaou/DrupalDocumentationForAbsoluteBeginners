Installing Drupal into an existing website and running it from its own subdirectory (/drupal) can be done smoothly using Composer. Since you have shell access and Composer installed, follow these steps:

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
## Step 4: Set Up Your Web Server to Recognize the /drupal Directory
If you want your main website at example.com to access Drupal at example.com/drupal:
```
Apache: Add a .htaccess file in the drupal directory if it’s not already there (it should come with the installation). Apache will then recognize the directory correctly.
```
```
Nginx: In your Nginx configuration, add a location block for /drupal if needed.
```
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
