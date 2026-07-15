# Node Exporter Deployment

## Introduction

Node Exporter was deployed using the official Prometheus Docker image.

Unlike most containers, Node Exporter must monitor the host operating system instead of its own container. To achieve this, it runs using the host network and PID namespace while mounting the host filesystem in read-only mode.

---

## Creation of the service

As always we need to create the pertinent directory.

```
cd ~/homelab
mkdir glances
cd glances
```

---

## Create the configuration file.

```nano docker-compose.yml```

And inside we'll type this, we will need to configurate this file more ahead to connect it to Prometheus, but for now, let's continue with this to check if there's any problems:

```
services:
  node-exporter:
    image: quay.io/prometheus/node-exporter:latest

    container_name: node-exporter

    restart: unless-stopped

    command:
      - '--path.rootfs=/host'

    volumes:
      - '/:/host:ro,rslave'

    ports:
      - "9100:9100"

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="767" height="401" alt="image" src="https://github.com/user-attachments/assets/8c15e717-9cf0-4b0c-a16a-482a11352e3e" />

---

## Start the container

```sudo docker compose up -d```

<img width="512" height="88" alt="image" src="https://github.com/user-attachments/assets/bf5388e8-bcda-45ad-8106-cbddec3700f4" />

And to check it: ```sudo docker ps```

<img width="1285" height="320" alt="image" src="https://github.com/user-attachments/assets/36b63346-0614-4844-ab7b-07b1e6254921" />

Then let's check if it's exporting metrics.

```curl http://localhost:9100/metrics```

<img width="979" height="814" alt="image" src="https://github.com/user-attachments/assets/30c5b468-b796-4be9-b121-fea897a684f3" />

Do not panic, this is the singh that it's working. Now let's connect it to Prometheus.

---

## Connection to Prometheus

We need to edit the file ```prometheus.yml```

```
cd ~/homelab/prometheus
nano prometheus.yml
```

Add the following scrape job to prometheus.yml

```
- job_name: "node-exporter"

    static_configs:
      - targets: ["node-exporter:9100"]
```

<img width="743" height="271" alt="image" src="https://github.com/user-attachments/assets/0aacaaf0-2c4f-42f7-8096-e04b01dc57f7" />

We need to restart Prometheus: ```sudo docker compose restart```

Verify the configuration by opening: ```http://192.168.0.100:9090```

Inside we need to go here:

<img width="896" height="458" alt="image" src="https://github.com/user-attachments/assets/7023af92-f93e-40ef-8fb4-ecfc1fe6f2bc" />

And we'll see this, must appear both with the status Up:

<img width="2098" height="509" alt="image" src="https://github.com/user-attachments/assets/dee6b674-0801-46c4-88aa-21fbff299db4" />

---

Node Exporter is now successfully integrated with Prometheus.

Prometheus periodically scrapes the metrics exposed by Node Exporter through the `/metrics` endpoint and stores them in its time-series database.

The monitoring stack is now able to collect real-time information about the host system, including CPU usage, memory consumption, disk usage, filesystem statistics and network activity.

This completes the Node Exporter deployment and prepares the environment for Grafana dashboards in the next phase.

---

## Next Step

The next phase of the homelab will be deploying Grafana and connecting it to Prometheus in order to visualize the collected metrics through interactive dashboards.
