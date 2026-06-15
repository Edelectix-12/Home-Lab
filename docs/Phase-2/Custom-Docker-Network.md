# Introduction

### Why should we create a specific network for our docker?

When you run a container without the ```--network option```, it is connected to the default bridge.

Containers attached to the default bridge have access to network services outside the Docker host. 

They use "masquerading" which means, if the Docker host has Internet access, no additional configuration is needed for the container to have Internet access.

Let's start with checking our actual network with the next command: ```docker network ls```

The list is subdevide in 4 columns: one for the ID of the Network, one for name, one for the type of driver that it has and if it's powering in local or an external machine:

<img width="419" height="141" alt="image" src="https://github.com/user-attachments/assets/a5fa3895-9f1b-4335-a3fa-4e7c685b8f02" />

## Creation of the Network

With the fellow command we will start building our own Network:

```docker network create homelab-network```, it will generate an ID, we can check if it worked with the command: ```docker network ls```

<img width="428" height="139" alt="image" src="https://github.com/user-attachments/assets/d87d6a3c-fca5-46ec-893e-a124d4c7baf5" />

We can see, our network is in bridge and that's great because we can connect to other networks throw our router.

Let's proceed to inspect the network, we can do that with the command: ```docker network inspect homelab-network```:

<img width="674" height="702" alt="image" src="https://github.com/user-attachments/assets/32659307-6798-47f8-b36d-b2693f175fd1" />

What can we see here?

1. The ID of our network.
2. The hour that was created.
3. The type of driver.
4. The subnet what is 182.19.0.0/16 with it's gateway.
5. And we can see if we have containers connected, not the case right now.

## Now let's connect the network with Jellyfin

We need to edit the docker-compose.yml file with the command:

```sudo nano docker-compose.yml```

We'll add the network notes like in the next screenshot:

<img width="779" height="390" alt="image" src="https://github.com/user-attachments/assets/95a07484-2282-470c-8c98-363e3f1eca9a" />

Now we need to restart the service, with the commands: ```docker compose down``` and ```docker compose up -d```

Then if we verify again we can see that in the containers section, Jellyfin will appear:

<img width="788" height="800" alt="image" src="https://github.com/user-attachments/assets/627f869c-6363-4a57-9a09-f9a60666d169" />
