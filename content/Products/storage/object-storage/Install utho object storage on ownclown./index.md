
# How to Install and Enable Utho Object Storage on OwnCloud
## Prerequisites

- OwnCloud installed and running
- SSH access to your server
- www-data user permissions

## Installation Steps
### 1. Change to the App Directory
Navigate to the directory where the files_external_s3 app is located:
```
cd /var/www/owncloud/apps/files_external_s3
```

### 2. Install Dependencies with Composer
Run the following command to install the required dependencies:
```
sudo -u www-data composer install --no-dev
```
### 3. Verify the Vendor Directory
Ensure that the vendor/autoload.php file exists:

```
ls -la /var/www/owncloud/apps/files_external_s3/vendor/autoload.php
```
### 4. Set Correct Permissions
Set appropriate permissions for the app directory:

```
sudo chown -R www-data:www-data /var/www/owncloud/apps/files_external_s3
sudo chmod -R 755 /var/www/owncloud/apps/files_external_s3
```

### 5. Restart Web Server
For Apache:
```
sudo systemctl restart apache2
```
For NGINX:
```
sudo systemctl restart nginx
```
### 6. Enable the App
Enable the S3 app in OwnCloud:
```
sudo -u www-data php /var/www/owncloud/occ app:enable files_external_s3
```
### 7. Verify Installation
Confirm that the app has been successfully installed:

```
sudo -u www-data php /var/www/owncloud/occ app:list | grep files_external
```

![alt text](<images/Screenshot from 2025-04-02 16-40-39.png>)

Log in to the OwnCloud page. On the homepage, click on the "External Storage" option.

![alt text](<images/Screenshot from 2025-04-02 16-53-17.png>)
From the dropdown, choose Amazon S3 compatible (SDK v3)

![alt text](<images/Screenshot from 2025-04-02 16-54-40.png>)

Now fill in the required details like Access key ,Bucket,Hostname, Enable SSL, Enable Path Style, Access key, Secret key

![alt text](<images/Screenshot from 2025-04-02 16-59-42.png>)

