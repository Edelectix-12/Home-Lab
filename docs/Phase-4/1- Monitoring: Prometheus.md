# Adminer Deployment

## Introduction

Prometheus is an open-source monitoring system originally developed by SoundCloud. Its function is to collect metrics from servers, applications, and containers to monitor their health and performance.

In this homelab, Prometheus will act as the core of the monitoring system. Later, it will gather information from services like Glances, cAdvisor, and Grafana to display statistics for the server and Docker containers.

---

## Creation of the service

We need to create a separate folder for Prometheus following the same structure used in the rest of the homelab.

```
mkdir -p ~/homelab/prometheus
cd ~/homelab/prometheus
```
---

## Create the configuration file.

```nano prometheus.yml```

And inside we'll type this:

```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "prometheus"

    static_configs:
      - targets: ["localhost:9090"]
```

This file specifies:

- how often to collect metrics;
- which services to monitor;
- how to access them.

Without this file, Prometheus doesn't know what to monitor.

<img width="753" height="161" alt="image" src="https://github.com/user-attachments/assets/42895296-a970-414d-b964-fe0ac734f550" />


---

## Create docker-compose

```nano docker-compose.yml```

And we will write this:

```
services:
  prometheus:
    image: prom/prometheus:latest

    container_name: prometheus

    restart: unless-stopped

    ports:
      - "9090:9090"

    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - ./data:/prometheus

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="770" height="372" alt="image" src="https://github.com/user-attachments/assets/04991093-ca8b-4b99-8c0f-237c28b69c10" />

Mount the server configuration file inside the container.

The suffix: ```:ro``` stands as Read Only, preventing the container from accidentally modifying the file.

---

## Create the data directory

A folder is created where Prometheus will store all the collected information.

Without this folder, the data would be lost if the container were deleted or recreated.

```mkdir data```

---

## Start the container

To prevent errors, let's do this way because is a lab, we must change the permission of the data directory: ```sudo chmod -R 777 data```.

Now we can do this:

```sudo docker compose up -d```

<img width="526" height="105" alt="image" src="https://github.com/user-attachments/assets/7eb4b16a-767c-4763-b0eb-0317a21edd94" />

To verify the state: ```sudo docker ps```sudo

<img width="1286" height="303" alt="image" src="https://github.com/user-attachments/assets/51d420c0-d32e-4111-af30-5f566cc8697a" />

## Web interface:

To check we need to go to the browser and type: ```http://192.168.0.100:9090```

<img width="2090" height="442" alt="image" src="https://github.com/user-attachments/assets/18cfd6bb-b4d9-42df-9f10-861b6feb4dea" />

Is done, the next step is Grafana, because Prometheus only recolects metrics.
