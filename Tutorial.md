
# Zero-Trust Server Hardening Lab  
A fully isolated Zero-Trust Linux security lab featuring SSH key-only authentication, UFW firewall lockdown, Fail2Ban brute-force protection, automated security updates, Tailscale private networking (with exit-node routing), and optional offensive security components such as a honeypot and BeEF exploitation lab.

This project documents the full setup process from generating keys, securing the server, installing Tailscale, and configuring complete traffic isolation.
![seedream-4 0_Make_hardering_zero_trust_server_in_space_most_secure_server Use_darks_color-0](https://github.com/user-attachments/assets/0a824853-1252-47b8-9022-ce11615df35d)


---

##  Features
- Zero-Trust access (0 public ports required)
- SSH key-only authentication (no passwords and root login)
- UFW firewall default-deny policy
- Fail2Ban for brute-force detection
- Automated security updates (unattended-upgrades)
- Tailscale private network access
- Exit-node routing capability
- Optional: BeEF web exploitation lab (isolated)
- Optional: Port 22 honeypot for brute-force monitoring

---

## Step 1: Generate Your SSH Keys 

###  Windows (PuTTY) ---on your PC, no on server!

1. Install **PuTTY** + **PuTTYgen**  
2. Open PuTTYgen → click **Generate**
<img width="590" height="457" alt="PUTTY GEN" src="https://github.com/user-attachments/assets/d10c1325-b8ca-4237-a817-f20de456cd8e" />

3. Move your mouse to create entropy
4. Save your **public** and **private** keys

###  Mac / Linux - Works on PowerShell (on your PC,no on server)
 ```bash 
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Enter a strong passphrase or hit 2 times enter

### Generated files
---
Copy the path to your SSH key. 
Keys looks like this
---
*id_ed25519* (private key) 
---
<img width="508" height="103" alt="tempsnip" src="https://github.com/user-attachments/assets/e39fc0dc-ec5d-46f5-a690-17fb6b4143fb" />

*id_ed25519.pub* (public key) (in this case id_ed25519.pub)
---
<img width="569" height="16" alt="tempsnip" src="https://github.com/user-attachments/assets/6224e35a-ef9a-4c1f-a292-49908051cf9b" />

The public key goes on your server provider. The private key stays with you — protect it.

---

## Step 2 Create folder .ssh (on your server) If the deployment does not automatically install your SSH key, do the following
Default username depends on your provider (ubuntu, admin, root).
```
mkdir -p ~/.ssh
chmod 700 ~/.ssh

```
After creating .ssh we need create /authorized_keys 
```
touch  ~/.ssh/authorized_keys
chmod 600  ~/.ssh/authorized_keys
```
Now paste your *public key* .pub into the *authorized_keys* file 
```
nano ~/.ssh/authorized_keys

```

## Step 3 Connect to Your Server using your key 

### Windows (PuTTY) ###

Open PuTTY
---
<img alt="PUTTY" src="https://github.com/user-attachments/assets/a8d07458-fa86-49e0-b441-1da18addf5d1" />
---


Enter server IP

Go to SSH → Auth → load your private key (.ppk)

Accept fingerprint 

Enter SSH key passphrase

### Mac / Linux works on PowerShell ###

Use this format:

This is for linux,PowerShell 
```
Win
ssh -i C:\Users\YourUser\.ssh\private_key username@server_ip
Linux
ssh -i ~/.ssh/private_key username@server_ip
```
Replace path, username, and IP
Verify SSH fingerprint
Enter passphrase

## You are now inside your server. 

---
 
## Step 4: Hardening Your Server
![seedream-4 0_A_dimly_lit_room_filled_with_rows_of_computer_servers_and_monitors_the_screens_d-0](https://github.com/user-attachments/assets/a3c851a6-eb77-49fe-a552-37556a54eb41)

### Update system ###
```
 sudo apt update && sudo apt upgrade -y
```
### Instal and configure UFW firewall 
```
sudo apt install ufw -y
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable

```
### Install Fail2Ban (brute-force protection) in this case i will disable password login  
```
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

```
### Change ssh prot 22 to 3333 (or other noumber)

```
sudo nano /etc/ssh/sshd_config
``` 
Find this 

<img width="205" height="73" alt="Capture" src="https://github.com/user-attachments/assets/5eda0d63-c9c8-4252-8147-6114191cb8e7" />

---
Chage port to 3333 like on img

Now check ``` grep -RIn "Port" /etc/ssh/sshd_config.d/ ``` and change port 22 to port 3333
 
### Disable root login + password auth 

Now we need to configure the file``` /etc/ssh/sshd_config ```. In that file, set PermitRootLogin no, PubkeyAuthentication yes, and PasswordAuthentication no.
Also open ``` nano /etc/ssh/sshd_config.d/50-cloud-init.conf``` if you use newest version of linux/ubuntu and set PasswordAuthentication no there as well.
After this, only machines that have your private key will be able to access the server — brute-forcing the server becomes impossible.
Now restart the SSH service with: ```sudo systemctl restart sshd```


### Automated security updates ###
```
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure -plow unattended-upgrades

```

--- 
## Step 5: Tailscale 

Tailscale creates a private, Zero-Trust network between your devices.
Your server becomes reachable only through this secure mesh, without exposing any public ports to the internet.

### Perfect for server setups where you want: ###

- Hidden and secure SSH access
- No open ports visible to attackers
- A fully private management network
- Simple, encrypted device-to-device connectivity

This greatly reduces the attack surface and prevents automated scans, brute-force attempts, and unauthorized acces

--- 

## Step 6: Install Tailscale on the Server

Now create an account on the Tailscale website.
You will see instructions similar to this, but specifically generated for your server.
In the download section, Tailscale will provide the exact commands and parameters for your machine.
I will also include the commands here as a precaution.

Go on TailScale web page and create account 


<img width="570" height="900" alt="register 1" src="https://github.com/user-attachments/assets/6eb1b62f-99d4-480a-862a-aafb860ef235" />

---

<img width="573" height="437" alt="register 2" src="https://github.com/user-attachments/assets/0c691e83-f016-42cb-802c-c11a16d5db78" />

```
curl -fsSL https://tailscale.com/install.sh | sh
```
Authenticate (from Tailscale admin console → create auth key):
```
sudo tailscale up --authkey YOUR_AUTH_KEY --advertise-exit-node 
```
THIS IS USEFUL ONLY IF YOU PLAN TO USE YOUR SERVER AS VPN  
```
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```
### Allow Tailscale port 
```
sudo ufw allow 41641/udp
sudo ufw reload
```

---

## Step 7: TOTAL LOCKDOWN MODE
Zero Trust means that all ports are closed except the ones you explicitly choose to open.
In my case, I am opening port 22 for the honeypot and port 80 for local web testing.

Block everything coming in
```
sudo ufw default deny incoming
```
Allow all outgoing traffic
```
sudo ufw default allow outgoing
```
Allow Tailscale traffic (private access)
```
sudo ufw allow in on tailscale0
sudo ufw allow out on tailscale0
```

If hosting a website on port 80 (HTTP) (In my case yes) 
```
sudo ufw allow 80/tcp
```
If running a honeypot on port 22 (In my case yes) 
```
sudo ufw allow 22/tcp
```
Enable the firewall
```
sudo ufw enable
```


## Step 8: How to login on your server when we set TailScale?


When you finish the setup following this guide, make sure to log in to your previously created Tailscale account. After logging in, check the “My Machines” page — your server should appear there. Once it’s visible, connect to it using the Tailscale IP shown on that page, for example:
The SSH port (3333) is not open publicly, but it can be used within the Tailscale network
```
ssh test@100.31.13.31 -p 3333
```
If you experience issues, verify that Tailscale is running on the server:
```
sudo systemctl status tailscaled
```
Also check that you are logged in through the web browser (Tailscale admin panel).


























