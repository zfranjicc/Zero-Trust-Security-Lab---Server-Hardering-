## 1. Install python dependencies
 ```
 sudo apt update
 sudo apt upgrade
sudo apt-get install git python3-virtualenv libssl-dev libffi-dev build-essential libpython3-dev python3-minimal authbind virtualenv
 ```
## 2. Create user account
 ```
sudo adduser --disabled-password cowrie
su - cowrie
 ```
## 3. Download cowrie
 ```
git clone http ://github.com/cowrie/cowri
 ```
## 4. Setup virtual environment
 ```
 virtualenv cowrie-env
 source cowrie-env/bin/activate
 pip install --upgrade pip
 pip install --upgrade -r requirements.txt
 ```
## 5. Enable telnet
 ```
 cp /etc/cowrie.cfg.dist cowrie.cfg
 ```
## 6.Iptables
 ```
sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
sudo iptables -t nat -A PREROUTING -p tcp --dport 23 -j REDIRECT --to-port 2223
 ```
## 6. Start cowrie
 ```
bin/cowrie start
 ```
## 7.live logs
 ```
tail -f ./var/log/cowrie/cowrie.log
 ```
