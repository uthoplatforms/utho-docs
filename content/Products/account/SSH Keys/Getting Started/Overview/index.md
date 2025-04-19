## **Overview of SSH Keys in Utho Cloud Platform**

An **SSH key** in the Utho Cloud Platform is a cryptographic authentication method used to securely connect to cloud instances without needing to enter a password. SSH keys consist of a key pair: a **public key** and a  **private key** , which work together to authenticate users to remote servers, ensuring secure access to cloud resources.

### **Common Uses of SSH Keys**

* **Secure Authentication:** SSH keys provide a highly secure way to authenticate to remote cloud instances without the risks associated with password-based logins.
* **Password-less Login:** Once SSH keys are configured, users can log in to their cloud instances securely without needing to enter passwords, streamlining the connection process.
* **Automated Access:** SSH keys are often used in automated systems and scripts to securely log into cloud instances without human intervention.
* **Secure File Transfer:** SSH keys can be used in conjunction with tools like `SCP` and `SFTP` to securely transfer files to and from cloud instances.

### **SSH Keys in Utho's Cloud Platform**

In Utho’s cloud platform, users can easily create and manage SSH keys to access cloud instances securely. Users provide an **SSH key name** and **SSH key content** (typically in the `ssh-ed25519` format) to generate their SSH keys. These keys are then associated with cloud instances, allowing secure, password-less login.

By managing SSH keys through Utho's cloud interface, users can:

* **Create** new SSH keys for secure access.
* **Delete** or **update** existing SSH keys to ensure that only authorized users have access.

This functionality ensures that Utho Cloud customers can maintain a high level of security when managing their cloud resources, with streamlined access controls and secure authentication methods.
