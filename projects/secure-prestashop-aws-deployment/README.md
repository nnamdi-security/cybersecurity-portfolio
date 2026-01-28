## Multi-Tier Cloud Deployment: PrestaShop on AWS

### Project Overview
This project demonstrates the deployment of a secure, scalable e-commerce platform using PrestaShop on a multi-tier AWS architecture. By separating the web server and the database, I implemented a design that follows the AWS Well-Architected Framework for security and reliability

---

### Objectives
- Deploy a publicly accessible web application on AWS
- Apply basic cloud security principles during deployment
- Separate application and database servers for seurity purpose
- Document infrastructure and security decisions clearly

---

### Architecture Overview
**Components:**
- **Application Server:** AWS EC2 instance hosting PrestaShop (Apache + PHP)
- **Database Server:** Separate AWS EC2 instance hosting MySQL
- **Networking:** AWS Security Groups used to restrict traffic

**Security Design Decisions:**
- Database server is not publicly accessible
- Database accepts connections only from the application server
- Minimal inbound ports exposed on the application server
- Deployment limited to AWS Free Tier resources

---

### Tools & Technologies
- Amazon Web Services (AWS)
  - EC2
  - Security Groups
- Linux (Ubuntu)
- Apache 
- PHP
- MySQL (Amazon RDS)
- PrestaShop (Open Source)

---

### Deployment Summary
1. Created two EC2 instances using AWS Free Tier
2. Configured security groups with restricted inbound rules
3. Installed and configured web server and PHP on the application server
4. Installed and secured MySQL/MariaDB on a separate database server
5. Deployed PrestaShop and connected it to the remote database
6. Verified public access to the application via AWS-provided URL

---

### Screenshots & Evidence
The following screenshots are included in this project:
- EC2 instance configurations
- Security group inbound/outbound rules
- PrestaShop installation and live homepage
- Database connectivity configuration

(See `/screenshots` directory)

---

## Challenges & Troubleshooting (The Cybersecurity Mindset)

Building in the cloud is rarely a straight line. Below are key technical challenges encountered during this project and how they were resolved, demonstrating a practical cybersecurity troubleshooting approach.

---

### 1. The “Permission Denied” SSH Barrier
**Challenge:**  
Windows OpenSSH rejected the `.pem` private key because file permissions were too permissive, triggering a security violation and preventing SSH access to the EC2 instance.

**Solution:**  
I used the `icacls` command in PowerShell to disable permission inheritance and restricted file access exclusively to my user account. This reinforced my understanding of **Identity and Access Management (IAM)** principles at the local operating system level.

---

### 2. The RDS “Stealth” Connection Timeout
**Challenge:**  
The web server failed to communicate with the database, returning a `MySQL Error 2002` connection timeout. Additionally, AWS CloudShell access to the database was blocked.

**Solution:**  
I identified the issue as a **Security Group misconfiguration**. I manually updated the database security group’s inbound rules to allow MySQL traffic on **Port 3306** strictly from:
- The EC2 application server’s **private IP**
- Cloud shell ip address

This exercise reinforced **Network Access Control** and the principle of **least privilege**.

---

### 3. The HTTPS/SSL “Infinite Redirect Loop”
**Challenge:**  
After installation, PrestaShop enforced HTTPS redirection, rendering the site inaccessible because no SSL certificate had been installed yet.

**Solution:**  
With the GUI unreachable, I performed a **back-end database remediation**. I connected to the RDS instance via Cloudshell and manually updated the `ps_configuration` table to set:
PS_SSL_ENABLED = 0


---

### Lessons Learned
- Practical experience with cloud infrastructure setup and access control
- Importance of network segmentation in secure deployments
- Real-world challenges of deploying and documenting production-like systems

---

### Author
**Peter Ileh**  
Cybersecurity Enthusiast | Cloud & Infrastructure Security
