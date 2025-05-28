In diesem git Projekt (https://github.com/tbet/openhab-s3/tree/main) wird der Hauptstromzähler via Optokopf (USB), die Nebenstromzähler via Shelly Pro 3EM (LAN) und der Goodwe Wechselrichter GW10K-ET via ModBus (LAN) von einem Raspberrp Pi Rechner (Linux) mit Openhab mit RRD4J Persistence im Docker Container ausgelesen.

# Openhab als Docker Container auf dem Raspi
https://github.com/openhab/openhab-docker
<pre> ```docker-compose.yml
version: "3.3"
services:
  openhab:
    image: "openhab/openhab:4.3.5"
    restart: always
    network_mode: host
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "/etc/timezone:/etc/timezone:ro"
      - "/home/pi/git/openhab/conf:/openhab/conf"
      - "/home/pi/git/openhab/userdata:/openhab/userdata"
      - "/home/pi/git/openhab/addons:/openhab/addons"
    environment:
      CRYPTO_POLICY: "unlimited"
      EXTRA_JAVA_OPTS: "-Duser.timezone=Europe/Berlin"
      OPENHAB_HTTP_PORT: "8080"
      OPENHAB_HTTPS_PORT: "8443"
``` </pre>
docker-compose up -d

# Installation, Start und Check von Mosquitto (MQTT <> Shelly Geräte) auf dem Raspi
<pre> ```bash
sudo apt install -y mosquitto mosquitto-clients
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
sudo systemctl status mosquitto
journalctl -u mosquitto -n 50 --no-pagercd 
netstat -tuln | grep 1883
mosquitto_sub -t "#" -v
``` </pre>

# Check der Modbus Verbindung zum Goodwe Wechselrichter vom Raspi aus
Ip-Adresses des Goodwe Wechselrichters verwenden
<pre> ```bash
telnet 192.168.XXX.XXX 502
mbpoll -a 247 -t 4:int -r 35105 -c 1 -0 -1 192.168.XXX.XXX
``` </pre>
