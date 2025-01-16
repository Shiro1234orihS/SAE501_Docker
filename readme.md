# Docker avec Home Assistant - Z-Wave - InfluxDB

## Installation de Docker

### 1. Installation des dépendances nécessaires

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
```

### 2. Ajout de la clé GPG de Docker

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### 3. Ajout du dépôt Docker

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
```

### 4. Installation de Docker

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

### 5. Vérification de l'installation

```bash
docker --version
```

## Lancement des conteneurs Docker

Pour démarrer les services, exécutez :

```bash
sudo docker compose up -d
```

## Adresses des projets

- **Grafana** : [http://VotreIp:3000](http://VotreIp:3000)
- **Home Assistant** : [http://VotreIp:8123](http://VotreIp:8123)
- **Portainer** : [http://VotreIp:9000](http://VotreIp:9000)
- **Z-Wave JS UI** : [http://VotreIp:8091](http://VotreIp:8091)
- **InfluxDB** : [http://VotreIp:8086](http://VotreIp:8086)

## Commandes pour ajuster les permissions

### 1. Permissions pour Grafana

```bash
sudo chmod 777 data/grafana/grafana.db
```

### 2. Permissions pour Home Assistant

```bash
sudo chmod 777 data/homeassistant/.storage/auth
```

---

## Notes additionnelles

- **Vérifiez les permissions des fichiers avant d'appliquer des changements drastiques comme `chmod 777` pour des raisons de sécurité.**
- **Utilisez `docker compose logs` pour diagnostiquer des problèmes dans les conteneurs.**

# Connecter InfluxDB et Home Assistant

1. **Ouvrir le fichier `data/homeassistant/configuration.yaml`**

2. **Ajoutez le contenu suivant :**

```yaml
influxdb:
  api_version: 2
  host: influxdb
  port: 8086
  token: "VotreToken"
  organization: "VotreOrganization"
  bucket: "VotreBucket"
  ssl: false
  verify_ssl: false

  default_measurement: value
  include:
    domains:
      - sensor
      - button
      - light
      - switch
    entities:
      - sensor.zwave_temperature
      - sensor.zwave_humidity
      - sensor.zwave_motion
      - light.zwave_lampe_salon
  exclude:
    domains:
      - automation
      - script

  tags_attributes:
    - friendly_name
    - device_id
    - unique_id
    - friendly_name_str
    - area_id
```

# Déployer Docker Compose au démarrage

### 1. Activer le démarrage automatique de Docker

```bash
sudo systemctl enable docker
```

### 2. Créez un nouveau fichier de service :

```bash
sudo nano /etc/systemd/system/docker-compose-app.service
```

### 3. Ajoutez le contenu suivant :

```ini
[Unit]
Description=Docker Compose Application
Requires=docker.service
After=docker.service

[Service]
Restart=always
WorkingDirectory=/home/user/mon-projet-docker
ExecStart=/usr/local/bin/docker-compose up -d
ExecStop=/usr/local/bin/docker-compose down

[Install]
WantedBy=multi-user.target
```

### 4. Rechargez les services :

```bash
sudo systemctl daemon-reload
```

### 5. Activez le service :

```bash
sudo systemctl enable docker-compose-app
```

### 6. Démarrez le service :

```bash
sudo systemctl start docker-compose-app
```

