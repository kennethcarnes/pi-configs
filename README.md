# 1. Set up directory structure
```bash
mkdir -p ~/pi-configs/{home-assistant,docker,pi-hole}
cd C:\code\pi-configs
mkdir .github\workflows -Force
```

# 2. Write the YAML content to a .gitub\workflows\ci-cd.yml file

# 3. Proceed with repository creation
```bash
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/kennethcarnes/pi-configs.git
git push -u origin main
```

# 6. Connect to Raspberry Pi via SSH & update
```bash
sudo raspi-config
# Follow: "Interfacing Options" -> "SSH" -> Enable
hostname -I
ssh pi@<raspberry-pi-ip>
sudo apt update && sudo apt upgrade -y
```

# 6.Install Docker, Add user to Docker group, and reboot
```bash
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker pi
sudo reboot
```

# 13. Check for Open Ports (For Pi-hole):
```bash
sudo lsof -i :53
sudo lsof -i :67
sudo lsof -i :80
sudo lsof -i :443
```

# 14. Install Home Assistant
```bash
docker run -d --name="home-assistant" \
  -v /home/pi/homeassistant:/config \
  -e TZ=America/Chicago \
  --net=host \
  homeassistant/home-assistant:stable
```

# 15. Install Pi-hole
```bash
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
```

# 15. Install and enable xrdp on Raspberry Pi
```bash
sudo apt install xrdp
sudo systemctl enable xrdp && sudo systemctl start xrdp
```