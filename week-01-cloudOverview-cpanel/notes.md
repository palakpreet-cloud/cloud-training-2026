# Week 1 — Cloud Fundamentals & Web Hosting

## Cloud Computing Concepts

### Service Models

| Model | Full Form | What it means | Example |
|-------|-----------|---------------|---------|
| IaaS | Infrastructure as a Service | You get raw infrastructure — virtual machines, storage, networking. You manage everything above the hardware. | AWS EC2, Azure Virtual Machines |
| PaaS | Platform as a Service | You get a platform to deploy your application. No need to manage the underlying OS or servers. | AWS Elastic Beanstalk, Azure App Service |
| SaaS | Software as a Service | You get a ready-to-use software application. No infrastructure or platform management needed. | Gmail, Dropbox, Zoom |

**Key difference:** IaaS gives you the most control but most responsibility. SaaS gives you the least control but zero maintenance. PaaS sits in between.

---

### Deployment Models

| Model | Description | Use Case |
|-------|-------------|----------|
| Public Cloud | Infrastructure owned and managed by a third party (AWS, Azure, GCP). Shared across multiple customers. | Startups, small businesses, cost-sensitive workloads |
| Private Cloud | Infrastructure dedicated to a single organization. Can be on-premises or hosted. | Banks, hospitals, government — high compliance needs |
| Hybrid Cloud | Combination of public and private cloud. Data and applications can move between them. | Enterprises that want cloud flexibility but need to keep sensitive data on-premise |

---

## DNS — How It Works

### What is a DNS A Record?

An **A Record** (Address Record) maps a domain name to an IPv4 address.

Example:
```
google.com → 142.250.182.46
```

When you type a website address in your browser, DNS looks up the A record to find which server IP to connect to.

### What I practiced

Checked A records for multiple domains and identified which websites are hosted on the same server (same IP address). This means one physical/virtual server can host multiple websites — called **shared hosting**.

**Key finding:** Multiple domains can point to the same IP. This is how budget web hosting works — one server, many websites.

### Why this matters in cloud

In AWS, when you launch an EC2 instance or set up a load balancer, you point your domain's A record to that server's public IP (or use an Alias record for load balancers). Understanding DNS is foundational for any cloud deployment.

---

## Tools Used This Week

- **cPanel** — web-based server management panel used by hosting companies
- **FTP client** — for uploading files to a web server
- **DNS lookup tools** — for checking A records and server IPs

---
  
## Web Hosting Hands-On — cPanel & WordPress

### Subdomain
A subdomain is a prefix added to your main domain (e.g., `pcloud.server.com`).
In DNS terms, it's a separate A record pointing to the same or different server IP.
Naming convention used in training: `[name-initial][project].server.com`

### WordPress via cPanel
WordPress is a CMS (Content Management System) — SaaS-like experience but
self-hosted (making it closer to PaaS when deployed on managed hosting).

cPanel's Softaculous installer automates:
- Database creation (MySQL)
- WordPress file deployment to document root
- Admin user setup

**Cloud equivalent:** This is what AWS Elastic Beanstalk or Azure App Service
does for web apps — abstracts the server setup so you focus on the application.
