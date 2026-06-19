# Introduction

Im going to use a volume to storage content, and self-hosted applications,
they are different from a normal bind mounts because of their dependency of the directory structure
and the OS of the host machine.

In my case i have 80 GB of storage in my VM so i will have no problems for now, in the case i run out of space
i can add more storage to the VM.

Volumes are a good choice for the following cases:

- Volumes are easier to back up or migrate than bind mounts.
- We can manages the volumes using the docker CLI commands
- Volumes works in Linux and Windows.
- New volumes can be more safely shared among multiple containers.

Let's start.

## Create and manage volumes

To create it:

```docker volume create homelab-data```

To verify it:

```docker volume ls```

<img width="462" height="131" alt="image" src="https://github.com/user-attachments/assets/897fcd11-6836-4fa2-9217-69bb8247173c" />

We can also inspect the volume with the command:

```docker volume inspect homelab-data```

<img width="550" height="225" alt="image" src="https://github.com/user-attachments/assets/1d0ebf95-90fc-4278-ab08-a4097f5e6ded" />

We use this to identify where Docker stores volume data.

And we can remove it if don't want it anymore with the command:

```docker volume rm homelab-data```

## Start and delete a container with a volume

Let's create a TEST container:

```
docker run -it --name volume-test \
  --mount source=homelab-data,target=/data \
  ubuntu bash
```

<img width="668" height="202" alt="image" src="https://github.com/user-attachments/assets/b164660b-fb46-4e3c-97ef-74174cd3dbca" />

And inside it we will write the follow command:

```echo "Docker Persistence Test" > /data/test.txt```

And after this, we will verify the file:

```cat /data/test.txt```

<img width="567" height="73" alt="image" src="https://github.com/user-attachments/assets/89248e8c-dcb2-4710-bb8b-834f119e0ebc" />

To exit:

```exit```

We created a finle INSIDE the mounted volume.

We did a test, so to remove the previous operation, we need to write the next code:

```docker rm volume-test```

## Mount the same volume into a new container

This test will be different because we will create in the same sentence a new file inside a new container in the same volume.

To create the second container:

```
docker run -it --name volume-test-2 \
  --mount source=homelab-data,target=/data \
  ubuntu bash
```

To check:

```cat /data/test.txt```

<img width="475" height="126" alt="image" src="https://github.com/user-attachments/assets/e5ed0ebc-d535-40af-bff1-5c276f0b3341" />

The file remained available after recreating the container.

And we will delete it as well.

```docker rm volume-test-2```

With this we finish this apart.
