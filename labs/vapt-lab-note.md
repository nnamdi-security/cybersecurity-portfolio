# Vulnerability Assessment & Penetration Testing Lab
## Project Overview

This lab documents the setup of a deliberately vulnerable web application (DVWA) and a vulnerability scanner (OpenVAS / Greenbone Vulnerability Management) on Kali Linux running in VMware

### Tools Used:

**Kali Linux**: Primary attacking OS.

**Docker**: To host the Damn Vulnerable Web App (DVWA).

**OpenVAS (GVM)**: For automated vulnerability scanning.

### Lab Setup & Configurations
**Target Environment (DVWA)**

To ensure a clean and repeatable environment, I deployed DVWA using Docker:

1. Update your Machine ```sudo apt update && sudo apt upgrade -y```

2. Install Docker ```sudo apt install docker.io -y```

3. Run DVWA: ```sudo docker run --rm -it -p 80:80 vulnerables/web-dvwa```

4. Access DVWA in Browser: Open browser and type ```http://localhost``` and login with ```admin/password```

**After logging in**:

1. Click **Setup / Reset DB** in the left menu

2. Scroll down

3. Click **Create / Reset Database**

Once you see **Database has been created**, DVWA is ready

### Vulnerability Scanner (OpenVAS/GVM)

**Minimum VMware Requirements**

Before installing OpenVAS, shut down Kali and set:

RAM: 4 GB minimum 

CPU: 2 cores minimum (4 recommended)

Storage: 40 GB+

Network: NAT

OpenVAS is heavy, under-resourcing causes silent failures.

**Steps**

1. **Install GVM**: ```sudo apt install gvm -y```
2. **Run initial setup**: ```sudo gvm-setup``` (This may take a while especially if your internet speed is poor. It will provide a password, copy it down)
3. **Verify setup**: ```sudo gvm-check-setup```

### Challenges & Resolutions
During the setup of the OpenVAS scanner, I encountered a critical database synchronization issue.

#### The Issue: PostgreSQL Collation Mismatch
When running ```sudo gvm-check-setup```, the process failed with the following error:

```ERROR: template database "template1" has a collation version mismatch DETAIL: The template database was created using collation version 2.41, but the operating system provides version 2.42.```

**Root Cause**

The Kali Linux system libraries (libc) were updated, but the existing PostgreSQL database cluster was still configured for an older version. This prevented the creation of the ```gvmd``` database.

**The Resolution**

Since this was a new installation, I performed a "hard reset" of the PostgreSQL cluster to align it with the system version using the following steps:

1. **Stop Services**: ```sudo systemctl stop postgresql```

2. **Drop Broken Cluster**: ```sudo pg_dropcluster --stop 18 main```(Note: replace 18 with your version number, if you don't know it, run ```pg_lsclusters```

3. **Recreate Clean Cluster**: ```sudo pg_createcluster 18 main --start``` 
4. **Restart the service**: ```sudo systemctl start postgresql```
5. **Run the GVM database creation script**: ```sudo runuser -u postgres -- /usr/share/gvm/create-postgresql-database```
6. **Re-initialize GVM setup**: ```sudo gvm-setup``` (Note: This might take 20–30 minutes)
7. **Verify setup again**: ```sudo gvm-check-setup``` (you should see a message like: **"It seems like your GVM-24.x.x installation is OK."**)

**Result**: The database was successfully created, and gvm-check-setup confirmed a healthy status.

## Vulnerability Scanning
