# Duplicati Deployment

## Introduction

The objective of this phase is to implement a centralized backup and recovery solution for the homelab infrastructure using Duplicati.

We will be following the offical documentation from the Duplicate web page: https://docs.duplicati.com/platform-specific-guides/using-duplicati-from-docker. With some adaptations for our own homelab, such as the network configuration.

The system will protect critical configuration files, application data and database backups. It will also provide a documented recovery procedure to verify that the backed-up information can be restored successfully.

---

## Creation of the service

```
cd ~/homelab
mkdir -p duplicati/data
cd duplicati
chmod 700 data
```
---

## Creation of the credentials

We need to generate 2 randoms values.

```
openssl rand -hex 32
openssl rand -hex 32
```

<img width="540" height="107" alt="image" src="https://github.com/user-attachments/assets/00002d5b-7188-411c-9d4c-9f3b5f1499b9" />

We do this because we need the first to serves as the web access password, and the second as the encryption key for the Duplicati configuration. These are two separate secrets.

I will post them because is the propose of this project, but in a real business environment, you should never let others know the key of your backup service.

### Creation of the variable file

```
nano .env
```

<img width="866" height="145" alt="image" src="https://github.com/user-attachments/assets/f905ee35-b76e-4cb9-8377-6a90c338ec13" />

To secure the file: ```chmod 600 .env```

---

## Create the volume

```sudo nano docker-compose.yml```

And inside of it:

```

services:
  duplicati:
    image: duplicati/duplicati:latest
    container_name: duplicati
    restart: unless-stopped

    ports:
      - "8200:8200"

    environment:
      DUPLICATI__WEBSERVICE_PASSWORD: ${DUPLICATI_WEB_PASSWORD}
      SETTINGS_ENCRYPTION_KEY: ${DUPLICATI_SETTINGS_ENCRYPTION_KEY}

    volumes:
      - ./data:/data
      - ../:/source:ro

    networks:
      - homelab-network

networks:
  homelab-network:
    external: true
```

<img width="750" height="416" alt="image" src="https://github.com/user-attachments/assets/a7cd680a-6802-4754-a0d1-e40608e8808e" />



We are using the official image of Duplicati.

The port we will be using is the ```:8200``` and the credentials that we already have and the network of our homelab.

```../:/source:ro:``` permite a Duplicati acceder a los archivos de ```~/homelab``` en modo de solo lectura.

---

## Lift the service and access to the service.

To lift the service: ```sudo docker compose up -d``` and with ```sudo docker ps``` to check them all if everything is correct.

<img width="1281" height="498" alt="image" src="https://github.com/user-attachments/assets/b1047551-1d0c-4442-ae49-acf976356b4c" />

To access to the interface: ```http://192.168.0.100:8200```

<img width="478" height="177" alt="image" src="https://github.com/user-attachments/assets/36b00956-d601-4f69-b0cd-6204830e1ab7" />

It asks for our password, more in concret the ```DUPLICATI_WEB_PASSWORD```, which is: ```462f4bac5c0cdd24defbc05bae9aa78fc6730ead9dae9bbd36db344f78170c0c0``` IN MY CASE.

<img width="1761" height="1059" alt="image" src="https://github.com/user-attachments/assets/4de9aab5-3aca-43e2-9c3f-02f50a13ed0b" />

The bottom option will not be marked. Is not neccesary.

It will ask us for a name for our machine, in my case it'll be ```Homelab```.

Now we have full access to the service.

<img width="2105" height="1388" alt="image" src="https://github.com/user-attachments/assets/f76f92e5-b068-41da-8851-f2206e328f4a" />

---

## Creation of a Backup

To create an automated and encrypted backup job for the HomeLab volumes, follow these steps in the Duplicati Web UI:

### Step 1: Add a New Backup Task
1. From the main dashboard, click on **Add backup** or **Create +**.

<img width="959" height="380" alt="image" src="https://github.com/user-attachments/assets/a44196e5-39f3-4276-b8cb-22f53d5d5c06" />

3. Select **Configure a new backup** and click **Next**.

<img width="323" height="391" alt="image" src="https://github.com/user-attachments/assets/15a85c34-c848-401b-a706-86ef32a27190" />

4. Configure the **General Settings**:
   * **Name:** `HomeLab-Volumes-Backup`
   * **Encryption:** `AES-256 encryption, built-in`
   * Set a strong passphrase for encrypting the backup files (or leave default if managed internally).

<img width="450" height="637" alt="image" src="https://github.com/user-attachments/assets/8ddeb01b-8c75-49e2-90fc-82bf00979d93" />

### Step 2: Select Source Data

Selecting the source directory is enough, and click Continue.

<img width="695" height="1263" alt="image" src="https://github.com/user-attachments/assets/6324dec2-dfb3-4676-86c0-8014ad361436" />

### Step 3: Destination

We will select among all the options, the File system one, to make it faster, you can type the name in the bar on top of all options:

<img width="986" height="358" alt="image" src="https://github.com/user-attachments/assets/dda3e8dc-f3b5-45e7-9a2d-95f4fb92304c" />

And we will select the ```/data``` file path:

<img width="527" height="751" alt="image" src="https://github.com/user-attachments/assets/44058b13-41d2-445b-8750-9d9e53cff20e" />

### Step 4: Schedule and Retention Options

