\# 🧰 Pentesting Essentials (Beginner-Friendly Guide)



Welcome to the `essentials/` folder!



This is your go-to collection of \*\*basic Linux and DevOps commands\*\* made easy for \*\*beginner penetration testers\*\*. Whether you're using Kali, Parrot OS, or a lab VM — these commands will help you work faster and smarter.



> 💡 Think of this like your cheat sheet while doing CTFs, labs, or real-world practice.



---



\## 🌟 What You’ll Learn



You’ll find simple explanations and ready-to-copy commands for things like:



\- 📁 Navigating the Linux file system  

\- 🕵️‍♂️ Finding hidden files and permissions  

\- 🧪 Running and managing background tasks  

\- 🌐 Checking network connections  

\- 🔒 Working with users and privileges  

\- 📦 Installing tools  

\- 🧱 Using Docker for vulnerable labs  

\- 🔧 Automating tasks with Ansible or scripts



---



\## 📚 Sections



1\. \[Basic File Commands](#1-basic-file-commands)  

2\. \[View \& Edit Files](#2-view--edit-files)  

3\. \[Processes \& Jobs](#3-processes--jobs)  

4\. \[Disks \& Permissions](#4-disks--permissions)  

5\. \[Networking for Pentesters](#5-networking-for-pentesters)  

6\. \[User \& Access Control](#6-user--access-control)  

7\. \[System Info](#7-system-info)  

8\. \[Compression \& Transfers](#8-compression--transfers)  

9\. \[Installing Tools](#9-installing-tools)  

10\. \[Services](#10-services)  

11\. \[Task Scheduling](#11-task-scheduling)  

12\. \[Backup \& Restore (Optional)](#12-backup--restore-optional)  

13\. \[Docker for Labs](#13-docker-for-labs)  

14\. \[Automation (Optional)](#14-automation-optional)



---



\## 1. Basic File Commands

```bash

ls           # List files and folders

cd           # Change directory

pwd          # Show current path

mkdir test   # Make a new folder

rm file.txt  # Delete a file

cp file.txt /tmp/  # Copy file

mv old new   # Rename or move a file

```



\## 2. View \& Edit Files

```bash

cat file.txt         # Show file content

less file.txt        # View file with scroll

nano file.txt        # Easy file editor

grep "root" /etc/passwd  # Find 'root' in a file

head file.txt        # First 10 lines

tail file.txt        # Last 10 lines

```



\## 3. Processes \& Jobs

```bash

ps aux               # List all processes

top / htop           # Live process viewer

kill 1234            # Kill process by ID

jobs                 # Background tasks

fg / bg              # Foreground / Background

```



\## 4. Disks \& Permissions

```bash

df -h                # Disk space

du -sh \*             # Folder sizes

chmod +x script.sh   # Make executable

chown user file      # Change owner

stat file.txt        # File metadata

```



\## 5. Networking for Pentesters

```bash

ip a                     # Show IPs

ping 10.10.10.10         # Check host

ss -tuln                 # Show ports

nmap -sV target          # Port scan

curl http://site         # Fetch page

scp file.txt user@host:/tmp/  # Copy to remote

ssh user@host            # Login remotely

```



\## 6. User \& Access Control

```bash

whoami               # Current user

id                   # Show UID \& groups

sudo -l              # Sudo rights

passwd               # Change password

su user              # Switch user

```



\## 7. System Info

```bash

uname -a             # OS info

hostname             # Hostname

uptime               # System up time

free -h              # RAM usage

lscpu / lshw         # CPU or hardware info

```



\## 8. Compression \& Transfers

```bash

tar -czf archive.tar.gz folder/   # Compress

tar -xzf archive.tar.gz           # Extract

zip -r archive.zip folder/        # Zip

unzip archive.zip                 # Unzip

rsync -av folder/ remote:/path/   # Sync folders

```



\## 9. Installing Tools

```bash

\# Debian/Ubuntu

sudo apt update \&\& sudo apt install tool



\# Red Hat/CentOS

sudo yum install tool

```



\## 10. Services

```bash

systemctl start nginx       # Start service

systemctl stop nginx        # Stop service

systemctl status nginx      # Check status

```



\## 11. Task Scheduling

```bash

crontab -e                 # Edit cron jobs

at 12:00                   # Run something at noon

sleep 5s                   # Wait 5 seconds

```



\## 12. Backup \& Restore (Optional)

```bash

rsync -avz source/ dest/   # Sync backup

scp file user@host:/dest/  # Remote copy

dd if=/dev/sda of=backup.img  # Backup disk

```



\## 13. Docker for Labs

```bash

docker pull vuln-image         # Get image

docker run -it vuln-image      # Start container

docker ps -a                   # See containers

docker exec -it id bash        # Shell inside container

docker-compose up              # Start multi-container lab

```



\## 14. Automation (Optional)

```bash

ansible all -m ping            # Ping all hosts

terraform init                 # Init cloud infra

git clone https://github.com   # Clone tool repo

```



---



🌟 \*\*More coming soon!\*\* This list will keep evolving as we explore more tools like `kubectl`, `k3s`, `zmap`, `BloodHound`, etc.



Happy Hacking! 

```



Let me know if you'd like to split this into sections or link it with Notion or GitHub. Ready to assist further!



