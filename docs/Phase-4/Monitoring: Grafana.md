# Adminer Deployment

## Introduction

The goal of this section is to monitor the Linux host using the Prometheus ecosystem.

The monitoring stack consists of three components:

Node Exporter → Collects hardware and operating system metrics from the host.
Prometheus → Periodically scrapes the metrics exposed by Node Exporter and stores them as a time-series database.
Grafana → Connects to Prometheus and visualizes the collected metrics through dashboards.

The final architecture is shown below:

Linux Host -> Node Exporter (Port 9100) -> Prometheus (Port 9090) -> Grafana (Port 3000)

---

## Creation of the service

We need to create a separate folder for Prometheus following the same structure used in the rest of the homelab.

```
mkdir -p ~/homelab/grafana
cd ~/homelab/grafana
```

## Create the volume

To create it with: ```mkdir data``` and to prevent errors: ```sudo chown -R 472:472 ~/homelab/grafana/data```.

Now let's create the compose file.

```
services:
  grafana:
    image: grafana/grafana-oss:latest

    container_name: grafana

    restart: unless-stopped

    ports:
      - "3000:3000"

    volumes:
      - ./data:/var/lib/grafana

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="779" height="349" alt="Screenshot 2026-07-20 112756" src="https://github.com/user-attachments/assets/1d29e0f1-b587-4242-928f-0599ddb6e137" />

---

## Lift the service and access to the service.

```sudo docker compose up -d``` and to check if everything is ok: ```sudo docker ps```

<img width="1121" height="92" alt="Screenshot 2026-07-20 113008" src="https://github.com/user-attachments/assets/15750020-1737-4f83-b703-2b62e21ae5a3" />

---

Access to the website: ```http://192.168.0.100:3000```

We need to type the username and passord.

```
Username: admin
Password: admin
```

<img width="509" height="508" alt="Screenshot 2026-07-20 113314" src="https://github.com/user-attachments/assets/f4d07951-8203-4fb8-a5d1-ffde7bac773e" />

<img width="2092" height="1111" alt="Screenshot 2026-07-20 113429" src="https://github.com/user-attachments/assets/8db3e740-b955-4094-89e0-016e5cbd29b3" />

---

## Connect to Prometheus

Onece inside, we need to go to: Connections -> Data sources -> Add data source and select Prometheus

<img width="2091" height="455" alt="Screenshot 2026-07-20 113838" src="https://github.com/user-attachments/assets/811d6b84-55a7-4cb3-85f1-fa16836ae121" />

Insert the URL: ```http://prometheus:9090```

<img width="1041" height="693" alt="Screenshot 2026-07-20 113940" src="https://github.com/user-attachments/assets/7ec4cafd-8189-455d-b783-8bc3d459be5b" />

And after clicking at Save & Test, should appear this:

<img width="872" height="125" alt="Screenshot 2026-07-20 114241" src="https://github.com/user-attachments/assets/395dd866-eb30-4f0c-8c36-1fb9138e3a15" />

---

## Import the Node Exporter files to the Dashboard

Inside grafana: Dashboards -> New -> Import

<img width="2090" height="229" alt="image" src="https://github.com/user-attachments/assets/4b1a2bd7-971d-4863-ad30-86f7c0e3135f" />

Introduce the ID: 1860

<img width="637" height="338" alt="Screenshot 2026-07-20 114517" src="https://github.com/user-attachments/assets/3262f703-8520-42f6-b34d-d801d353a6ea" />

And Import, after this, our stack to monitoritation will be working.

<img width="2078" height="1299" alt="Screenshot 2026-07-20 115514" src="https://github.com/user-attachments/assets/79aece38-9504-40f9-879d-c89b1fd9f142" />

Now, whenever we need to check how everything is going, we just need to go here:

<img width="1854" height="356" alt="image" src="https://github.com/user-attachments/assets/28ddd168-31a5-447c-9b66-6899653b6354" />

---

# Connect to Dashy

We need to change to the Dachy directory to edit the configuration file.

```
cd ~/homelab/dashy/user-data
nano conf.yml
```

And inside the monitoring section, below Uptime Kuma, we will add this:

```
- title: Grafana
  description: Monitoring dashboards
  icon: hl-grafana
  url: http://192.168.0.100:3000
- title: Prometheus
  description: Metrics database
  icon: hl-prometheus
  url: http://192.168.0.100:9090
```

<img width="365" height="241" alt="image" src="https://github.com/user-attachments/assets/a0993ecb-ed9b-4f6a-8ac2-4f0571855859" />

And will appear like this:

<img width="1990" height="434" alt="image" src="https://github.com/user-attachments/assets/2e67970c-6cf6-4148-b374-8830149d8d55" />

