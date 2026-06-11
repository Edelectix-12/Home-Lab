# Installation of Jellyfin

### Fast introduction

Jellyfin is a Free Software Media System that puts you in control of managing and streaming your media. There are no strings attached, no premium licenses or features.

## Installation process

We need to download and verify the script,  then execute it on your system (requires curl and sha256sum):

```curl -s https://repo.jellyfin.org/install-debuntu.sh -O && \ curl -s https://repo.jellyfin.org/install-debuntu.sh.sha256sum -O && \ sha256sum -c install-debuntu.sh.sha256sum```

<img width="672" height="109" alt="image" src="https://github.com/user-attachments/assets/42a75a52-17ff-4a23-8997-ea29e61f771f" />

```install-debuntu.sh: OK``` means the checksum is correct.

Then execute it with:

```sudo bash install-debuntu.sh```

I couldn't execute because i ran out of space and i need to do everything from 0.......
