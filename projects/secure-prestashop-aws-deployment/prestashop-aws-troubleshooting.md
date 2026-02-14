# PrestaShop AWS Deployment Troubleshooting Guide

## Project Overview

**Project:** PrestaShop E-commerce Platform on AWS  
**Architecture:** Two-tier cloud architecture (Separation of Concerns)  
**Infrastructure:**
- **Web Server:** EC2 Instance (Ubuntu 24.04, Apache/PHP)
- **Database:** Amazon RDS (MySQL/MariaDB)
- **Region:** EU-North-1 (Stockholm)
- **Use Case:** Cybersecurity Lab Environment

---

## Problem Statement

After successfully deploying a functional PrestaShop instance and accessing it via the initial public IP (`http://13.48.56.102/`), the application became inaccessible following an EC2 instance restart. The new public IP (`http://16.171.140.92/`) could not be reached, and ping requests from external machines failed.

### Initial Symptoms
- ❌ Unable to access application via new public IP
- ❌ Cannot ping EC2 instance from external machine
- ❌ Previous public IP no longer works
- ✅ EC2 instance showing as "Running" in AWS Console

---

## Root Cause Analysis

### Issue 1: Dynamic Public IP Assignment
**Root Cause:** AWS EC2 instances receive a new public IP address each time they are stopped and restarted (unless an Elastic IP is allocated).

**Impact:** PrestaShop was configured with the old IP address, causing redirect loops and connection failures.

### Issue 2: Application Hardcoded IP Configuration
**Root Cause:** PrestaShop stores the base URL in its database. When accessed via the new IP, the application redirected to the old IP address.

**Evidence:**
```bash
$ curl -v http://16.171.140.92/
< HTTP/1.0 302 Found
< Location: http://13.48.56.102/index.php?
```

### Issue 3: Database Connection Failure (Error 500)
**Root Cause:** After updating the application URL, the application returned HTTP 500 errors due to database authentication failures.

**Evidence from logs:**
```
PHP Fatal error: Uncaught PrestaShopException: Link to database cannot be established: 
SQLSTATE[HY000] [1045] Access denied for user 'nnamdi_security'@'172.31.39.41' (using password: YES)
```

**Underlying Cause:** RDS security group was not properly configured to allow connections from the EC2 instance's private IP.

---

## Troubleshooting Methodology

### Phase 1: Connectivity Verification

#### Step 1.1: Verify EC2 Instance State
```bash
# AWS Console: EC2 → Instances → Check instance state
Status: Running ✓
```

#### Step 1.2: Test Basic Connectivity
```bash
# From local machine
$ ping 16.171.140.92
# Result: Request timeout (Expected - ICMP disabled by default in AWS)
```

#### Step 1.3: Check Security Group Rules
**Location:** EC2 → Instances → Security Tab → Security Groups

**Required Inbound Rules:**
| Type | Protocol | Port | Source |
|------|----------|------|--------|
| HTTP | TCP | 80 | 0.0.0.0/0 |
| SSH | TCP | 22 | My IP |
| ICMP (Optional) | ICMP | - | 0.0.0.0/0 |

**Status:** ✓ All rules present and correct

#### Step 1.4: Test HTTP Response from EC2
```bash
# SSH into EC2 instance
$ ssh ubuntu@16.171.140.92

# Test local web server
$ curl -v http://16.171.140.92/
```

**Result:** HTTP 302 redirect to old IP address detected

---

### Phase 2: Application Configuration Fix

#### Step 2.1: Identify Redirect Issue
```bash
$ curl -v http://16.171.140.92/
*   Trying 16.171.140.92:80...
* Connected to 16.171.140.92 (16.171.140.92) port 80 (#0)
> GET / HTTP/1.1
> Host: 16.171.140.92
> User-Agent: curl/7.81.0
> Accept: */*
> 
< HTTP/1.0 302 Found
< Location: http://13.48.56.102/index.php?
```

**Diagnosis:** PrestaShop database contains old IP address

#### Step 2.2: Update PrestaShop Database Configuration

```bash
# Connect to RDS database
$ mysql -h <rds-endpoint>.eu-north-1.rds.amazonaws.com -u nnamdi_security -p

# Display current configuration
mysql> USE prestashop;
mysql> SELECT * FROM ps_shop_url;

# Update shop URL to new IP
mysql> UPDATE ps_shop_url 
       SET domain = '16.171.140.92', 
           domain_ssl = '16.171.140.92' 
       WHERE id_shop = 1;

# Update configuration settings
mysql> UPDATE ps_configuration 
       SET value = 'http://16.171.140.92/' 
       WHERE name = 'PS_SHOP_DOMAIN';

mysql> UPDATE ps_configuration 
       SET value = 'http://16.171.140.92/' 
       WHERE name = 'PS_SHOP_DOMAIN_SSL';

mysql> EXIT;
```

#### Step 2.3: Clear Application Cache
```bash
# Clear PrestaShop cache
$ sudo rm -rf /var/www/html/var/cache/*

# Restart Apache
$ sudo systemctl restart apache2
```

#### Step 2.4: Verify Fix
```bash
$ curl -v http://16.171.140.92/
# Expected: HTTP 302 redirect to http://16.171.140.92/index.php (same IP)
```

**Result:** ✓ Redirect now points to correct IP, but HTTP 500 error encountered

---

### Phase 3: Database Connection Troubleshooting

#### Step 3.1: Analyze Error Logs
```bash
$ sudo tail -50 /var/log/apache2/error.log
```

**Critical Error Found:**
```
[php:error] PHP Fatal error: Uncaught PrestaShopException: 
Link to database cannot be established: SQLSTATE[HY000] [1045] 
Access denied for user 'nnamdi_security'@'172.31.39.41' (using password: YES)
```

**Diagnosis:** Database connection failure from EC2 private IP `172.31.39.41`

#### Step 3.2: Test Database Connectivity
```bash
# Test direct connection from EC2 to RDS
$ mysql -h <rds-endpoint>.rds.amazonaws.com -u nnamdi_security -p
```

**Result:** ✓ Connection successful

**Conclusion:** Database credentials are correct, but application cannot connect.

#### Step 3.3: Verify RDS Security Group Configuration

**Location:** RDS → Databases → Connectivity & Security → VPC Security Groups

**Required Inbound Rule:**
| Type | Protocol | Port | Source |
|------|----------|------|--------|
| MySQL/Aurora | TCP | 3306 | EC2 Security Group ID (sg-xxxxx) |

**OR**

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| MySQL/Aurora | TCP | 3306 | 172.31.39.41/32 (EC2 Private IP) |

**Issue Found:** Missing or misconfigured inbound rule

**Fix Applied:**
1. Navigate to RDS Security Group
2. Edit Inbound Rules
3. Add rule: Type=MySQL/Aurora, Port=3306, Source=EC2-Security-Group-ID
4. Save rules

#### Step 3.4: Verify PrestaShop Database Configuration

```bash
$ sudo cat /var/www/html/app/config/parameters.php | grep -A 10 database
```

**Required Configuration:**
```php
'database_host' => '<rds-endpoint>.eu-north-1.rds.amazonaws.com',  // Must be RDS endpoint
'database_port' => '3306',
'database_name' => 'prestashop',
'database_user' => 'nnamdi_security',
'database_password' => 'correct_password',
'database_prefix' => 'ps_',
```

**Common Mistakes to Avoid:**
- ❌ Using `localhost` instead of RDS endpoint
- ❌ Using EC2 private/public IP instead of RDS endpoint
- ❌ Incorrect database name
- ❌ Wrong password

#### Step 3.5: Restart Services
```bash
$ sudo systemctl restart apache2
```

#### Step 3.6: Final Verification
```bash
# Test from browser
http://16.171.140.92/
```

**Result:** ✓ PrestaShop loads successfully

---

## Permanent Solution: Elastic IP Implementation

### Problem with Dynamic IPs
Every time an EC2 instance stops and restarts, AWS assigns a new public IP, requiring manual database updates.

### Solution: Elastic IP Address

#### Benefits
- ✅ Static public IP that persists across instance restarts
- ✅ No need to update PrestaShop configuration repeatedly
- ✅ Free while associated with a running instance
- ✅ Professional and reliable access point

#### Implementation Steps

**Step 1: Allocate Elastic IP**
```
AWS Console → EC2 → Elastic IPs → Allocate Elastic IP address
```

**Step 2: Associate with EC2 Instance**
```
Select Elastic IP → Actions → Associate Elastic IP address
- Resource type: Instance
- Instance: i-0d571c588911a18cd (prestashop-web-server)
- Private IP: Leave empty (uses primary private IP)
→ Click "Associate"
```

**New Elastic IP Assigned:** `16.16.47.86`

**Step 3: Update PrestaShop Configuration (Final Time)**
```bash
# Connect to database
$ mysql -h <rds-endpoint>.rds.amazonaws.com -u nnamdi_security -p

# Update to Elastic IP
mysql> USE prestashop;
mysql> UPDATE ps_shop_url 
       SET domain = '16.16.47.86', 
           domain_ssl = '16.16.47.86' 
       WHERE id_shop = 1;

mysql> UPDATE ps_configuration 
       SET value = 'http://16.16.47.86/' 
       WHERE name = 'PS_SHOP_DOMAIN';

mysql> UPDATE ps_configuration 
       SET value = 'http://16.16.47.86/' 
       WHERE name = 'PS_SHOP_DOMAIN_SSL';

mysql> EXIT;

# Clear cache and restart
$ sudo rm -rf /var/www/html/var/cache/*
$ sudo systemctl restart apache2
```

**Step 4: Verify Access**
```
Browser: http://16.16.47.86/
```

**Result:** ✓ PrestaShop accessible via permanent IP address

---

## Cost Management for Lab Environment

### Recommendation: Shut Down When Not in Use

Since this is a cybersecurity lab environment, the infrastructure should be stopped when not actively used.

### Benefits
- 💰 **Cost Savings:** EC2 and RDS charge by the hour
- 🔒 **Enhanced Security:** Reduced attack surface when offline
- 📊 **Resource Management:** Conserves AWS Free Tier limits

### Proper Shutdown Procedure

**Before Stopping:**
```bash
# SSH into instance
$ ssh ubuntu@16.16.47.86

# Stop services gracefully
$ sudo systemctl stop apache2

# Exit
$ exit
```

**Stop EC2 Instance:**
```
AWS Console → EC2 → Instances → Select instance → Instance State → Stop
```

**Stop RDS Database (Optional):**
```
AWS Console → RDS → Databases → Select database → Actions → Stop temporarily
Note: RDS auto-restarts after 7 days
```

### Startup Procedure

**Start EC2 Instance:**
```
AWS Console → EC2 → Instances → Select instance → Instance State → Start
```

**Start RDS Database:**
```
AWS Console → RDS → Databases → Select database → Actions → Start
```

**Wait 1-2 minutes, then access:**
```
http://16.16.47.86/
```

### Important Note: Elastic IP Charges

- ✅ **FREE** when associated with a running instance
- ⚠️ **$0.005/hour (~$3.60/month)** when instance is stopped but Elastic IP remains allocated
- This small cost is acceptable for maintaining a permanent IP address

---

## Complete Network Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Internet                              │
└────────────────────────┬────────────────────────────────────┘
                         │
                         │ HTTP (Port 80)
                         │
                    ┌────▼────┐
                    │ Elastic IP │
                    │ 16.16.47.86│
                    └────┬────┘
                         │
        ┌────────────────▼─────────────────┐
        │   EC2 Security Group             │
        │   Inbound:                       │
        │   - HTTP (80): 0.0.0.0/0        │
        │   - SSH (22): My IP             │
        └────────────┬─────────────────────┘
                     │
        ┌────────────▼──────────────────────┐
        │   EC2 Instance (Web Server)       │
        │   - Public IP: 16.16.47.86        │
        │   - Private IP: 172.31.39.41      │
        │   - Apache + PHP + PrestaShop     │
        └────────────┬──────────────────────┘
                     │
                     │ MySQL (Port 3306)
                     │
        ┌────────────▼──────────────────────┐
        │   RDS Security Group              │
        │   Inbound:                        │
        │   - MySQL (3306): EC2-SG          │
        └────────────┬──────────────────────┘
                     │
        ┌────────────▼──────────────────────┐
        │   RDS Database Instance           │
        │   - Database: prestashop          │
        │   - User: nnamdi_security         │
        │   - Engine: MySQL/MariaDB         │
        └───────────────────────────────────┘
```

---

## Key Lessons Learned

### 1. AWS Networking
- EC2 instances receive dynamic public IPs by default
- Elastic IPs provide static public addressing
- ICMP (ping) is blocked by default in AWS security groups
- Private IPs remain constant, public IPs change on restart

### 2. Security Group Configuration
- EC2 security groups control inbound/outbound traffic to instances
- RDS security groups must explicitly allow database connections
- Best practice: Reference security groups by ID rather than IP addresses

### 3. Application Configuration
- Web applications often hardcode URLs in databases
- Always update application configuration after IP changes
- Cache must be cleared after configuration changes

### 4. Database Connectivity
- RDS endpoints remain constant (don't change on restart)
- Always use RDS endpoint, never localhost or IP addresses
- Test database connectivity separately from application connectivity

### 5. Troubleshooting Methodology
- Check infrastructure first (instance state, networking)
- Verify connectivity at each layer (HTTP, database)
- Examine logs for detailed error information
- Test components in isolation

---

## Quick Reference Commands

### Connection & Testing
```bash
# SSH into EC2
ssh ubuntu@16.16.47.86

# Test web server locally
curl -v http://localhost/

# Test web server externally
curl -v http://16.16.47.86/

# Connect to RDS database
mysql -h <rds-endpoint>.rds.amazonaws.com -u nnamdi_security -p

# Test database connectivity
mysql -h <rds-endpoint>.rds.amazonaws.com -u nnamdi_security -p -e "SHOW DATABASES;"
```

### Log Analysis
```bash
# Apache error logs
sudo tail -f /var/log/apache2/error.log

# Apache access logs
sudo tail -f /var/log/apache2/access.log

# System logs
sudo journalctl -xe
```

### Service Management
```bash
# Check Apache status
sudo systemctl status apache2

# Restart Apache
sudo systemctl restart apache2

# Clear PrestaShop cache
sudo rm -rf /var/www/html/var/cache/*
```

### Database Updates
```sql
-- Update shop URL
USE prestashop;
UPDATE ps_shop_url SET domain = 'NEW_IP', domain_ssl = 'NEW_IP' WHERE id_shop = 1;
UPDATE ps_configuration SET value = 'http://NEW_IP/' WHERE name = 'PS_SHOP_DOMAIN';
UPDATE ps_configuration SET value = 'http://NEW_IP/' WHERE name = 'PS_SHOP_DOMAIN_SSL';
```

---

## Security Best Practices

### ✅ Implemented
- Two-tier architecture (separation of web and database)
- RDS database isolated in separate security group
- Security groups configured with least privilege principle
- SSH access restricted to specific IP addresses

### 🔄 Recommended for Production
- Implement SSL/TLS certificates (Let's Encrypt)
- Use custom domain name instead of IP address
- Enable AWS CloudTrail for audit logging
- Implement AWS CloudWatch monitoring and alerts
- Regular automated backups (RDS snapshots, AMI creation)
- Multi-AZ deployment for high availability
- Web Application Firewall (AWS WAF)
- Regular security updates and patching

---

## Troubleshooting Flowchart

```
Start: Cannot Access PrestaShop
    ↓
Is EC2 instance running?
    No → Start instance → Wait 2 minutes
    Yes ↓
    ↓
Can you ping the IP?
    No → Check Security Group (HTTP port 80)
    Yes ↓ (Note: Ping might be disabled by design)
    ↓
Does curl from EC2 work?
    No → Check Apache: systemctl status apache2
    Yes ↓
    ↓
What HTTP response code?
    302 → Check redirect location
        ↓ Redirects to old IP?
        Yes → Update ps_shop_url in database
    500 → Check error logs
        ↓ Database connection error?
        Yes → Check RDS security group
              → Verify database config in parameters.php
    200 → Application working ✓
```

---

## Conclusion

This troubleshooting experience demonstrates the importance of understanding cloud infrastructure fundamentals, proper security group configuration, and application-level configuration management. The implementation of an Elastic IP provides a permanent solution to the dynamic IP address challenge, making the deployment more reliable and maintainable.

**Final Infrastructure Status:**
- ✅ PrestaShop accessible via Elastic IP: `http://16.16.47.86/`
- ✅ Two-tier architecture maintained
- ✅ Proper security group segmentation
- ✅ Database connectivity verified
- ✅ Cost-optimized for lab environment (shutdown capability)

---

## Additional Resources

- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS RDS Documentation](https://docs.aws.amazon.com/rds/)
- [PrestaShop Documentation](https://devdocs.prestashop.com/)
- [AWS Security Groups Best Practices](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)

---

**Author:** Nnamdi Ileh  
**Date:** February 10, 2026  
**Environment:** AWS EU-North-1 (Stockholm)  
**Purpose:** Cybersecurity Lab
