# Mariadb Deplyment

## Introduction and pre-sets

MariaDB Server is one of the most popular database servers in the world. It's made by the original developers of MySQL and guaranteed to stay open source. Notable users include Wikipedia, DBS Bank, and ServiceNow.

I will be using the official documentation of how to install it in docker: https://hub.docker.com/_/mariadb

First we need a structure:

```
mkdir -p ~/homelab/mariadb
cd ~/homelab/mariadb
mkdir data
```

## Create Docker Compose

Now that we are in the directory, we need to create the configuration file.

```sudo nano docker-compose.yml```

In relationship with my docker compose Jellyfin, this will not be the same as the official web because i adapted it to my own homelab.

```
services:
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
      - ./data:/var/lib/mysql

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="762" height="454" alt="image" src="https://github.com/user-attachments/assets/13abcf3e-46ff-4d85-b1e6-7208dc95d7e5" />

---

The networks part is always the same to comununicate it with the homelab network, is an easy copy-paste.

I added MARIADB_DATABASE, MARIADB_USER, and MARIADB_PASSWORD because the official documentation also covers automatically creating a database and a user during initialization.

To verify the file: ```cat docker-compose.yml```

Should appear without problems.

To iniciate the container:

```sudo docker compose up -d```

<img width="491" height="85" alt="image" src="https://github.com/user-attachments/assets/d3620a72-0a2f-46c8-9d8e-1bc9d1f4c0aa" />

Now we can check to see if it's everything up and working:

<img width="1284" height="230" alt="image" src="https://github.com/user-attachments/assets/0ef67922-0014-4068-9e09-f1c26e3c2e30" />

<img width="886" height="89" alt="image" src="https://github.com/user-attachments/assets/139ed92b-536a-42fc-b6e8-12a2f348cee3" />

Is ready for connections.

## Log in into Mariadb

```sudo docker exec -it mariadb mariadb -u root -p```

<img width="657" height="196" alt="image" src="https://github.com/user-attachments/assets/bb9c21e2-8a8c-46d5-8a82-e23422b3b488" />

Now let's check if the database exists:

```SHOW DATABASES;```

<img width="347" height="252" alt="image" src="https://github.com/user-attachments/assets/ad002034-6a07-4167-b23e-24dfa0310c6d" />

And to finish i will try to log in with the created user:

```sudo docker exec -it mariadb mariadb -u host -p```

<img width="654" height="180" alt="image" src="https://github.com/user-attachments/assets/9ef45dcb-a85b-458e-a4cc-38e93532bfb2" />

## Add Jellyfin
-

We need to edit the file: ```~/homelab/dashy/user-data/conf.yml``` with ```sudo nano```

And under the Jellyfin section we will add this, because it needs its own section:

```
- name: Databases
  icon: fas fa-database
  items:
    - title: Adminer
      icon: hl-adminer
      url: http://192.168.0.100:8082
```

<img width="308" height="118" alt="image" src="https://github.com/user-attachments/assets/3fda5268-0df3-4021-b42d-97b589780ace" />

And when open our web site, we will not be able to access, because that's Adminer rol here, but we don't have it installed YET, in the next application we will do it.

<img width="1983" height="344" alt="image" src="https://github.com/user-attachments/assets/f90e6945-ba69-4d7b-b691-c290b2dfb468" />

Application installation completed.
