# Install Salt-Master with Salt-Cloud
`curl -o bootstrap-salt.sh -L https://bootstrap.saltproject.io`  
`sudo sh bootstrap-salt.sh -P -M -U -L -x python3`  

#### MongoDB pillar data for Salt-Master:
`apt install python3-pip`  
`python3 -m pip install 'pymongo[srv]'`  
**database name: <db-name>**
**collection name: pillar**

#### Install Salt-Api
`apt-get install salt-api`  
`pip install CherryPy==10.2.2`

`openssl genrsa -out /etc/ssl/private/key.pem 4096`  
`openssl req -new -x509 -key /etc/ssl/private/key.pem -out /etc/ssl/private/cert.pem -days 1826`

**Create user and password to use Salt-Api**  
`useradd saltmaster --no-create-home --no-user-group --shell /bin/false`  
`passwd saltmaster <mypassword>`

**Enable the service:**  
`systemctl enable salt-api`

**Generate SSH key pair**  
`ssh-keygen`  

#### Clone  repositories (states, pillar, formulas and master configs) to /srv
`/srv/salt`  
`/srv/pillar`  
`/srv/formulas`  
`/srv/master-configs`  

#### Add botfront-dist, and rasa-for-botfront-venv files in place
**/srv/salt/botfront/files/botfront_dist.tar.gz**  
**/srv/salt/rasa-for-botfront/files/rasa-for-botfront-venv.tar.gz**

**Add Salt-Master ssh-key and other ssh-keys if needed these will be added to minion when run common state**  
`nano /srv/salt/common/files/authorized_keys`


## Salt-Master config files

**Copy master configs**  
`cp -R -v /srv/master-configs/etc/ /`  
**Copy python files**  
`cp -R -v /srv/master-configs/usr/ /` 

**Configure database name and mongo uri**  
`nano /etc/salt/master.d/ext_pillar.conf`  

**Configure your cloud provider**  
https://docs.saltproject.io/en/latest/topics/cloud/index.html

### Setup salt-minion to salt-master

`nano /etc/salt/minion`

master: **<master_ip>**

**Remmeber to restart salt-master salt-minion and salt-api to configs take effect**  
`systemctl restart salt-api.service salt-master.service salt-minion.service`  

**Accpet salt-minion key to Salt-Master**  
`salt-key -a master` 

**Test Salt-Master connection to minion**  
`salt master test.ping`  

**Run updates to Salt-Master**  
`salt master state.apply system-update`  

**Run security configs and install oh-my-zsh to Salt-Master**  
`salt master state.apply common`  

### Test creating new server
```bash
# salt-cloud -p <cloud-profile> <server-name>
```

