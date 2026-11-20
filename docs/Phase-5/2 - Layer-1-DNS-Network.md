# Layer 1 (`docker-compose-v1.yml`): Core Network & Local DNS Service

## 1. Purpose and Motivation
The objective of this first incremental layer is to establish the core network resolution and traffic filtering infrastructure for the homelab using **Pi-hole**. 

Deploying Pi-hole as the foundational layer solves two key requirements:
* **Local Authoritative DNS:** Enables custom local domain resolution across all containers and network clients.
* **Network-Wide Filtering:** Acts as a DNS sinkhole to block unwanted tracking and advertisement domains without requiring client-side software.

---

## 2. Incremental Compose Configuration (`docker-compose-v1.yml`)

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

---

## 3. Layer Deployment

Deploy Layer 1 in detached mode and verify container initialization:

```
sudo docker compose -f compose-versions/docker-compose-v1.yml up -d
sudo docker ps
```

---

## 4. Partial Testing & Verification Results

To validate Layer 1 before introducing database services in Layer 2, the following functional tests were executed:

### Test 1: Web Administration Interface

Procedure: Accessed http://192.168.0.100:8081/admin via web browser and authenticated using the configured API password (FTLCONF_webserver_api_password).

Result: Successful login and full access to the Pi-hole administrative dashboard.

### Test 2: Docker Network Isolation Verification
Procedure: Executed ```sudo docker inspect pihole``` to verify network attachment.

Result: Confirmed the container is properly attached to homelab-network with internal DNS resolution active.

### Test 3: DNS Resolution Test (nslookup)
Procedure: Executed an interactive shell inside the container (```sudo docker exec -it pihole bash```) and queried an external domain (```nslookup google.com```).

Result: The DNS service resolved the external address accurately, confirming port 53 binding and upstream forwarding are fully operational.

---

5. Layer Conclusion
Layer 1 (docker-compose-v1.yml) passed all network and DNS resolution tests. With name resolution and network filtering verified, the infrastructure is ready to proceed to Layer 2 (Database Persistence & Management).
