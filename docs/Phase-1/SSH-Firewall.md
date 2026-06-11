To have the best communication for now, I will install ```SSH``` as remote access and ```UFW``` as firewall

SSH
-
```sudo apt install openssh-server``` to install it and check status with ```systemctl status ssh```.

<img width="704" height="140" alt="image" src="https://github.com/user-attachments/assets/870bae6f-f245-45f1-a113-b5ed788cdd48" />

Now i can access to the VM from my personal computer via PowerShell just tryping ```ssh host@Server IP```.

Firewall
-

```sudo apt install ufw```, to allow the SSH throw firewall, and after that we need to enable the firewall with ```sudo ufw enable```.

<img width="453" height="232" alt="image" src="https://github.com/user-attachments/assets/70eedc9e-a959-43c5-8510-cfb223e71332" />
