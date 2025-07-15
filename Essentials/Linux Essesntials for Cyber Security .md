\# 🐧 Linux Essential Commands (Beginner-Friendly)



Welcome! This file is a collection of the most commonly used \*\*Linux commands\*\* for beginners in cybersecurity. Whether you're learning how to use the terminal, working on your home lab, or starting your journey as a SOC Analyst or Pentester, these commands will help you feel more confident.



---



\## 📁 1. User and Group Management



| Command | What It Does | Example |

|--------|---------------|---------|

| `useradd` | Add a new user | `sudo useradd john` |

| `usermod` | Add user to a group | `sudo usermod -aG sudo john` |

| `userdel` | Delete a user | `sudo userdel john` |

| `groupadd` | Create a group | `sudo groupadd developers` |

| `groupdel` | Delete a group | `sudo groupdel developers` |

| `passwd` | Change password | `passwd john` |

| `who` | See who’s logged in | `who` |



---



\## 📂 2. File and Directory Management



| Command | What It Does | Example |

|--------|---------------|---------|

| `ls` | List files and folders | `ls -l /home` |

| `cd` | Change directory | `cd /var/log` |

| `pwd` | Show current location | `pwd` |

| `mkdir` | Make new folder | `mkdir projects` |

| `rm` | Delete files/folders | `rm -rf temp/` |

| `cp` | Copy files | `cp file.txt backup/` |

| `mv` | Move or rename file | `mv file.txt newname.txt` |

| `touch` | Make empty file | `touch index.html` |

| `find` | Search for files | `find /home -name "\*.log"` |



---



\## 📄 3. Viewing and Editing Files



| Command | What It Does | Example |

|--------|---------------|---------|

| `cat` | Show file contents | `cat notes.txt` |

| `less` / `more` | View long files page by page | `less file.txt` |

| `head` | First few lines | `head -n 5 file.txt` |

| `tail` | Last few lines | `tail -n 5 file.txt` |

| `nano` | Open text editor | `nano config.txt` |

| `vi` | Open advanced editor | `vi config.txt` |



---



\## 🔐 4. Permissions and Ownership



| Command | What It Does | Example |

|--------|---------------|---------|

| `chmod` | Change file permissions | `chmod 755 script.sh` |

| `chown` | Change owner | `chown john:admin file.txt` |

| `ls -l` | See file permissions | `ls -l /etc/passwd` |



---



\## 💽 5. Disk and File System



| Command | What It Does | Example |

|--------|---------------|---------|

| `df -h` | Show disk space | `df -h` |

| `du -sh` | Folder size | `du -sh /var/log` |

| `mount` / `umount` | Mount or unmount drives | `sudo mount /dev/sdb1 /mnt` |

| `lsblk` | List disks and partitions | `lsblk` |



---



\## ⚙️ 6. Running Programs (Processes)



| Command | What It Does | Example |

|--------|---------------|---------|

| `ps aux` | Show running processes | `ps aux` |

| `top` / `htop` | Real-time process viewer | `top` |

| `kill` | Stop a program | `kill 1234` |

| `jobs`, `bg`, `fg` | Background/foreground tasks | `fg %1` |



---



\## 🌐 7. Network Basics



| Command | What It Does | Example |

|--------|---------------|---------|

| `ping` | Check connection | `ping google.com` |

| `ip a` | See your IP address | `ip a` |

| `netstat` / `ss` | Show open ports | `ss -tulnp` |

| `wget` / `curl` | Download or view websites | `wget http://example.com` |

| `nmap` | Scan ports | `nmap 192.168.1.1` |



---



\## 📊 8. System Monitoring



| Command | What It Does | Example |

|--------|---------------|---------|

| `uptime` | Show system uptime | `uptime` |

| `free -m` | Show memory usage | `free -m` |

| `watch` | Repeat a command | `watch -n 2 date` |

| `journalctl` | View system logs | `journalctl -xe` |



---



\## ✂️ 9. Text Processing (Very Useful!)



| Command | What It Does | Example |

|--------|---------------|---------|

| `grep` | Search inside files | `grep 'error' logfile.txt` |

| `awk` | Print columns | `awk '{print $1, $2}' file.txt` |

| `sed` | Replace text | `sed 's/old/new/g' file.txt` |

| `cut` | Cut specific text | `cut -d':' -f1 /etc/passwd` |

| `sort` / `uniq` | Sort and remove duplicates | `sort names.txt | uniq` |

| `wc -l` | Count lines | `wc -l file.txt` |



---



\## 📦 10. Installing \& Managing Software



\### For Ubuntu/Debian:

| Command | What It Does | Example |

|--------|---------------|---------|

| `sudo apt update` | Update package list | `sudo apt update` |

| `sudo apt install` | Install software | `sudo apt install nmap` |

| `sudo apt remove` | Remove software | `sudo apt remove nano` |

| `dpkg -i` | Install `.deb` file | `sudo dpkg -i file.deb` |



\### For Red Hat/CentOS/Fedora:

| Command | What It Does | Example |

|--------|---------------|---------|

| `dnf install` | Install software | `sudo dnf install git` |

| `rpm -ivh` | Install `.rpm` file | `sudo rpm -ivh file.rpm` |



---



\## 🗜️ 11. Archive and Compression



| Command | What It Does | Example |

|--------|---------------|---------|

| `tar -czvf` | Create compressed `.tar.gz` | `tar -czvf backup.tar.gz folder/` |

| `tar -xzvf` | Extract `.tar.gz` | `tar -xzvf backup.tar.gz` |

| `zip`, `unzip` | Create/extract `.zip` | `zip file.zip file1` |



---



\## 🔁 12. Shutdown and Reboot



| Command | What It Does | Example |

|--------|---------------|---------|

| `sudo shutdown now` | Shut down immediately | `sudo shutdown -h now` |

| `sudo reboot` | Reboot system | `sudo reboot` |

| `shutdown -r +5` | Reboot in 5 minutes | `shutdown -r +5` |



---



\## 🕒 13. Scheduled Tasks



| Command | What It Does | Example |

|--------|---------------|---------|

| `crontab -e` | Edit scheduled jobs | `crontab -e` |

| `at` | Run a command later | `echo "reboot" | at now + 1 min` |



---



\## 🛠️ 14. System Troubleshooting



| Command | What It Does | Example |

|--------|---------------|---------|

| `dmesg` | Kernel and boot logs | `dmesg | grep error` |

| `journalctl` | System logs | `journalctl -f` |

| `top` / `htop` | Check what’s slowing system | `htop` |

| `free` | Check memory | `free -m` |

| `vmstat`, `iostat` | Performance stats | `vmstat 1 5` |



---



\## 💡 Tips for Beginners



\- Use `man <command>` to learn more.  

&nbsp; ➤ Example: `man grep`  

\- Use `--help` with any command to get quick help.  

&nbsp; ➤ Example: `ls --help`  

\- Practice slowly, and don’t copy-paste everything. Try to understand what each command does.



---



\## 📌 Final Note



This list is \*\*not exhaustive\*\*, but it's enough to get started. Focus on using these commands during your daily practice, CTFs, and labs.



\*\*✅ Learn by doing. Make mistakes. Break things. Fix them. That’s how real cybersecurity skills grow.\*\*



---





