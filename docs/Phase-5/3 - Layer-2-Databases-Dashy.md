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
      - /home/host/homelab/dashy/user-data:/app/user-data
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

<img width="472" height="363" alt="image" src="https://github.com/user-attachments/assets/d7511ffb-9716-49c1-b6d1-b9632d30d1f8" />

- Persistent Data: MariaDB persists its data directory in ./mariadb/data:/var/lib/mysql, while Dashy stores conf.yml, icons, and custom settings in ./dashy/user-data:/app/user-data.

- Port Allocation: Dashy runs on 8080:8080 (reserving port 80 cleanly) and Adminer exposes its UI on 8082:8080.

---

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

---

4. Partial Testing & Verification Results

Test 1: MariaDB Initialization, Database Creation & User Authentication
Procedure: Connected to the mariadb container as root (sudo docker exec -it mariadb mariadb -u root -p), listed databases with SHOW DATABASES;, and verified login with the initialized host user (sudo docker exec -it mariadb mariadb -u host -p).

<img width="627" height="400" alt="image" src="https://github.com/user-attachments/assets/faa46d97-e296-41da-8756-f266ebbd8a14" />

<img width="646" height="355" alt="image" src="https://github.com/user-attachments/assets/3b0fc270-45b9-491b-9317-631924c783de" />


Result: The homelab database was automatically created on initialization, and both root and host credentials authenticated without errors.

---

Test 2: Adminer Web Interface & Database Connectivity

Procedure: Opened http://192.168.0.100:8082 in a web browser and authenticated against MariaDB using the configured credentials.

<img width="556" height="324" alt="image" src="https://github.com/user-attachments/assets/08ef8a57-ab15-403b-a66b-a224db1e3551" />

The credentials of our database:

<img width="279" height="267" alt="image" src="https://github.com/user-attachments/assets/527e7c16-bdf3-4c9b-afda-424c14138ee2" />

<img width="1060" height="556" alt="image" src="https://github.com/user-attachments/assets/a2e82688-bc73-40d2-90d5-d009a3a872d3" />

Result: Adminer loaded properly and established a graphical session with MariaDB, confirming network reachability and database readiness.

Test 3: Dashy Configuration Validation & Web Dashboard Access
Procedure: Checked container logs (docker logs dashy), configured ./dashy/user-data/conf.yml, restarted the container (sudo docker restart dashy), verified clean startup logs, and accessed http://192.168.0.100:8080.

Before doing this, we need to make sure that our conf.yml file, the owner is the host, not root, if the file's owner is root, this will result in an error, to see it you must use this command: ```ls -la ~/homelab/dashy/user-data```.

<img width="491" height="83" alt="image" src="https://github.com/user-attachments/assets/76bb3cb8-0642-4e91-a50e-d230e4f8b396" />

To change it you must use the command: ```sudo chown host:host ~/homelab/dashy/user-data/conf.yml && sudo chmod 644 ~/homelab/dashy/user-data/conf.yml```.

<img width="1069" height="123" alt="image" src="https://github.com/user-attachments/assets/5495dc7f-710d-4e5b-baa1-dedba0c33c8b" />

Now we can use the command: ```sudo docker logs dashy```:

<img width="698" height="440" alt="image" src="https://github.com/user-attachments/assets/ddab6394-4ada-4426-ae26-b5e8f9dafeb1" />

Now to acess to the website: http://192.168.0.100:8080.

<img width="1955" height="478" alt="image" src="https://github.com/user-attachments/assets/f5965732-9b7f-4231-9fe9-a9bc05c303fb" />

Result: Dashy validated conf.yml with no errors and rendered the web dashboard displaying configured service links (Pi-hole, Adminer, etc.).

---

5. Layer Conclusion

Layer 2 (docker-compose-v2.yml) successfully integrates persistent relational storage (MariaDB), web-based database administration (Adminer), and centralized dashboard navigation (Dashy) on top of the verified DNS layer from Layer 1.

Key takeaways from this stage:

- Automated & Persistent Database Setup: MariaDB initializes the homelab database and host user automatically while persisting data under ./mariadb/data.

- Operational Visibility & Central Navigation: Adminer provides immediate visual verification of database state, while Dashy centralizes service access through version-controlled configuration (conf.yml) and healthcheck monitoring.

With network filtering, database persistence, and dashboard access in place, the environment is ready for Layer 3 (docker-compose-v3.yml) (monitoring and remaining homelab services).
