<p align="center">
<img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo"/>
</p>

# osTicket - Prerequisites and Installation

A walkthrough of the prerequisites and steps required to install the open-source help desk ticketing platform, **osTicket**, on a Windows Server environment hosted in Microsoft Azure.

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines / Compute)
- Remote Desktop Protocol (RDP)
- Internet Information Services (IIS)

## Operating System

- Windows 10 (21H2)

## Prerequisites Checklist

- Provision a Windows 10 virtual machine in Azure
- Enable IIS with CGI and Common HTTP Features
- Download and install the supporting files required by osTicket
- Register PHP inside IIS
- Install and configure osTicket itself

## Installation Steps

### 1. Create the Azure Virtual Machine

<p align="center">
<img src="https://i.imgur.com/R0j8B3U.png" width="80%" alt="Azure VM creation"/>
</p>

Create a resource group and a Windows 10 virtual machine (2-4 vCPUs), letting Azure provision a new virtual network for it. Name the VM something identifiable (e.g. vm-osticket) and set a username and password - keep these somewhere safe, such as a password manager or a local note, since you'll need them to connect. Once the VM is running, connect to it over Remote Desktop.

### 2. Enable IIS and Required Windows Features

<p align="center">
<img src="https://i.imgur.com/3tekzq9.png" width="80%" alt="Windows Features dialog"/>
</p>

From inside the VM, open Control Panel -> Programs -> Turn Windows features on or off and enable:

- Internet Information Services
  - Web Management Tools -> IIS Management Console
  - World Wide Web Services -> Application Development Features -> CGI
  - World Wide Web Services -> Common HTTP Features

### 3. Install the Supporting Files

<p align="center">
<img src="https://i.imgur.com/L1KIV7C.png" width="80%" alt="Supporting files installed"/>
</p>

With IIS enabled, install the components osTicket depends on:

- PHP Manager for IIS (e.g. PHPManagerForIIS_V1.5.0.msi)
- URL Rewrite Module (e.g. rewrite_amd64_en-US.msi)
- PHP itself - create a C:\PHP directory and extract a compatible PHP release (e.g. PHP 7.3.8 non-thread-safe, Win32, VC15 x86 build) into it
- Visual C++ Redistributable (VC_redist.x86.exe)
- MySQL (e.g. MySQL 5.5.62) - during setup choose Typical Setup, then run the configuration wizard with Standard Configuration and set a root password

### 4. Register PHP with IIS

<p align="center">
<img src="https://i.imgur.com/E5WKcPk.png" width="80%" alt="PHP Manager registration"/>
</p>

Launch IIS Manager as Administrator, open PHP Manager, and choose Register new PHP version, pointing it at the C:\PHP folder. Restart the IIS service once registration completes.

### 5. Install osTicket

<p align="center">
<img src="https://i.imgur.com/eqFSUiY.png" width="80%" alt="osTicket installation"/>
</p>

1. Download the desired osTicket release and extract the upload folder.
2. Copy it into C:\inetpub\wwwroot and rename it to osTicket.
3. Restart IIS (stop and start the site).
4. In IIS, go to Sites -> Default -> osTicket and click "Browse *:80" - some PHP extensions won't be enabled yet.
5. Back in IIS, open PHP Manager -> Enable or disable an extension and enable:
   - php_imap.dll
   - php_intl.dll
   - php_opcache.dll
6. Refresh the site in your browser to confirm the changes took effect.
7. Rename the sample config file:
   C:\inetpub\wwwroot\osTicket\include\ost-sampleconfig.php -> ost-config.php
8. Update permissions on ost-config.php: disable inheritance, remove all existing permissions, then grant Everyone -> Full Control.
9. Continue the setup wizard in the browser:
   - Set a help desk name and default support email address.
10. Install HeidiSQL, connect to your local MySQL instance (root / your configured password), and create a new database (e.g. osTicket).
11. Back in the browser setup wizard, enter the database connection details:
    - MySQL Database: osTicket
    - MySQL Username: root
    - MySQL Password: (your configured password)
12. Click "Install Now!" to finish the installation.

---

*This guide documents a personal lab exercise for standing up osTicket on Azure/IIS for learning purposes.*
