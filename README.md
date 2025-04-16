# Graylog Installation on Ubuntu: A Complete Guide

Hi! My name is Reinaldi, and I’m passionate about IT security—especially when it comes to learning how security tools work under the hood.

This guide walks you through the process of setting up Graylog on an Ubuntu server. Graylog is an open-source log management platform that helps with collecting, indexing, and analyzing log data in real-time. The tutorial covers all the necessary steps, including setting up dependencies, installing MongoDB, Elasticsearch, and configuring Graylog for your use. all running inside VMware Fusion. I’ll be using SSH from my MacBook to access the Ubuntu VM in VMWare Fushion, so it’s all done remotely and cleanly.

---

## Prerequisites

Before starting, make sure you have:

- A server running **Ubuntu 20.04** or later.
- Terminal access to run system commands.

Let’s dive in!

---  
## Prerequisites
Ensure you have root privileges before proceeding with the installation.

```bash
sudo su
```

---

## Step 1: Update System Packages

First, update the system to ensure all packages are current:

```bash
apt update
```

```bash
apt install curl wget apt-transport-https
```
<img width="652" alt="Image" src="https://github.com/user-attachments/assets/5b8a25a2-965e-48aa-8914-a388fc71e92c" />

<img width="654" alt="Image" src="https://github.com/user-attachments/assets/295c1928-feed-4d17-ad6a-1b920e0e8260" />

---

## Step 2: Install Java

Graylog requires Java. Install OpenJDK 11:

```bash
apt install openjdk-11-jre -y
```

Verify the Java version:

```bash
java -version
```
<img width="650" alt="Image" src="https://github.com/user-attachments/assets/bb6f9278-d20a-454e-8eb8-93bad0fb7e6e" />

<img width="570" alt="Image" src="https://github.com/user-attachments/assets/c53f6c56-859c-453e-a65a-6aac322b8a79" />

---

## Step 3: Install MongoDB

Add the MongoDB GPG signing key:

```bash
curl -fsSL https://pgp.mongodb.com/server-6.0.asc | \
sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/mongodb-server-6.0.gpg
```
<img width="654" alt="Image" src="https://github.com/user-attachments/assets/9a93e839-ee6b-4298-8568-764d5211bd85" />

Add the MongoDB repository:

```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```
<img width="651" alt="Image" src="https://github.com/user-attachments/assets/a76c69c5-bc1b-45ff-9a52-9503c894899f" />


Update and install MongoDB:

```bash
apt update && apt install mongodb-org -y
```
<img width="648" alt="Image" src="https://github.com/user-attachments/assets/84105f1d-da0c-40cb-b5ef-d5fef51a6181" />

<img width="651" alt="Image" src="https://github.com/user-attachments/assets/e961faf5-4753-4d89-ab2c-3e305b6600ff" />

Start, enable MongoDB and Check MongoDB status:

```bash
systemctl start mongod
systemctl enable mongod
systemctl status mongod
```
<img width="656" alt="Image" src="https://github.com/user-attachments/assets/a0f3ea68-b5d9-4dcc-bafc-db5a09f4901f" />

To verify the version installed

```bash
mongod --version
```
<img width="500" alt="Image" src="https://github.com/user-attachments/assets/ef2a2d33-f9d3-49af-ac2d-64f2fc45f305" />

---

## Step 4: Install Elasticsearch

Install dependencies:

```bash
apt install apt-transport-https openjdk-11-jre -y
```

Add Elasticsearch GPG key and repository:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

Update and install:

```bash
apt update && apt install elasticsearch -y
```

Start and enable Elasticsearch:

```bash
systemctl start elasticsearch
systemctl enable elasticsearch
```

Verify Elasticsearch:

```bash
curl -X GET "localhost:9200/"
```

---

## Step 5: Install Graylog

Download and install the Graylog repository:

```bash
wget https://packages.graylog2.org/repo/packages/graylog-4.2-repository.deb
dpkg -i graylog-4.2-repository.deb
```

Update and install Graylog:

```bash
apt update && apt install graylog-server -y
```

---

## Step 6: Configure Graylog

Generate a password secret:

```bash
pwgen 96 1
```

Create a SHA256 hash of your admin password:

```bash
echo -n yourpassword | sha256sum
```

Edit the Graylog configuration:

```bash
nano /etc/graylog/server/server.conf
```

Set:

```properties
password_secret = <your_generated_secret>
root_password_sha2 = <your_hashed_password>
```

---

## Step 7: Start Graylog

Start and enable Graylog:

```bash
systemctl start graylog-server
systemctl enable graylog-server
```

Check Graylog status:

```bash
systemctl status graylog-server
```

---

## Step 8: Access the Graylog Web Interface

Once running, access Graylog via:

```
http://<your-server-ip>:9000
```

Login credentials:

- **Username:** `admin`
- **Password:** (the one you hashed earlier)

---

## ✅ Conclusion

You've successfully installed and configured Graylog on Ubuntu. You can now collect logs, monitor activity, and analyze data using the web interface. For more advanced setup options, check out the official [Graylog Documentation](https://docs.graylog.org/).
