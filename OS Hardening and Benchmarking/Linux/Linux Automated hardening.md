Introduction
🔒 Securing your Ubuntu system is essential to protect against unauthorized access, data breaches, and other cyber threats. The Ubuntu Security Guide (USG) provides an automated approach to align your system with industry-standard benchmarks, such as those from the Center for Internet Security (CIS).

This guide will take you through the process of enabling and utilizing USG for effective system hardening. 🧰

Step 1: Enable Ubuntu Pro
⚙️ First, you need to enable Ubuntu Pro to access the full range of features, including USG. Ubuntu Pro provides extended security maintenance and compliance tools.

To learn how to enable Ubuntu Pro on your server, follow the detailed guide here:
How to Enable Ubuntu Pro on Ubuntu Server 22.04 (Jammy Jellyfish) — Different from Desktop

Command: sudo pro enable usg
This command enables the USG service, which helps you audit and harden your system according to security benchmarks. 📊

Step 2: Install Ubuntu Advantage Tools
🔧 The ubuntu-advantage-tools package is required for managing Ubuntu Pro services, including USG.

Command: sudo apt install ubuntu-advantage-tools
This package provides the tools necessary to manage your Ubuntu Pro subscription, ensuring you can activate services like USG. 🛠️

Step 3: Enable USG Services and Install USG
🛡️ Once you have enabled USG, you need to install the USG package to begin hardening your system.

Command: sudo apt install usg
This installs the USG tool, enabling you to start auditing and applying security fixes. 🔐

Step 4: Audit the System
📊 Auditing your system is essential to identify any existing vulnerabilities and understand the security status.

Command: sudo usg audit cis_level1_server
This command will audit your system against the CIS Level 1 Server profile, highlighting areas that need attention. 📜

Step 5: View the Report
📂 After the audit, the system generates a report, which can be found in the /var/lib/usg directory.

Location: /var/lib/usg
Use this location to view the audit report, which will provide detailed information on compliance with security standards. 📝

Step 6: Apply Hardening Fixes
🔧 Once you have reviewed the report, you can apply the hardening fixes recommended by USG to address security vulnerabilities.

Command: sudo usg fix cis_level1_server
This command applies necessary changes to align your system with the CIS Level 1 Server security profile. 🛠️

Step 7: Reboot to Apply Changes
🔄 Some changes may require a system reboot to take effect.

Command: sudo reboot
Rebooting ensures that all applied configurations are properly initialized. 🔁

Step 8: Audit Again to Assess the Fixes
🔍 After rebooting, perform a second audit to assess whether the fixes were applied successfully.

Command: sudo usg audit cis_level1_server
This audit checks if the previously applied fixes resolved the identified security gaps. ✅

Step 9: Compare the Two Reports
🔎 After running the second audit, compare the new report with the initial one. This comparison allows you to track improvements and identify remaining vulnerabilities.

Tools: Use tools like diff to compare the audit reports or manually review the results.
Step 10: Review Available Profiles
⚙️ USG offers different profiles tailored to various system roles (servers, workstations, etc.). Reviewing these profiles helps you choose the most appropriate one for your system’s role.

Command: sudo usg profiles
This command will list all available profiles that you can use to harden your system. 🔧

Step 11: Analyze the Reports
📊 After applying fixes and reviewing the profiles, carefully analyze the reports to ensure your system meets the desired security standards. This analysis will help you understand the impact of the changes.

