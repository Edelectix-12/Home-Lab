# Dashy Deployment

## Introduction and pre-sets
Dashy will be my homelab dashboard, from it i will access to all the differents services i will install.

First we need a structure:

```
mkdir -p ~/homelab/dashy
cd ~/homelab/dashy
```

## Create Docker Compose

I will follow the official documentation to mantain the configuration as code for a more easy services gestion:

```sudo nano docker-compose.yml```

And the content will be this one:

```
services:
  dashy:
    image: lissy93/dashy:latest

    container_name: dashy

    restart: unless-stopped

    ports:
      - "8080:8080"

    volumes:
      - ./user-data:/app/user-data

    environment:
      - NODE_ENV=production

    healthcheck:
      test: ["CMD", "node", "/app/services/healthcheck"]
      interval: 90s
      timeout: 10s
      retries: 3
      start_period: 40s

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="781" height="517" alt="image" src="https://github.com/user-attachments/assets/f79ff097-6130-4f62-9ffa-7bcb28b5e5ce" />

## Create the configuration directory

Dashy saves:
- conf.yml file
- icons
- themes
- custom CSS
- resources

everything in ```/app/user-data```

Create it: ```mkdir user-data```

## Launch Dashy

```docker compose up -d```
<img width="475" height="84" alt="image" src="https://github.com/user-attachments/assets/3d465a27-720e-4765-9f4e-2a366fc3701d" />

To verify:

```docker ps```

<img width="1265" height="100" alt="image" src="https://github.com/user-attachments/assets/544a308c-6ae4-4a06-8428-99b8c139e7c9" />

To check logs:

```docker logs dashy```

<img width="763" height="503" alt="image" src="https://github.com/user-attachments/assets/1108ea24-0ccf-42d0-848f-31ee4ad152cd" />

It will give us this error, so we need to create the configuraion file that Dashy needs.

```sudo nano user-data/conf.yml```

And we will paste this:

```
pageInfo:
  title: My Home Lab
  description: ASIR Homelab Dashboard

sections:
  - name: Media
    icon: fas fa-film
    items:
      - title: Jellyfin
        description: Media Server
        url: http://192.168.0.100:8096
        icon: hl-jellyfin
```

<img width="770" height="238" alt="image" src="https://github.com/user-attachments/assets/3b1d94fa-87e8-495a-b602-3577a9d9cf08" />

Restart dashy: ```sudo docker restart dashy```

And to see the logs again, we will see this:

<img width="742" height="794" alt="image" src="https://github.com/user-attachments/assets/1041f708-ccda-4f69-8e36-ae7cf2d84a0e" />

In my case, is ok the way it is, it says that the configuration file has no issues, and is ready to execute.

To open it in my case, we go to the browser and type: ```http://192.168.0.100:8080```.

<img width="844" height="452" alt="image" src="https://github.com/user-attachments/assets/1f6ccf71-5f15-4fee-94bb-c53d3030980a" />

We can see the jellyfin service, it was a success
