
# How to Create a User and Grant Privileges on MariaDB for a Specific IP  

This guide provides step-by-step instructions to create a new user in MariaDB and grant them specific privileges for access from a particular IP address.  

---

## Steps to Create a User and Grant Privileges  

### 1. Log in to MariaDB  

Use the following command to log in to the MariaDB server as the root user:  

```bash
mysql -u root -p
```  
Enter the root password when prompted.  

---

### 2. Create the User  

Run the following command to create a new user for a specific IP address:  

```sql
CREATE USER 'username'@'ip_address' IDENTIFIED BY 'password';
```  

**Example**:  

```sql
CREATE USER 'testuser'@'192.168.1.100' IDENTIFIED BY 'SecurePassword123';
```  

This command creates a user `testuser` who can connect from the IP address `192.168.1.100` using the password `SecurePassword123`.  

---

### 3. Grant Privileges to the User  

To grant all privileges to the user on all databases and tables, run the following command:  

```sql
GRANT ALL PRIVILEGES ON *.* TO 'testuser'@'192.168.1.100';
```  

**Note**: Replace `*.*` with a specific database and table if you want to limit the user's access, such as `mydb.*` for all tables in the `mydb` database or `mydb.mytable` for a specific table.  

---

### 4. Apply the Changes  

After granting privileges, apply the changes with this command:  

```sql
FLUSH PRIVILEGES;
```  

---

### 5. Test the Connection  

To verify that the user can connect to the MariaDB server, use the following command from the allowed IP address:  

```bash
mysql -u username -p -h server_ip
```  

Replace `username` with the created user and `server_ip` with the server's IP address. Enter the user's password when prompted.  

---

### 6. Restart MariaDB (if necessary)  

If you encounter issues, restart the MariaDB service to ensure all changes take effect:  

```bash
sudo systemctl restart mariadb
```  

---

Now, your new user is successfully created, and they should have the privileges you granted for the specified IP address.  