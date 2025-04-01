# Migrating AWS Server to Utho Platform

## Step 1: Accessing Your AWS EC2 Instance
Before starting the migration, ensure you have an active AWS EC2 instance. In this guide, we will refer to an instance named ubuntu24 Pro. When you click on your server, you will find important details such as the Public IP and Instance ID.

![alt text](<images/Screenshot from 2025-03-19 14-31-24.png>)

## Step 2: Setting Permissions for the Private Key
Before using the private key, set the appropriate permissions to ensure security:
```
chmod 400 /home/ritss/Downloads/migration.pem
```

## Step 3: Logging into the EC2 Instance via SSH
Use the following command to connect to your instance via SSH using a private key:
```
ssh -i /path/to/your-key.pem default-instance-name@instance-ip
```
![alt text](<images/Screenshot from 2025-03-19 14-34-55.png>)


## Step 4: Enabling Password Authentication for SSH
- To allow SSH access using a password, modify the SSH configuration file.
- Editing the SSH Configuration File
- Open the SSH configuration file using a text editor:
```
sudo nano /etc/ssh/sshd_config
```

Locate and modify the following settings:
```
PasswordAuthentication yes
PermitRootLogin yes
UsePAM yes
```

### Restart the SSH Service
After making these changes, restart the SSH service:
```
sudo service ssh restart
```

## Step 5: Changing the Password for the Default User
To set a new password for the default user (e.g., ubuntu), use the following command:
```
passwd ubuntu
```

You will be prompted to enter and confirm a new password.

![alt text](<images/Screenshot from 2025-03-19 14-44-42.png>)

## Step 6: Logging into the Instance Using a Password
Once password authentication is enabled, you can log into the instance without using the private key:
```
ssh default-instance-name@instance-ip
```
![alt text](<images/Screenshot from 2025-03-19 14-48-06.png>)

### **Note** - Before running migration command user can switch to root.

## Step 7: Migrating to the UTHO Platform
To proceed with the migration, initiate the migration process on the UTHO platform using the following command:
```
curl https://api.utho.com/migration.sh | bash -s WYluVytfkPJrKXiUQsnMvzadNgeSCDToHbjAxcOqEwBhRZIFLGmp 1637600
```



```
  root@ip-172-31-41-152:~$ curl https://api.utho.com/migration.sh | bash -s WYluVytfkPJrKXiUQsnMvzadNgeSCDToHbjAxcOqEwBhRZIFLGmp 1637600
  % Total	% Received % Xferd  Average Speed   Time	Time 	Time  Current
                             	Dload  Upload   Total   Spent	Left  Speed
100 22211  100 22211	0 	0   140k  	0 --:--:-- --:--:-- --:--:--  140k
API token is valid.
Cloud ID  is valid.
2025-03-19 09:32:06 - Migration
2025-03-19 09:32:06 - Detected OS: ubuntu, Version: 24.04
2025-03-19 09:32:06 - Checking for jq installation...
2025-03-19 09:32:06 - jq is already installed.
2025-03-19 09:32:06 - Checking power status from API: https://api.utho.com/v2/cloud/1637600
2025-03-19 09:32:08 - Power status is 'Running'. Proceeding with the next steps.
2025-03-19 09:32:09 - Migration
2025-03-19 09:32:09 - Migration
lsblk: /dev/vda: not a block device
2025-03-19 09:32:10 - Migration
2025-03-19 09:32:11 - Migration
2025-03-19 09:32:11 - Sending configuration to API: https://api.utho.com/v2/cloud/1637600/enablerescue
2025-03-19 09:32:40 - Server rescue mode is enabled
2025-03-19 09:32:41 - Migration
2025-03-19 09:32:42 - Migration
2025-03-19 09:32:42 - Pinging server 103.189.88.55 (Attempt 1/10)...
2025-03-19 09:32:45 - Server 103.189.88.55 is not reachable. Retrying in 20 seconds...
2025-03-19 09:33:05 - Pinging server 103.189.88.55 (Attempt 2/10)...
2025-03-19 09:33:08 - Server 103.189.88.55 is not reachable. Retrying in 20 seconds...
2025-03-19 09:33:28 - Pinging server 103.189.88.55 (Attempt 3/10)...
2025-03-19 09:33:31 - Server 103.189.88.55 is not reachable. Retrying in 20 seconds...
2025-03-19 09:33:51 - Pinging server 103.189.88.55 (Attempt 4/10)...
2025-03-19 09:33:53 - Server 103.189.88.55 is not reachable. Retrying in 20 seconds...
2025-03-19 09:34:13 - Pinging server 103.189.88.55 (Attempt 5/10)...
2025-03-19 09:34:14 - Server 103.189.88.55 is not reachable. Retrying in 20 seconds...
2025-03-19 09:34:34 - Pinging server 103.189.88.55 (Attempt 6/10)...
2025-03-19 09:34:37 - Server 103.189.88.55 is not reachable. Retrying in 20 seconds...
2025-03-19 09:34:57 - Pinging server 103.189.88.55 (Attempt 7/10)...
2025-03-19 09:34:57 - Server 103.189.88.55 is reachable.
2025-03-19 09:34:57 - Migration
2025-03-19 09:34:57 - Checking for lsblk installation...
2025-03-19 09:34:57 - lsblk is already installed.
2025-03-19 09:34:58 - Migration
Select the source disk:
DEBUG: Global OS variable is set to 'ubuntu'
Available disks on Linux:
DEBUG: Available Disks - /dev/xvda
1) /dev/xvda
Choose the source disk (Enter the number): 1
DEBUG: User input was '1'
You selected: /dev/xvda
Source Disk Selected: /dev/xvda
sshpass is not installed. Installing now...
Hit:1 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble InRelease
Get:2 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]                                  	 
Get:3 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports InRelease [126 kB]                                	 
Get:4 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/universe amd64 Packages [15.0 MB]                           	 
Get:5 http://security.ubuntu.com/ubuntu noble-security InRelease [126 kB]                     	 
Hit:6 https://esm.ubuntu.com/apps/ubuntu noble-apps-security InRelease                                        	 
Hit:7 https://esm.ubuntu.com/apps/ubuntu noble-apps-updates InRelease                	 
Get:8 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/universe Translation-en [5982 kB]
Hit:9 https://esm.ubuntu.com/infra/ubuntu noble-infra-security InRelease       	 
Hit:10 https://esm.ubuntu.com/infra/ubuntu noble-infra-updates InRelease         	 
Get:11 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/universe amd64 Components [3871 kB]
Get:12 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/universe amd64 c-n-f Metadata [301 kB]
Get:13 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/multiverse amd64 Packages [269 kB]
Get:14 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/multiverse Translation-en [118 kB]
Get:15 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/multiverse amd64 Components [35.0 kB]
Get:16 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/multiverse amd64 c-n-f Metadata [8328 B]
Get:17 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/main amd64 Packages [921 kB]
Get:18 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/main Translation-en [209 kB]
Get:19 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/main amd64 Components [151 kB]
Get:20 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/main amd64 c-n-f Metadata [13.4 kB]
Get:21 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/universe amd64 Packages [1040 kB]
Get:22 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/universe Translation-en [262 kB]
Get:23 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/universe amd64 Components [364 kB]
Get:24 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/universe amd64 c-n-f Metadata [25.8 kB]
Get:25 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/restricted amd64 Packages [759 kB]
Get:26 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/restricted Translation-en [153 kB]
Get:27 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/restricted amd64 Components [212 B]
Get:28 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/restricted amd64 c-n-f Metadata [464 B]
Get:29 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/multiverse amd64 Packages [30.1 kB]
Get:30 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/multiverse Translation-en [5884 B]
Get:31 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/multiverse amd64 Components [940 B]
Get:32 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates/multiverse amd64 c-n-f Metadata [656 B]
Get:33 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/main amd64 Components [208 B]
Get:34 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/main amd64 c-n-f Metadata [112 B]
Get:35 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/universe amd64 Packages [14.2 kB]
Get:36 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/universe Translation-en [12.1 kB]
Get:37 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/universe amd64 Components [20.0 kB]
Get:38 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/universe amd64 c-n-f Metadata [1256 B]
Get:39 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/restricted amd64 Components [216 B]
Get:40 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/restricted amd64 c-n-f Metadata [116 B]
Get:41 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/multiverse amd64 Components [212 B]
Get:42 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports/multiverse amd64 c-n-f Metadata [116 B]
Get:43 http://security.ubuntu.com/ubuntu noble-security/main amd64 Packages [670 kB]
Get:44 http://security.ubuntu.com/ubuntu noble-security/main Translation-en [130 kB]
Get:45 http://security.ubuntu.com/ubuntu noble-security/main amd64 Components [9004 B]
Get:46 http://security.ubuntu.com/ubuntu noble-security/main amd64 c-n-f Metadata [6912 B]
Get:47 http://security.ubuntu.com/ubuntu noble-security/universe amd64 Packages [819 kB]
Get:48 http://security.ubuntu.com/ubuntu noble-security/universe Translation-en [177 kB]                                                                          	 
Get:49 http://security.ubuntu.com/ubuntu noble-security/universe amd64 Components [52.0 kB]                                                                       	 
Get:50 http://security.ubuntu.com/ubuntu noble-security/universe amd64 c-n-f Metadata [16.9 kB]                                                                   	 
Get:51 http://security.ubuntu.com/ubuntu noble-security/restricted amd64 Packages [726 kB]                                                                        	 
Get:52 http://security.ubuntu.com/ubuntu noble-security/restricted Translation-en [146 kB]                                                                        	 
Get:53 http://security.ubuntu.com/ubuntu noble-security/restricted amd64 Components [208 B]                                                                       	 
Get:54 http://security.ubuntu.com/ubuntu noble-security/restricted amd64 c-n-f Metadata [432 B]                                                                   	 
Get:55 http://security.ubuntu.com/ubuntu noble-security/multiverse amd64 Packages [26.2 kB]                                                                       	 
Get:56 http://security.ubuntu.com/ubuntu noble-security/multiverse Translation-en [4892 B]                                                                        	 
Get:57 http://security.ubuntu.com/ubuntu noble-security/multiverse amd64 Components [212 B]                                                                       	 
Get:58 http://security.ubuntu.com/ubuntu noble-security/multiverse amd64 c-n-f Metadata [448 B]                                                                   	 
Fetched 32.8 MB in 12s (2647 kB/s)                                                                                                                                	 
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  sshpass
0 upgraded, 1 newly installed, 0 to remove and 143 not upgraded.
Need to get 11.7 kB of archives.
After this operation, 35.8 kB of additional disk space will be used.
Get:1 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/universe amd64 sshpass amd64 1.09-1 [11.7 kB]
Fetched 11.7 kB in 0s (869 kB/s)
Selecting previously unselected package sshpass.
(Reading database ... 70615 files and directories currently installed.)
Preparing to unpack .../sshpass_1.09-1_amd64.deb ...
Unpacking sshpass (1.09-1) ...
Setting up sshpass (1.09-1) ...
Processing triggers for man-db (2.12.0-4build2) ...
Scanning processes...                                                                                                                                              	 
Scanning linux images...                                                                                                                                           	 

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
sshpass installed successfully.
2025-03-19 09:38:52 - Source Device: /dev/xvda
2025-03-19 09:38:52 - Destination Device: /dev/vdb
pv is not installed. Installing now...
Hit:1 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble InRelease
Hit:2 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-updates InRelease                     	 
Hit:3 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble-backports InRelease                   	 
Hit:4 http://security.ubuntu.com/ubuntu noble-security InRelease                                  	 
Hit:5 https://esm.ubuntu.com/apps/ubuntu noble-apps-security InRelease
Hit:6 https://esm.ubuntu.com/apps/ubuntu noble-apps-updates InRelease
Hit:7 https://esm.ubuntu.com/infra/ubuntu noble-infra-security InRelease
Hit:8 https://esm.ubuntu.com/infra/ubuntu noble-infra-updates InRelease
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
143 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Suggested packages:
  doc-base
The following NEW packages will be installed:
  pv
0 upgraded, 1 newly installed, 0 to remove and 143 not upgraded.
Need to get 73.9 kB of archives.
After this operation, 188 kB of additional disk space will be used.
Get:1 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu noble/main amd64 pv amd64 1.8.5-2build1 [73.9 kB]
Fetched 73.9 kB in 0s (4785 kB/s)
Selecting previously unselected package pv.
(Reading database ... 70620 files and directories currently installed.)
Preparing to unpack .../pv_1.8.5-2build1_amd64.deb ...
Unpacking pv (1.8.5-2build1) ...
Setting up pv (1.8.5-2build1) ...
Processing triggers for man-db (2.12.0-4build2) ...
Scanning processes...                                                                                                                                              	 
Scanning linux images...                                                                                                                                           	 

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
pv installed successfully.
2025-03-19 09:39:03 - Starting data migration from /dev/xvda to /dev/vdb on remote server 103.189.88.55.
dd: failed to open '/dev/xvda': Permission denied
0.00  B 0:00:00 [0.00  B/s] [<=>                                                                                                                                   	]
Warning: Permanently added '103.189.88.55' (ED25519) to the list of known hosts.
0+0 records in
0+0 records out
0 bytes copied, 0.0177413 s, 0.0 kB/s
2025-03-19 09:39:04 - Data migration completed successfully.
2025-03-19 09:39:04 - Migration
2025-03-19 09:39:04 - Starting detection and removal of cloud provider agents...
2025-03-19 09:39:04 - walinuxagent is not installed, skipping.
2025-03-19 09:39:04 - Removing cloud-init...
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  cloud-init*
0 upgraded, 0 newly installed, 1 to remove and 142 not upgraded.
After this operation, 3133 kB disk space will be freed.
(Reading database ... 70630 files and directories currently installed.)
Removing cloud-init (24.4-0ubuntu1~24.04.2) ...
Processing triggers for man-db (2.12.0-4build2) ...
(Reading database ... 70243 files and directories currently installed.)
Purging configuration files for cloud-init (24.4-0ubuntu1~24.04.2) ...
dpkg: warning: while removing cloud-init, directory '/etc/cloud/cloud.cfg.d' not empty so not removed
Processing triggers for rsyslog (8.2312.0-3ubuntu9) ...
2025-03-19 09:39:09 - Removing cloud-initramfs-copymods...
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  cloud-initramfs-copymods* ubuntu-server*
0 upgraded, 0 newly installed, 2 to remove and 141 not upgraded.
After this operation, 43.0 kB disk space will be freed.
(Reading database ... 70182 files and directories currently installed.)
Removing ubuntu-server (1.539.1) ...
Removing cloud-initramfs-copymods (0.49~24.04.1) ...
Processing triggers for initramfs-tools (0.142ubuntu25.4) ...
update-initramfs: Generating /boot/initrd.img-6.8.0-1021-aws
(Reading database ... 70173 files and directories currently installed.)
Purging configuration files for cloud-initramfs-copymods (0.49~24.04.1) ...
Processing triggers for initramfs-tools (0.142ubuntu25.4) ...
update-initramfs: Generating /boot/initrd.img-6.8.0-1021-aws
2025-03-19 09:39:21 - Removing cloud-initramfs-dyn-netconf...
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  cloud-initramfs-dyn-netconf*
0 upgraded, 0 newly installed, 1 to remove and 141 not upgraded.
After this operation, 28.7 kB disk space will be freed.
(Reading database ... 70173 files and directories currently installed.)
Removing cloud-initramfs-dyn-netconf (0.49~24.04.1) ...
Processing triggers for initramfs-tools (0.142ubuntu25.4) ...
update-initramfs: Generating /boot/initrd.img-6.8.0-1021-aws
(Reading database ... 70167 files and directories currently installed.)
Purging configuration files for cloud-initramfs-dyn-netconf (0.49~24.04.1) ...
Processing triggers for initramfs-tools (0.142ubuntu25.4) ...
update-initramfs: Generating /boot/initrd.img-6.8.0-1021-aws
2025-03-19 09:39:33 - aws-cli is not installed, skipping.
2025-03-19 09:39:33 - Removing ec2-instance-connect...
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  ec2-instance-connect*
0 upgraded, 0 newly installed, 1 to remove and 141 not upgraded.
After this operation, 56.3 kB disk space will be freed.
(Reading database ... 70167 files and directories currently installed.)
Removing ec2-instance-connect (1.1.17-0ubuntu1) ...
Deleted system user ec2-instance-connect
(Reading database ... 70156 files and directories currently installed.)
Purging configuration files for ec2-instance-connect (1.1.17-0ubuntu1) ...
Deleted system user ec2-instance-connect
2025-03-19 09:39:37 - amazon-ssm-agent is not installed, skipping.
2025-03-19 09:39:37 - google-cloud-sdk is not installed, skipping.
2025-03-19 09:39:37 - google-compute-engine-oslogin is not installed, skipping.
2025-03-19 09:39:37 - google-guest-agent is not installed, skipping.
2025-03-19 09:39:37 - openstack-cloud-init is not installed, skipping.
2025-03-19 09:39:37 - Removing cloud agent configuration files...
2025-03-19 09:39:37 - Updating initramfs...
update-initramfs: Generating /boot/initrd.img-6.8.0-1021-aws
2025-03-19 09:39:41 - Blacklisting CD-ROM modules...
blacklist cdrom
blacklist sr_mod
2025-03-19 09:39:41 - Cloud agent removal process completed.
2025-03-19 09:39:41 - Disabling server rescue mode...
jq: parse error: Invalid numeric literal at line 4, column 0
jq: parse error: Invalid numeric literal at line 4, column 0
2025-03-19 09:43:19 - Array
2025-03-19 09:43:19 - Server rescue mode is disabled
2025-03-19 09:43:20 - Migration
2025-03-19 09:43:21 - Migration
```

## Final Step: Verifying Migration on UTHO Platform
Once the migration command is successfully executed, your server will appear on the UTHO Platform.
![alt text](<images/Screenshot from 2025-03-19 15-17-29.png>)
After the migration command is successfully executed:


### Server Details via Email
- You will receive the new server details (IP, credentials, and other necessary information) on your registered email ID. Then you can login onto the server:

![alt text](<images/Screenshot from 2025-03-19 15-20-41.png>)