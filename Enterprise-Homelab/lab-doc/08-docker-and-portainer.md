08 - Docker & Portainer Installation (Ubuntu 22.04 Server VM)
Objective
This guide will walk you through installing and configuring Docker CE and Portainer CE on an Ubuntu 22.04 Server VM. This setup will give you a powerful platform for container-based deployments and an easy-to-use web UI for managing your Docker environment.

Prerequisites
An Ubuntu 22.04 Server VM is already installed.

The VM has a working internet connection and an IP address from your network (e.g., 10.10.10.50/24).

You have a user with sudo privileges.

1. Update Your System
First, make sure your system's package list is up to date and all packages are upgraded.

Bash

sudo apt update && sudo apt upgrade -y
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
2. Install Docker CE
To install Docker, you'll need to add its official GPG key and repository to your system.

Bash

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update and install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

# Enable and start Docker
sudo systemctl enable docker --now
Verify the installation:

Bash

docker --version
3. Add Your User to the Docker Group
By default, you need to use sudo to run Docker commands. To run them without sudo, add your user to the docker group.

Bash

sudo usermod -aG docker $USER
newgrp docker
4. Install Docker Compose (Plugin)
Docker Compose is a tool that allows you to define and run multi-container applications.

Bash

sudo apt install -y docker-compose-plugin
docker compose version
5. Install Portainer
Portainer is a simple web UI for managing Docker. You will run it as a Docker container.

Bash

# Create a volume for Portainer's persistent data
docker volume create portainer_data

# Run the Portainer CE container
docker run -d \
  -p 8000:8000 \
  -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
6. Access Portainer
Now you can access the Portainer web interface from your browser.

Open your browser and navigate to https://<VM-IP>:9443 (e.g., https://10.10.10.50:9443).

You will likely see an SSL warning because Portainer uses a self-signed certificate. You can safely accept this warning and proceed.

On your first login, you will be prompted to create a new admin password.

Once logged in, connect to your local Docker environment.

7. Verification Checklist
You can run the following commands to confirm everything is working correctly:

Docker is running: systemctl status docker

User can run Docker without sudo: docker ps

Docker Compose is installed: docker compose version

Portainer container is running: docker ps

Portainer Web UI is accessible: Go to https://10.10.10.50:9443 in your browser.

8. Security Notes
Change the default password: Make sure you change the admin password in Portainer immediately after the initial setup.

Restrict access: If your VM is exposed to the internet, consider using firewall rules to restrict access to the Portainer web UI.

Keep images up to date: Regularly update your Docker and Portainer images to get the latest features and security fixes.

Bash

# Update Portainer
docker pull portainer/portainer-ce:latest
docker stop portainer
docker rm portainer
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
