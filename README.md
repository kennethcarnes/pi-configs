### Script to Set Up CI/CD Configuration and GitHub Actions Workflow for Home Assistant, Docker, and Pi-hole on Raspberry Pi

# Set up directory structure
mkdir -p ~/pi-configs/{home-assistant,docker,pi-hole}

# Navigate to your project directory
cd C:\code\pi-configs

# Create the .github/workflows directory
mkdir .github\workflows -Force

# Write the YAML content to a .gitub\workflows\ci-cd.yml file

# Proceed wit repository creation
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/kennethcarnes/pi-configs.git
git push -u origin main

### Enable SSH for Remote Access:
# Enable SSH on Raspberry Pi
sudo raspi-config
# Follow: "Interfacing Options" -> "SSH" -> Enable

# Find Raspberry Pi's IP address
hostname -I

### Connect from Windows PC using SSH:
# SSH from Windows PowerShell
ssh pi@<raspberry-pi-ip> # Replace <raspberry-pi-ip> with Pi's IP

### Prepare Raspberry Pi for Docker and Home Assistant:
# Update and upgrade Raspberry Pi OS
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -sSL https://get.docker.com | sh

# Add user to Docker group (replace 'pi' with your username)
sudo usermod -aG docker pi

# Reboot Raspberry Pi
sudo reboot

### Check for Open Ports (For Pi-hole):
# Check if ports 53, 67, 80, and 443 are in use
sudo lsof -i :53
sudo lsof -i :67
sudo lsof -i :80
sudo lsof -i :443

### Install Home Assistant and Pi-hole:
# Install Home Assistant
docker run -d --name="home-assistant" \
  -v /home/pi/homeassistant:/config \
  -e TZ=America/Chicago \
  --net=host \
  homeassistant/home-assistant:stable

# Install Pi-hole
docker run -d \
  --name="pihole" \
  -p 53:53/tcp -p 53:53/udp \
  -p 67:67/udp -p 80:80 \
  -p 443:443 \
  -v /home/pi/pihole:/etc/pihole \
  -v /home/pi/pihole/dnsmasq.d:/etc/dnsmasq.d \
  --dns=127.0.0.1 --dns=1.1.1.1 \
  --restart=unless-stopped \
  --hostname pi.hole \
  -e TZ=America/Chicago \
  -e WEBPASSWORD=YourPasswordHere \
  --cap-add=NET_ADMIN \
  pihole/pihole:latest

### Enabling Remote Desktop (RDP):
# Install and enable xrdp on Raspberry Pi
sudo apt install xrdp
sudo systemctl enable xrdp && sudo systemctl start xrdp

### Connect via Remote Desktop:
# Use Windows Remote Desktop Connection to connect to Pi's IP address
