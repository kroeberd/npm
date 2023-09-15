# Anleitung

## Docker installieren
```
apt update && apt dist-upgrade -y && apt autoremove -y && apt autoclean -y

apt install curl -y && apt install docker.io -y && systemctl enable docker
```

## Docker Compose Plugin installieren
```
curl -L https://github.com/docker/compose/releases/download/v2.17.3/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
## Pfad für NPM erstellen
```
mkdir npm

cd npm

nano docker-compose.yml
```

Nach dem letzten Befehl sollte sich ein Texteditor öffnen. In diesen dann folgenden Text einfügen:
```
version: '3.8'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: always
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
      # Add any other Stream port you want to expose
      # - '21:21' # FTP
    environment:
      # Mysql/Maria connection parameters:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm"
      DB_MYSQL_PASSWORD: "npm"
      DB_MYSQL_NAME: "npm"
      # Uncomment this if IPv6 is not enabled on your host
      # DISABLE_IPV6: 'true'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    depends_on:
      - db

  db:
    image: 'jc21/mariadb-aria:latest'
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 'npm'
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'npm'
      MYSQL_PASSWORD: 'npm'
    volumes:
      - ./mysql:/var/lib/mysql
```
Dann `nano` wieder mit Ctrl+O und Ctrl+X schließen.

## Docker auf dem LXC lauffähig machen
```
systemctl stop docker

nano /etc/docker/daemon.json
```
nun folgendes in die Datei einfügen:
```
{
  "storage-driver": "vfs"
}
```
Dann `nano` wieder mit Ctrl+O und Ctrl+X schließen.

Nun kann Docker wieder gestartet werden:
```
systemctl start docker
```
Jetzt kann auch der Docker Stack des Nginx Proxy Manager mit folgendem Befehl gestartet werden:
```
docker-compose up -d
```
Nun nur noch auf dem Webpanel (http://IP-des-LXCs:81/) des Nginx Proxy Managers Mail und Passwort ändern (default ist admin@example.com und changeme)
