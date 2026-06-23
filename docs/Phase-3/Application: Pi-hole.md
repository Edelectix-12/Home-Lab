# Pi-hole deployment

## Introduction and pre-sets


Pi-hole is a DNS sinkhole that protects your devices from unwanted content, without installing any client-side software.

Im going to follow the official documentation with my own adaptation for my own home lab, feel free to change what you need.

First we need a structure:

```
mkdir -p ~/homelab/pihole
cd ~/homelab/pihole
```

Then we need a directory to save the data: ```mkdir data```.

## Create Docker Compose

We need to create our configuration file, as i said before, i will change a bit the information from the official site to adapt it to my own home lab.

```sudo nano docker-compose.yml```

And inside we will type this:

```
services:
  pihole:
    container_name: pihole

    image: pihole/pihole:latest

    hostname: pihole

    restart: unless-stopped

    ports:
      - "53:53/tcp"
      - "53:53/udp"

      - "8081:80/tcp"

    environment:
      TZ: "Europe/Madrid"

      FTLCONF_webserver_api_password: "5057"

      FTLCONF_dns_listeningMode: "ALL"

    volumes:
      - ./data:/etc/pihole

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="777" height="549" alt="image" src="https://github.com/user-attachments/assets/f0381e1e-dcb2-40b5-a096-eca6f079efb7" />

The officail documentation says that the port should be 80:80 but i have Dashy with that port, so i will change it to 80:81.

To iniciate the container:

```sudo docker compose up -d```

<img width="463" height="58" alt="image" src="https://github.com/user-attachments/assets/b926b83f-ecb0-4a01-a70a-c50f2c3e7039" />

<img width="465" height="64" alt="image" src="https://github.com/user-attachments/assets/c83bbe93-1e56-4e61-9a44-a3360d795d3b" />

To verify: ```sudo docker ps```

<img width="1288" height="215" alt="image" src="https://github.com/user-attachments/assets/9a678dc6-9e6b-459a-8544-8b777a3f5a86" />

Perfect.

To access to the service:

- Web browser: ```http://192.168.0.100:8081/admin```

<img width="579" height="722" alt="image" src="https://github.com/user-attachments/assets/9db2e354-86b9-426f-b767-e8dcf957eb4d" />

We will need to place the password that we entered previously in our configuration file, in my case 5057.

<img width="1276" height="1328" alt="image" src="https://github.com/user-attachments/assets/261271c7-1af5-4d43-b504-6ea1e0b1817a" />

Excellent.

Verify if it's in the Home Lab network: ```sudo docker inspect pihole```

<img width="772" height="403" alt="image" src="https://github.com/user-attachments/assets/9711ce6d-a1cb-461f-a725-dbec5690211c" />

There it is, now it verify the DNS resolution, let's type this:

```
sudo docker exec -it pihole bash
```

And let's test with ```nslookup google.com```

<img width="257" height="152" alt="image" src="https://github.com/user-attachments/assets/9cb4fa3b-4e73-4340-b32b-68667dfc1646" />

It resolved the address so onces more, the DNS is working perfectly.

## Add Pi-hole to Uptime Kuma

Create a new monitor with the parameters:

- Monitor Type: HTTP(s)
- URL: http://pihole/admin

<img width="2069" height="1038" alt="image" src="https://github.com/user-attachments/assets/847e950d-1566-47b8-9135-02b2d929d0db" />

## Add Pi-hole to Dashy

We need to edit the ```conf.yml``` file:

```
- name: Network
    icon: fas fa-network-wired
    items:
      - title: Pi-hole
        icon: hl-pihole
        url: http://192.168.0.100:8081/admin
```
<img width="720" height="426" alt="image" src="https://github.com/user-attachments/assets/50ea884c-83fc-41df-897c-90b89a6af893" />

<img width="1549" height="326" alt="image" src="https://github.com/user-attachments/assets/7ef0631b-c990-4371-8153-acc0ce50486c" />

Without this we couldn't install the next service: Database Management.....
