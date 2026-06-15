# Installation of Jellyfin

### Fast introduction

Jellyfin is a Free Software Media System that puts you in control of managing and streaming your media. There are no strings attached, no premium licenses or features.

## Preparation process

We need to download and verify the script,  then execute it on your system (requires curl and sha256sum):

```curl -s https://repo.jellyfin.org/install-debuntu.sh -O && \ curl -s https://repo.jellyfin.org/install-debuntu.sh.sha256sum -O && \ sha256sum -c install-debuntu.sh.sha256sum```

<img width="672" height="109" alt="image" src="https://github.com/user-attachments/assets/42a75a52-17ff-4a23-8997-ea29e61f771f" />

```install-debuntu.sh: OK``` means the checksum is correct.

Now, I'm going to create a little structure, so i can have everything more organized:

```mkdir -p ~/homelab/jellyfin```

```cd ~/homelab/jellyfin```

Now i'll create a compose with the follow content:

```nano docker-compose.yml```

```
services:
  jellyfin:
    image: jellyfin/jellyfin

    container_name: jellyfin

    restart: unless-stopped

    ports:
      - "8096:8096"

    volumes:
      - ./config:/config
      - ./cache:/cache
      - ./media:/media

    environment:
      - TZ=Europe/Madrid
```

Onces done, with the command ```sudo docker compose up -d``` should appear the next screen:

<img width="1286" height="100" alt="image" src="https://github.com/user-attachments/assets/c562e9bc-0f78-45b6-8186-83a4e402803b" />

Then with ```sudo docker ps``` we can verify if it's working

<img width="1295" height="79" alt="image" src="https://github.com/user-attachments/assets/0b70d607-2aec-45fe-8e00-ee173eb8ddb8" />

And after that if we go to our Main computer / a computer in the same network as the jellyfin service, we can write in the navigator: ```https://192.168.0.100:8089``` (in my case) to see this:

<img width="2178" height="600" alt="image" src="https://github.com/user-attachments/assets/441fa49b-8a19-477c-86f8-5992915e31f2" />

With that we have our first docker compose. (I'm kinda happy)
