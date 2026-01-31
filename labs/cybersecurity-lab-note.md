# Cybersecurity Lab Setup: Kali Linux & Cowrie Honeypot
This repository documents the technical setup of a local cybersecurity laboratory environment. The lab consists of an Attacker Machine (Kali Linux) and a Victim/Honeypot Machine (Cowrie on Debian 12), both running on VMware Workstation Pro. It also documents key challenges encountered and how they were resolved.
## 1. Attacker Environment: Kali Linux
### Installation Process
1. **Installing VMware Workstation Pro (Host Setup)**

**Steps**
- Visit the Broadcom Support Portal.
- Create an account and log in.
- Navigate to My Downloads.
- Search for VMware Workstation.
- Select the latest version for Windows.
- Click the release date, complete the additional checks, and download the installer.
- Install VMware Workstation Pro on the host system.

2. **VM Deployment**: Downloaded the pre-configured Kali VMware Image (.7z).
- Extract the archive to a folder on your system.
- Use the "Open a Virtual Machine" feature in VMware to import the .vmx file.

### Critical Troubleshooting: The "Invisible Cursor" Bug
**Issue**: Upon launching Kali, the cursor became "stuck" in a transparent state. It was visible when "ungrabbed" (Ctrl+Alt) but disappeared immediately upon clicking into the VM.

**Attempted Fixes (Failed):**
- Disabling **Accelerate 3D Graphics**.
- Forcing software cursors via ```etc/environment (KWIN_FORCE_SW_CURSOR=1)```.
- Reinstalling ```open-vm-tools-desktop``` (Failed due to missing ```xserver-xorg-video-vmware``` package candidates).
- Fresh installation via ISO instead of pre-configured image.

**The Solution**: The issue was caused by outdated hardware compatibility in the pre-configured image.
**Fix**: Right-click VM > Manage > Change Hardware Compatibility.

**Action**: Upgraded the compatibility version to 16.x.

**Result**: Cursor visibility and mouse integration were restored immediately.

2. ## Victim Environment: Cowrie Honeypot
**Server Setup**
- OS: Debian 12 "netinst" ISO (amd64).
- Downloaded from the official Debian site: ```https://www.debian.org/distrib/netinst```

**Installation Steps**
- Create a new VM in VMware Workstation.
- Attach the Debian netinst ISO.
- Configure minimal hardware resources.
- Start the VM and use the **Graphical Installer.**
- 
**Install only:**
- Standard system utilities
- (No desktop environment)

**Cowrie Installation & Configuration**

1. Install Required Dependencies:

      ```sudo apt update && sudo apt install git python3-venv libssl-dev libffi-dev build-essential authbind -y```
      
   **Note**: ```sudo apt``` command may not work so you need to install it before you can use it by logging in as root user and using ```apt install sudo -y```
   
   If your Debian VM cannot reach the internet for the installalation, change the VM's **network adapter** setting to **NAT**.
   
2. Clone Cowrie and Set Up Virtual Environment:
   
   ```git clone http://github.com/cowrie/cowrie```
   
   ```cd cowrie```
   
   ```python3 -m venv cowrie-env```
   
   ```source cowrie-env/bin/activate```
   
   ```pip install --upgrade pip```
   
   ```pip install -r requirements.txt```

3. Configure Cowrie:

   ```cp etc/cowrie.cfg.dist etc/cowrie.cfg```

### Critical Troubleshooting: "No such file or directory" on Startup
**Issue**: Attempting to run ```bin/cowrie start``` resulted in a "file not found" error, despite being in the correct directory.

**The Solution**: Modern Cowrie versions no longer ship with a prebuilt bin/cowrie script by default. The Cowrie package needed to be explicitly installed into the virtual environment's path to register the binaries correctly.

**Fix:** ```pip install -e .```. This registers Cowrie as a Python package and creates the cowrie executable inside the virtual environment.

**Verification**: Used ```which cowrie``` to confirm the path.

**Launch**: Successfully initialized using ```cowrie start``` and verified with ```cowrie status```.

## Tools & Technologies Used
- **Hypervisor**: VMware Workstation-full-25H2
- **Attacker OS**: Kali Linux 2025.4
- **Victim OS**: Debian 12 (Bookworm)
- **Honeypot**: Cowrie (SSH/Telnet)
- **Networking**: VMware Host-Only Adapter (Internal Lab Network)

## Outcome
- Kali Linux configured as the attack platform
- Debian server running Cowrie SSH honeypot
- Major issues resolved through:
   - VMware hardware compatibility tuning
   - Understanding modern Cowrie deployment workflow
- Lab is now suitable for:
   - Log analysis
   - SSH attack simulation
   - Honeypot research and learning
   - Running **Nmap** scans from Kali to detect the Cowrie SSH service.
   - Initiating brute-force attacks to populate Cowrie logs.
   - Capturing and analyzing the traffic using **Wireshark.**
   






