# Improving Performance with Maintenance

## 1. Backup Your Database
Before making changes, always create a backup of your database to avoid data loss:

With Drush:
```
drush sql-dump --result-file=backup.sql
```
With cPanel:
Go to phpMyAdmin.
Select your database, click Export, and save the file.

## 2. Clear Caches
Drupal generates a lot of cache data, which can inflate the database size. Clear these caches:

Via Drupal Admin:
Go to Configuration > Performance and click Clear all caches.
With Drush:
```
drush cr
```
## 3. Remove Old Revisions
Content revisions can accumulate over time, especially if revisions are frequently created:

Install the Revision Delete module:
Install via Composer:
```
composer require drupal/revision_delete
```
Enable the module:
```
drush en revision_delete -y
```
Use its interface under Configuration > Development > Delete revisions to remove unnecessary revisions.
## 4. Delete Orphaned Data
### A. Unused Files
Navigate to Content > Files and delete files not used in any content.
### B. Database Tables
Some modules leave behind tables when uninstalled. Identify and drop unnecessary tables in phpMyAdmin or via SQL commands. Be cautious and only remove tables you are certain are not needed.

## 5. Optimize Tables
Optimize database tables to reclaim unused space:

### With phpMyAdmin:
Select your database.
Click Check All, then choose Optimize table from the dropdown menu.
### With Drush: 
Install the DB Tools module for easier optimization:
```
composer require drupal/db_tools
```
Then use the module's optimization tools.
## 6. Remove Expired Sessions
Drupal stores user session data, which can build up over time:

Run the following query in phpMyAdmin or with Drush:
```
DELETE FROM sessions WHERE timestamp < UNIX_TIMESTAMP(DATE_SUB(NOW(), INTERVAL 1 MONTH));
```
## 7. Prune Logs
Database log tables can grow large if not managed:

Navigate to Reports > Recent log messages > Settings and set shorter retention periods.

Manually delete older entries from the watchdog table:
```
DELETE FROM watchdog WHERE timestamp < UNIX_TIMESTAMP(DATE_SUB(NOW(), INTERVAL 6 MONTH));
```
## 8. Use Database Maintenance Tools
### A. Adminer or phpMyAdmin:
Use these tools to:
Check for and repair broken tables.
Identify large tables for cleanup.
### B. Drush Maintenance Commands:
Run Drush maintenance commands like:
```
drush sql:optimize
```
## 9. Uninstall Unused Modules
Disable and uninstall modules you no longer use:

Disable the module via Extend > Uninstall.
Drop leftover tables related to the module (if applicable).
## 10. Review Custom Code
Check if any custom modules or themes are generating unnecessary database entries. Optimize custom code to reduce excessive writes or logs.

## 11. Audit and Monitor
Use the Devel module to monitor database queries and identify slow or redundant ones:
```
composer require drupal/devel
```
Use DB Tuner to identify inefficient database configurations.
## 12. Schedule Regular Maintenance
Set up a cron job to:
Clear old sessions.
Remove outdated logs.
Optimize tables regularly.
