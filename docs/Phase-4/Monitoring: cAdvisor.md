# cAdvisor Deployment

## Introduction

cAdvisor (Container Advisor) provides container users an understanding of the resource usage and performance characteristics of their running containers. 
It is a running daemon that collects, aggregates, processes, and exports information about running containers.

- Node Exporter → Monitors the host (CPU, RAM, disk, network, operating system).

- cAdvisor → Monitors each Docker container (CPU, memory, network, disk, I/O).

---

## Creation of the service

```
mkdir -p ~/homelab/cadvisor
cd ~/homelab/cadvisor
```

## Create the volume

```sudo nano docker-compose.yml```

And inside of it:

```
services:
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor

    restart: unless-stopped

    ports:
      - "8085:8080"

    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
      - /dev/disk:/dev/disk:ro

    privileged: true

    devices:
      - /dev/kmsg

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="768" height="475" alt="image" src="https://github.com/user-attachments/assets/7c31ef9b-4017-4ac9-a0ba-c9b3482fee5b" />

Unlike the original, we need to ubicate the port instead of 8080, the 8085 to prevent conflicts, this rest is the same as the others volumes with the networks, the restart and adding the original volumes data.

## Lift the service and access to the service.

```sudo docker compose up -d``` and to check ```sudo docker ps```

<img width="1282" height="456" alt="image" src="https://github.com/user-attachments/assets/e540600a-bad3-4be7-b481-ce4aead94f13" />

To access to the web interface: ```http://192.168.0.100:8085```.

<img width="915" height="768" alt="image" src="https://github.com/user-attachments/assets/1c130666-a6cf-4717-8a9c-111467406540" />

And below we can see see the process, memory usages, CPU, Network and more of each docker container.

<img width="1096" height="1311" alt="image" src="https://github.com/user-attachments/assets/5c99e21a-a474-44b8-84cd-0d4724e091cd" />


<img width="983" height="667" alt="image" src="https://github.com/user-attachments/assets/cbd683e4-9eab-4401-9afd-bd1e856cf96b" />

---

## Connect to Prometheus

We need to edit the ```prometheys.yml``` file.

```
cd ~/homelab/prometheus
nano prometheus.yml
```

Add this:

```
- job_name: "cadvisor"

  static_configs:
    - targets: ["cadvisor:8080"]
```

<img width="746" height="385" alt="image" src="https://github.com/user-attachments/assets/f2af2e64-282d-4d29-848d-a630daa91282" />

Restart promethes: ```sudo docker compose restart```.

### Verify

Entering in ```http://192.168.0.100:9090``` should appear the three services in Up status:

<img width="1622" height="594" alt="image" src="https://github.com/user-attachments/assets/41560d73-bbdf-46f9-b5c0-0ebc40484b90" />

## Connect to Grafana

We need to import the offcial dashboard.

The most used UD is ```14282``` or ```193```, one is for container monitoring and the other for the cAdvisor exporter. I will use the first one because is the most active of these two.

<img width="866" height="602" alt="image" src="https://github.com/user-attachments/assets/b327af84-7142-4a39-8e0f-c03a7bd3165d" />

After clicking in Import, we need to select on the top left section, the host and the containers, and we will see this:

<img width="1822" height="1323" alt="image" src="https://github.com/user-attachments/assets/51a29f2c-36aa-4827-8e60-4f55a530e780" />

And now we will have in the Dashboard of Grafana the two of them:

<img width="1254" height="304" alt="image" src="https://github.com/user-attachments/assets/5c61647a-eb09-45c2-8da2-7c065ca379f2" />

