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

Install OpenJDK, we need to install Java :

```bash
apt install openjdk-17-jre-headless -y
```
<img width="650" alt="Image" src="https://github.com/user-attachments/assets/8f9cd2a4-e543-4c98-8861-4e0abb1db07d" />

We can confirm the version of Java

```bash
java -version
```
<img width="570" alt="Image" src="https://github.com/user-attachments/assets/98323be7-c4d4-44b6-ba2c-82b6e0b8bb3a" />


After that, we can download and add the Elasticsearch GPG Key and repository:

```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | tee -a /etc/apt/sources.list.d/elastic-7.x.list
```
<img width="650" alt="Image" src="https://github.com/user-attachments/assets/0d531ff5-38b3-40a6-bfc5-bfb20090ac82" />

<img width="654" alt="Image" src="https://github.com/user-attachments/assets/43878ece-ee80-4495-9739-b3108ef3861c" />


Update and install:

```bash
apt update
apt install elasticsearch -y
```
<img width="646" alt="Image" src="https://github.com/user-attachments/assets/fc7ab99c-206d-4fc3-a703-f6b9104f31ce" />

<img width="651" alt="Image" src="https://github.com/user-attachments/assets/e63c6457-94b6-4790-8515-24dc55c40ceb" />

After installing Elasticsearch, you'll need to adjust a few settings in its main configuration file. Open the file using any text editor you're comfortable with — in this example, we'll use nano from the command line.

```bash
nano /etc/elasticsearch/elasticsearch.yml
```
<img width="613" alt="Image" src="https://github.com/user-attachments/assets/1bfd4a81-5a2f-4b93-bbcd-dbccdb776be2" />

Specify your desired cluster.name, and immediately after, append action.auto_create_index: false to prevent Elasticsearch from creating indices on its own.

```bash
cluster.name: graylog
action.auto_create_index: false
```
<img width="637" alt="Image" src="https://github.com/user-attachments/assets/4f3d8d14-011e-43e3-a599-24dfafb43a08" />

Reload systemd for the change to apply, Start and enabling Elasticsearch service to start on boot.

```bash
systemctl daemon-reload
systemctl start elasticsearch
systemctl enable elasticsearch
```
<img width="650" alt="Image" src="https://github.com/user-attachments/assets/e69b06ff-e16b-4cff-99d1-85d094349994" />

Verify the status of Elasticsearch

```bash
systemctl status elasticsearch
```
<img width="654" alt="Image" src="https://github.com/user-attachments/assets/ae55db0f-c2a3-4308-b9c2-9a40700a5820" />

Verify Elasticsearch:

```bash
curl -X GET "localhost:9200/"
```
<img width="545" alt="Image" src="https://github.com/user-attachments/assets/77155a95-6716-4060-b291-731e9c9ef932" />

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
