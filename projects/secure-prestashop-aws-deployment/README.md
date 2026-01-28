## Secure Cloud Deployment of PrestaShop on AWS

### Overview
This project demonstrates the secure deployment of an open-source e-commerce application (PrestaShop) on Amazon Web Services (AWS) using Free Tier resources. The deployment follows basic cloud security and infrastructure best practices, including separation of application and database layers to reduce attack surface and improve system resilience.

The goal of this project is to showcase hands-on experience with cloud infrastructure, secure system configuration, and real-world deployment documentation from a cybersecurity perspective.

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
- MySQL 
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

### Security Considerations
- Separation of application and database reduces the impact of server compromise
- Network-level access control enforces least privilege
- No sensitive credentials are stored in this repository

---

### Lessons Learned
- Practical experience with cloud infrastructure setup and access control
- Importance of network segmentation in secure deployments
- Real-world challenges of deploying and documenting production-like systems

---

### Author
**Chizobam Ileh**  
Cybersecurity Enthusiast | Cloud & Infrastructure Security
