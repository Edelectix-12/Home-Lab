# Adminer Deplyment

## Introduction and pre-sets

Adminer (formerly phpMinAdmin) is a full-featured database management tool written in PHP. Conversely to phpMyAdmin, it consist of a single file ready to deploy to the target server. Adminer is available for MySQL, so is perfect for MariaDB.

I will be using the official documentation of how to install it in docker: https://hub.docker.com/_/adminer/

First we need a structure:

```
mkdir ~/homelab/adminer
cd ~/homelab/adminer
```

This time we will not create a data file.

## Create Docker Compose

Now that we are in the directory, we need to create the configuration file.

```nano docker-compose.yml```

And i will write this:

```
services:
  adminer:

    image: adminer:latest

    container_name: adminer

    restart: unless-stopped

    ports:
      - "8082:8080"

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="764" height="317" alt="image" src="https://github.com/user-attachments/assets/cd3a4ecf-999b-4bdb-977a-fe7c7996a48d" />

---

To iniciate the container:

```sudo docker compose up -d```

<img width="473" height="96" alt="image" src="https://github.com/user-attachments/assets/9e7b9cce-240f-4351-89ee-469d9c7d3ed2" />

Now, to check that everything is correct:

```sudo docker ps```

<img width="1292" height="289" alt="image" src="https://github.com/user-attachments/assets/b84b7733-8b65-4cb0-a111-86ec2eb8abdc" />

Let's open it in the web browser:

```http://192.168.0.100:8082```

<img width="611" height="335" alt="image" src="https://github.com/user-attachments/assets/a7c0eeb4-fce0-4bef-bdbf-c5c54e8f86ac" />

Perfection, let's add the password and users:

<img width="1279" height="604" alt="image" src="https://github.com/user-attachments/assets/f70e0be8-4855-4259-8466-84e15312064b" />

And we can see that is the same but in a graphic interface MariaDB.

Because we added Adminer, now, we can check our home lab and we will see that the Adminer ico appears:

<img width="1995" height="345" alt="image" src="https://github.com/user-attachments/assets/16011f3d-6e31-4cd0-a63b-ca3bf15e645c" />

## Add to Uptime Kuma

---

This is easy, we just need to go our uptime kuma and create a new connection with this info:

<img width="661" height="449" alt="image" src="https://github.com/user-attachments/assets/47018939-253e-4559-a90a-ecc89eaed318" />

And with this, the Phase 3 is completed.

<img width="1944" height="560" alt="image" src="https://github.com/user-attachments/assets/da6e3042-d48e-49ce-83d0-297fde61117b" />


