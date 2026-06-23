# Uptime Kuma Deplyment

## Introduction and pre-sets
-

Uptime Kuma is used to monitor the availability of homelab services and will allow us to prevent possible issues.

First we need a structure:

```
cd ~/homelab
mkdir uptime-kuma
cd uptime-kuma
```

Then we need to create the directory to save the persistent data

```mkdir data```

## Create Docker Compose


Now that we are in the directory, we need to create the configuration file, following the official documentation that you can find in the official Uptime Kuma website.

```sudo nano docker-compose.yml```

And the content will be this one, in relationship with my docker compose Jellyfin, is not the same as the official web because i adapted it to my homelab:

```
services:
  uptime-kuma:
    image: louislam/uptime-kuma:2

    container_name: uptime-kuma

    restart: unless-stopped

    ports:
      - "3001:3001"

    volumes:
      - ./data:/app/data

    environment:
      - TZ=Europe/Madrid

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="768" height="438" alt="image" src="https://github.com/user-attachments/assets/e405eab1-478b-4b5c-a62b-1de3ad839215" />

To verify the file: ```cat docker-compose.yml```

Should appear without problems.

To iniciate the container:

```sudo docker compose up -d```

<img width="501" height="110" alt="image" src="https://github.com/user-attachments/assets/c4a2f680-bb79-4c7c-b0fa-a31df742a465" />

Now let's verify the network:

```sudo docker network inspect homelab-network```

<img width="844" height="644" alt="image" src="https://github.com/user-attachments/assets/7065701a-3e2e-41b5-808b-ba5090929e32" />

To access to the service:

- Web browser: ```http://192.168.0.100:3001```

When is our first time here, will ask us which database we want to install, in our case, with SQLite is more than enough.

<img width="662" height="533" alt="image" src="https://github.com/user-attachments/assets/1a961a0b-fb37-48a6-bd91-5fa5808da1a2" />

And after enter the administrator information, we will be here:

<img width="2091" height="617" alt="image" src="https://github.com/user-attachments/assets/21fca146-69ec-41db-9554-b22c9272f591" />

## Add Jellyfin
-

Inside Uptime Kuma we need to go to ```Add New Monitor```

With the fellow configuration:

- Type: HTTP(s)
- Friendly Name: Jellyfin
- URL: http://jellyfin:8096
- Hearbeat Interval: 60
- Retry: 3

<img width="2073" height="1060" alt="image" src="https://github.com/user-attachments/assets/eae979e4-cdf7-4191-af92-e25ad152890e" />

## Add Dashy
-

The process is the same as before, just changing these parameters:

- Name: Dashy
- URL: http://dashy:8080

### This is the result:

I had some problems related with the URL, but now it's ok, to prevent errors, don't insert in the URL the IP of your machine, instead, type the name of the application followed by the port.

<img width="2083" height="1144" alt="image" src="https://github.com/user-attachments/assets/63e1b3b9-dc92-45ed-b56a-29e12ed22d85" />

## Add to Dashy Dashboard
-

We need to edit the file: ```~/homelab/dashy/user-data/conf.yml``` with ```sudo nano```

And under the Jellyfin section we will add this, because it needs its own section:

```
sections:
  - name: Monitoring
    icon: fas fa-chart-line
    items:
      - title: Uptime Kuma
        url: http://192.168.0.100:3001
        icon: hl-uptime-kuma
```

<img width="722" height="330" alt="image" src="https://github.com/user-attachments/assets/c5679534-a27d-49e3-abf4-faa1a9ca4de5" />

We will need to restart Dashy: ```sudo docker restart dashy```

<img width="1060" height="312" alt="image" src="https://github.com/user-attachments/assets/fe15298d-8ee4-46a1-90e0-092b83421b55" />

Application installation completed.
