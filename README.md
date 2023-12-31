# Installation
## 1. Set up directory structure
```bash
mkdir -p ~/pi-configs/{home-assistant,docker,pi-hole}
cd C:\code\pi-configs
mkdir .github\workflows -Force
```

## 2. Write the YAML content to a .gitub\workflows\ci-cd.yml file

## 3. Proceed with repository creation
```bash
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/kennethcarnes/pi-configs.git
git push -u origin main
```

## 4. Config SSH
```bash
sudo raspi-config
# Follow: "Interfacing Options" -> "SSH" -> Enable
```

## 5. Install Docker, Add user to Docker group, and reboot
```bash
sudo apt update && sudo apt upgrade -y
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker pi
sudo reboot
```

## 7. Install Home Assistant
```bash
docker run -d --name="home-assistant" 
  -v /home/pi/homeassistant:/config \
  -e TZ=America/Chicago \
  --net=host \
  --restart=unless-stopped \
  homeassistant/home-assistant:stable\
```

## 8. Install Pi-hole
```bash
#Verify open ports
sudo lsof -i :53
sudo lsof -i :67
sudo lsof -i :80
sudo lsof -i :443
```
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

## Verification
- ```hostname -I``` should return the ip address of the Raspberry Pi
- SSH: ```ssh pi@192.168.1.41```
- Pi-hole: ```http://192.168.1.41/admin```
- Home Assistant: ````http://192.168.1.41:8123````
- VNC: ```192.168.1.41```