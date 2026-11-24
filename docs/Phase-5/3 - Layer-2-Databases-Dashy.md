# Layer 2 (`docker-compose-v2.yml`): Relational Database, Management UI & Central Dashboard

## 1. Purpose and Motivation
Building on the verified DNS and network layer (`v1`), Layer 2 introduces persistent relational data storage and centralized service access for the homelab:

* **MariaDB:** Provides a reliable, open-source relational database engine (`homelab` database and `host` user) to store persistent application data.
* **Adminer:** Offers a lightweight web-based database management interface (`http://192.168.0.100:8082`) to inspect tables, test connectivity, and administer MariaDB without heavy overhead.
* **Dashy:** Acts as a single entry-point web dashboard (`http://192.168.0.100:8080`) using configuration-as-code (`conf.yml`) and a built-in container healthcheck to organize and launch homelab services.

---

## 2. Incremental Compose Configuration (`docker-compose-v2.yml`)

First we need to create the file in the ```compose-versions``` directory with: ```nano docker-compose-v2.yml```.

In `compose-versions/docker-compose-v2.yml`, the **Pi-hole** service from Layer 1 is preserved and expanded with **MariaDB**, **Adminer**, and **Dashy** connected to the shared `homelab-network`:

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
      - ./pihole/data:/etc/pihole
    networks:
      - homelab-network

  mariadb:
    image: mariadb:latest
    container_name: mariadb
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD: "5057"
      MARIADB_DATABASE: "homelab"
      MARIADB_USER: "host"
      MARIADB_PASSWORD: "5057"
    ports:
      - "3306:3306"
    volumes:
      - ./mariadb/data:/var/lib/mysql
    networks:
      - homelab-network

  adminer:
    image: adminer:latest
    container_name: adminer
    restart: unless-stopped
    ports:
      - "8082:8080"
    networks:
      - homelab-network

  dashy:
    image: lissy93/dashy:latest
    container_name: dashy
    restart: unless-stopped
    ports:
      - "8080:8080"
    volumes:
      - ./dashy/user-data:/app/user-data
    environment:
      NODE_ENV: "production"
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

<img width="825" height="715" alt="image" src="https://github.com/user-attachments/assets/b0f0a7bc-2c70-457b-ae6e-2d33c764bb74" />

<img width="508" height="364" alt="image" src="https://github.com/user-attachments/assets/f2a3fe0e-53a5-4986-af8c-148025e388c0" />

- Persistent Data: MariaDB persists its data directory in ./mariadb/data:/var/lib/mysql, while Dashy stores conf.yml, icons, and custom settings in ./dashy/user-data:/app/user-data.

- Port Allocation: Dashy runs on 8080:8080 (reserving port 80 cleanly) and Adminer exposes its UI on 8082:8080.

3. Layer Deployment

Before continuing, we have now 2 MARIADB, ADMINER and DASHY with the same container name, so if we try to lift this one up, it will give us an error:

<img width="1285" height="192" alt="image" src="https://github.com/user-attachments/assets/fb4b6fa7-bc7b-48de-ad53-c921323f7911" />

To prevent this, we need to stop the current services and remove them.

- This is save because all our data from MariaDB is in ```~/homelab/mariadb/data```.

- The data from Adminer doesn't save any data because is a web interface, it only connects with MariaDB.

- The data from Dashy is saved in ```~/homelab/dashy/user-data```.

Now to stop and remove them we need to enter the next commands: ```sudo docker stop mariadb adminer dashy``` & ```sudo docker rm mariadb adminer dashy```

From the ~/homelab directory, deploy Layer 2 and verify running containers:

sudo docker compose -f compose-versions/docker-compose-v2.yml up -d
sudo docker ps

<img width="1285" height="688" alt="image" src="https://github.com/user-attachments/assets/1a999eb9-dcea-4d21-be1d-fda34b3b2f48" />
