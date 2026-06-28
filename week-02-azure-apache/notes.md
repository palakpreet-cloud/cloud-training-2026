# Week 2 — Azure Virtual Machines & Apache Web Server

## Azure Virtual Machines

### What is a Virtual Machine?

A Virtual Machine (VM) is a software-based computer that runs inside a physical server. It has its own OS, CPU allocation, RAM, and storage — but shares the underlying hardware with other VMs.

In Azure, VMs are an **IaaS** offering — you get full control over the OS and everything installed on it. Azure manages the physical hardware underneath.

**AWS parallel:** Azure Virtual Machines = AWS EC2.

### Azure VM Key Concepts

| Concept | What it means |
|---------|---------------|
| Resource Group | A logical container that holds related Azure resources (VM, public IP, disk) together |
| Public IP Address | An IP assigned to the VM so it can be reached over the internet |
| Region | The Azure data centre location where your VM is deployed |

### Windows VM vs Linux VM

| | Windows VM | Linux VM |
|--|------------|----------|
| Access method | RDP (Remote Desktop Protocol) — port 3389 | SSH (Secure Shell) — port 22 |
| Use case | GUI-based administration, .NET workloads | Web servers, cloud-native apps, scripting |
| Cost | Higher (Windows licence included) | Lower (open source OS) |

---

## Linux on the VM

### Basic Commands Practised

```bash
pwd                   # print working directory — shows your current location
ls                    # list files and folders in current directory
cd /path/to/folder    # change directory
sudo <command>        # run a command as administrator (root)
```

### Users and Home Directories

Linux is a multi-user OS. Every user has a home directory at `/home/username`.

The username set during VM creation is the default SSH login user. Running `pwd` right after login shows `/home/username` — confirming which user you are logged in as.

---

## Apache Web Server

### What is Apache?

Apache HTTP Server (`apache2` on Ubuntu) is open-source software that listens for HTTP requests and responds by serving HTML files from a specified folder on the server.

When someone visits your server's IP address in a browser, Apache receives the request and returns the webpage.

**Cloud parallel:** Apache on a VM is the manual version of what AWS manages automatically with S3 static hosting or Elastic Beanstalk. Understanding Apache shows you what those managed services are abstracting away underneath.

### Installation and Status Check

```bash
# Update packages then install Apache
sudo apt update && sudo apt upgrade -y
sudo apt install apache2

# Verify Apache is running
sudo systemctl status apache2

# Start / Stop / Restart
sudo systemctl start apache2
sudo systemctl stop apache2
sudo systemctl restart apache2

# Check version
apache2 -v
```

After installation, visiting `http://<server-public-ip>` in a browser shows the Apache default page — confirmation the web server is live.

---

## Document Root

### What is Document Root?

The Document Root is the folder on the server where Apache looks for website files to serve. When a visitor hits the server IP, Apache serves `index.html` from this folder.

**Default document root on Ubuntu:**
```
/var/www/html/
```

### Uploading Files to the Server — SCP

SCP (Secure Copy Protocol) transfers files from your local machine to the remote server over SSH.

```bash
# Copy a single file
scp filename.html username@server-ip:/home/username/

# Copy an image file
scp profile.jpeg username@server-ip:/home/username/

# Copy a folder recursively (all files inside)
scp -r foldername username@server-ip:/home/username/
```

The `-r` flag means recursive — required when copying folders so all files and subfolders are included.

After uploading, copy files from home directory to the document root:

```bash
sudo cp -r /home/username/* /var/www/html/
```

**AWS parallel:** SCP = manually pushing files to an EC2 instance. In production this is replaced by CI/CD pipelines (GitHub Actions) or `aws s3 cp`. Knowing SCP is essential for understanding and debugging live servers.

### Changing the Document Root

You can point Apache to serve files from any folder — not just `/var/www/html`. This is useful when you want to host files directly from a user's home directory.

**Two files need to be edited:**

**1. `/etc/apache2/sites-available/000-default.conf`** — the VirtualHost config for your site

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

Change the `DocumentRoot` line to your new folder:

```apache
DocumentRoot /home/username/foldername
```

**2. `/etc/apache2/apache2.conf`** — the global Apache config

Apache denies access to any directory outside `/var/www` and `/usr/share` by default. To allow your new folder, add a `<Directory>` block:

```bash
sudo nano /etc/apache2/apache2.conf
```

Add:

```apache
<Directory /home/username/foldername>
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

Without this block, Apache throws a **403 Forbidden** error even if `DocumentRoot` is correctly set.

**3. Restart Apache to apply all changes:**

```bash
sudo systemctl restart apache2
```

### Hosting Two Different Websites

A single Apache server can serve different websites from different document roots by updating the same config files. Each time you change the `DocumentRoot` and the `<Directory>` block, and restart Apache, the server now serves the new website.

This is the foundation of **Virtual Hosting** — one server, multiple websites. At scale, cloud platforms use load balancers and target groups to achieve the same result across many servers.

---

## Key Concepts Summary

| Concept | What it is | AWS Equivalent |
|---------|------------|----------------|
| Azure VM | Virtual machine in the cloud | EC2 instance |
| Resource Group | Container for related Azure resources | CloudFormation stack / tag-based grouping |
| Apache2 | Web server software serving HTML over HTTP | EC2 + Apache, or ALB + S3 for static sites |
| Document Root | Folder Apache serves website files from | S3 bucket (static hosting) |
| SCP | Secure file transfer over SSH to remote server | `aws s3 cp`, or CI/CD pipeline in production |
| VirtualHost config | Per-site Apache settings | ALB listener rules |
| `<Directory>` block | Grants Apache permission to access a folder | S3 bucket policy |
