# Glances Deployment

## Introduction

Glances is a lightweight monitoring application that provides a real-time overview of the server. Unlike Prometheus, which stores metrics for historical analysis, Glances focuses on displaying the current status of the system through an easy-to-use web interface.

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
  glances:
    image: nicolargo/glances:latest-full
    container_name: glances

    restart: unless-stopped

    pid: host

    ports:
      - "61208:61208"

    environment:
      - GLANCES_OPT=-w

    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /etc/os-release:/etc/os-release:ro

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="777" height="422" alt="image" src="https://github.com/user-attachments/assets/0180d481-e5d6-4456-8f5b-9b43209575c3" />

---

## Start the container

```sudo docker compose up -d```

<img width="489" height="82" alt="image" src="https://github.com/user-attachments/assets/41dd5bd8-944f-4b43-87d2-d24d5608f40c" />

And to check it: ```sudo docker ps```

<img width="1286" height="320" alt="image" src="https://github.com/user-attachments/assets/5e035fd5-d1bc-49ef-9d1d-ba547fcefb79" />

Now in the web browser: ```http://192.168.0.100:61208```

<img width="3065" height="806" alt="image" src="https://github.com/user-attachments/assets/b5d0d99b-bfa2-467a-b00f-6d54e941b74d" />

As we can see in the bottom site, there's no warnings detected or errors, perfect.

---

## Connection to Promethes in Glances

In the official documentation of Glances, it says that to export the metrics to Prometheus with the option ```--export prometheus``` in the docker-compose file.

To be continue....
