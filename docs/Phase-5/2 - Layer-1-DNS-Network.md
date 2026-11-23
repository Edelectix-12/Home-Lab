# Layer 1 (`docker-compose-v1.yml`): Core Network & Local DNS Service

## 1. Purpose and Motivation
The objective of this first incremental layer is to establish the core network resolution and traffic filtering infrastructure for the homelab using **Pi-hole**. 

Deploying Pi-hole as the foundational layer solves two key requirements:
* **Local Authoritative DNS:** Enables custom local domain resolution across all containers and network clients.
* **Network-Wide Filtering:** Acts as a DNS sinkhole to block unwanted tracking and advertisement domains without requiring client-side software.

---

## 2. Incremental Compose Configuration (`docker-compose-v1.yml`)

First we need to create the directory to fill it with the fellows compose versions, in the homelab directory we will do this: ```mkdir -p compose-versions``` and 
we will create the file with: ```nano docker-compose-v1.yml```.


In this initial stage (`compose-versions/docker-compose-v1.yml`), only the shared bridge network (`homelab-network`) and the Pi-hole service are defined:

```yaml
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

networks:
  homelab-network:
    external: true
```

- Port Mapping Adjustment: While the official documentation defaults to port 80:80, port 80 is reserved for the central dashboard (Dashy). Therefore, the Pi-hole web interface is mapped to host port 8081 (8081:80/tcp).
- Persistent Storage: Configuration and DNS records are persisted in ./pihole/data.

<img width="778" height="390" alt="image" src="https://github.com/user-attachments/assets/803c26f9-313c-4eea-a7ba-c20fe2f5a873" />

---

## 3. Layer Deployment

Before continuing, we have now 2 PIHOLEs with the same container name, so if we try to lift this one up, it will give us an error:

<img width="1282" height="149" alt="image" src="https://github.com/user-attachments/assets/49942767-c29b-4237-ae81-ef02754d591b" />

To prevent this, we need to stop the current pihole and remove it, this is save because all our data is inside our ```~/homelab/pihole/data```.

To stop it: ```sudo docker stop pihole && sudo docker rm pihole```.

---

Now deploy Layer 1 in detached mode and verify container initialization:

```
sudo docker compose -f compose-versions/docker-compose-v1.yml up -d
sudo docker ps
```

<img width="1284" height="545" alt="image" src="https://github.com/user-attachments/assets/3860a154-b4e9-4c64-9d05-a7b6b9016586" />

---

## 4. Partial Testing & Verification Results

To validate Layer 1 before introducing database services in Layer 2, the following functional tests were executed:

### Test 1: Web Administration Interface

Procedure: Accessed http://192.168.0.100:8081/admin via web browser and authenticated using the configured API password (FTLCONF_webserver_api_password).

<img width="1201" height="1029" alt="image" src="https://github.com/user-attachments/assets/69c8d391-e2ad-4d22-b270-cf58ea70260b" />

<img width="1119" height="1241" alt="image" src="https://github.com/user-attachments/assets/65a3e0a5-a2f4-4d8b-bbf4-12082ab684ee" />

Result: Successful login and full access to the Pi-hole administrative dashboard.

### Test 2: Docker Network Isolation Verification
Procedure: Executed ```sudo docker inspect pihole``` to verify network attachment.

<img width="846" height="629" alt="image" src="https://github.com/user-attachments/assets/6d0615d7-9a22-4c39-8202-6d41df0f6663" />

Result: Confirmed the container is properly attached to homelab-network with internal DNS resolution active.


### Test 3: DNS Resolution Test (nslookup)
Procedure: Executed an interactive shell inside the container (```sudo docker exec -it pihole bash```) and queried an external domain (```nslookup google.com```).

<img width="589" height="171" alt="image" src="https://github.com/user-attachments/assets/b54d1936-ea00-44f6-a50d-2abb273387fb" />

Result: The DNS service resolved the external address accurately, confirming port 53 binding and upstream forwarding are fully operational.

### Test 4: Ad-Blocking & Query Log Verification (Web UI)

* **Procedure:** Executed DNS queries against `192.168.0.100` for both legitimate domains (`github.com`) and known tracking/ad domains (`doubleclick.net`) using `nslookup doubleclick.net 1270`.
* **Result:** Pi-hole successfully intercepted and sank the ad domain to `0.0.0.0`.
* The web **Query Log** and main dashboard reflected live traffic metrics, confirming active filtering rules (`Blocked (gravity)`) alongside forwarded requests.

<img width="363" height="165" alt="image" src="https://github.com/user-attachments/assets/c65ffb21-03cb-4a8b-9c1c-f488ad321de6" />

<img width="1128" height="532" alt="image" src="https://github.com/user-attachments/assets/26536de0-a8bd-4a1d-8795-a3a52e8d0d8a" />

<img width="1117" height="1241" alt="image" src="https://github.com/user-attachments/assets/03334f6c-9440-4b3f-91e4-b65889467ff5" />

More queries and results:

```
nslookup google.com 127.0.0.1
nslookup github.com 127.0.0.1
nslookup ubuntu.com 127.0.0.1
nslookup ads.google.com 127.0.0.1
```

<img width="369" height="607" alt="image" src="https://github.com/user-attachments/assets/915fd72e-565a-44f7-bde3-95d218d9db9d" />

<img width="1113" height="775" alt="image" src="https://github.com/user-attachments/assets/eb345fc4-479d-46a2-8518-f9330c2296d7" />

---

## 5. Layer Conclusion

Layer 1 (`docker-compose-v1.yml`) has been successfully deployed, isolated, and validated across all network and DNS functional tests. 

The key achievements of this foundational stage include:
* **Network Isolation & Service Availability:** The Pi-hole container operates cleanly within the dedicated `homelab-network` bridge, exposing DNS resolution on port `53` (TCP/UDP) and its web management interface on port `8081` without conflicting with upcoming web services.
* **Verified Name Resolution & Traffic Filtering:** Both CLI queries (`nslookup`) and real-time **Query Log** telemetry confirm that Pi-hole accurately forwards legitimate domain requests while actively sinking unwanted advertising and tracking domains (`0.0.0.0`).
* **Persistent State:** All blocklists, query metrics, and custom DNS configurations remain safely persisted on the host filesystem (`./pihole/data`).

With a stable, secure, and verified network and name-resolution layer in place, the infrastructure is ready to advance to **Layer 2 (`docker-compose-v2.yml`)**, introducing relational database persistence (**MariaDB**), web-based database administration (**Adminer**), and centralized service navigation (**Dashy**).
