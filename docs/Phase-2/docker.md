# Docker Installation

I'm going to start just installing the ```docker.io``` package with it's dependencies with   the command

```sudo apt install docker.io```

The following steps will be, check the current version, set on the service and verify if it can do a hello-world with no problems

To check the version we can use the command

```docker --version```

<img width="421" height="75" alt="image" src="https://github.com/user-attachments/assets/5742eb07-5ad7-4855-afca-f3fd55f2ad14" />


To activate the service will do the following commands:

```sudo systemctl enable docker``` and ```sudo systemctl start docker```

Now we can verify if it can runs a simple hello-world:

```sudo docker run hello-world```

<img width="673" height="489" alt="image" src="https://github.com/user-attachments/assets/eca3c318-7d77-4363-b709-6579416ed24d" />

We can see that if it doesn't find the image, it can search in the web to find it like in this example.

If we don't have 0 problems we can continue.
