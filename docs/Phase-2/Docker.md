# Docker Installation

Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker apt repository. Afterward, you can install and update Docker from the repository.

Before installing, we need to type this:

```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

Then we can instal it, i'm going to start with the last ```docker``` package with the command:

```sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin```

The following steps will be, check the current status:

```sudo systemctl status docker```

<img width="1263" height="405" alt="image" src="https://github.com/user-attachments/assets/3a7920a8-3154-4ef5-bc38-e6a371359a0f" />

To activate the service will do the following commands in case the program is not on-line:

```sudo systemctl enable docker``` and ```sudo systemctl start docker```

Now we can verify if it can runs a simple hello-world:

```sudo docker run hello-world```

<img width="673" height="489" alt="image" src="https://github.com/user-attachments/assets/eca3c318-7d77-4363-b709-6579416ed24d" />

We can see that if it doesn't find the image, it can search in the web to find it like in this example.

If we don't have 0 problems we can do a ```sudo apt update``` and continue.

Source: https://docs.docker.com/engine/install/ubuntu/
