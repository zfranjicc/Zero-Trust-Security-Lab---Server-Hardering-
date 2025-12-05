


# Introduction 
                     

Features

-Dockerized environment for clean and isolated testing

-Easy service deployment through Docker Compose

-Wazuh SIEM for real-time detection and monitoring

-Centralized log collection and analysis

-Built-in vulnerability detection and file-integrity monitoring

-Automatic security alerting

Optional: Kibana dashboards and visualizations

Optional: Honeypot integration for attacker telemetry
![seedream-4 0_Napravi_mi_ovakvu_sliku_sa_modernim_izgledom_sa_laganim_crnim_pozadinskim_izlom_-0](https://github.com/user-attachments/assets/f8ddf2c9-64c4-4dbe-bf27-48b40981e9ac)

 ### Prerequisites

### Before starting, ensure you have:
-Ubuntu 22.04 or 24.04 server
-SSH access
-Basic Linux terminal knowledge
-Minimum 1 GB RAM


### Step-by-Step Installation 

## 1. Update the system
```
sudo apt update && sudo apt upgrade -y
```
## 2. Install required packages
```
sudo apt install git python3 python3-pip python3-venv python3-dev libssl-dev libffi-dev build-essential libmpfr-dev -y
```
## 3. Create a dedicated user for Cowrie
```
sudo adduser --disabled-password --gecos "" cowrie
sudo -u cowrie -H bash
```
---

To enter the cowrie user, you must switch from your privileged account using:
```
sudo -u cowrie -H bash
```
<img width="889" height="52" alt="Capture" src="https://github.com/user-attachments/assets/fbb43dc5-f774-4391-8969-fe3aedaf92b7" />

If you see folder /root just go back to home  ```cd ~ ``` beacuse cowrie doest have sudo privilegies

---

**POSSIBLE ERROR** <br>
The cowrie user has no password and cannot use``` sudo```.
This is intentional — the cowrie account is only meant to run the honeypot in isolation.
So if you try commands like ```sudo su``` while logged in as cowrie, the system will ask for a password that does not exist. That is normal.

## 4. Clone the Cowrie repository
```
git clone https://github.com/cowrie/cowrie.git
cd cowrie
```

## 5. Create a Python virtual environment

```
python3 -m venv cowrie-env
source cowrie-env/bin/activate
```
--- 

After this command you will see your virtual environment looks like:
<img width="455" height="21" alt="Capture 2" src="https://github.com/user-attachments/assets/c75fca1f-af33-43e3-97d4-14e69de06139" />

## 6. Install Cowrie dependencies
```
pip install --upgrade pip
pip install -r requirements.txt
```
## 7. Copy the configuration file

cp etc/cowrie.cfg.dist etc/cowrie.cfg

## 8. Start Cowrie
```
bin/cowrie start
```
---

**POSSIBLE ERROR**
<img width="523" height="36" alt="Capture 3" src="https://github.com/user-attachments/assets/4f4ef878-5ec3-4b5b-bb97-0987e1f168ad" />

If you have error like on image, dont worry you just need install pip:
```
pip install .
```

































