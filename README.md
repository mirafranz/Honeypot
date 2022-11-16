# Project Honeypot

Use Google Cloud's Platform Free Tier
> download and install GCP SDK


MHN Admin Deployment
> First set up firewall rules for MHN Admin

> Use these commands:
```http
gcloud compute firewall-rules list

gcloud compute firewall-rules create http ^
    --allow tcp:80 ^
    --description="Allow HTTP from Anywhere" ^
    --direction ingress ^
    --target-tags="mhn-admin"

gcloud compute firewall-rules create honeymap ^
    --allow tcp:3000 ^
    --description="Allow HoneyMap Feature from Anywhere" ^
    --direction ingress ^
    --target-tags="mhn-admin"

gcloud compute firewall-rules create hpfeeds ^
    --allow tcp:10000 ^
    --description="Allow HPFeeds from Anywhere" ^
    --direction ingress ^
    --target-tags="mhn-admin"
```

![image](https://imgur.com/v5KFQ9W.gif)

Create MHN-Admin VM

> Use these commands:
```http
gcloud compute instances create "mhn-admin" ^
    --machine-type "n1-standard-1" ^
    --subnet "default" ^
    --maintenance-policy "MIGRATE" ^
    --tags "mhn-admin" ^
    --image-family "ubuntu-minimal-1804-lts" ^
    --image-project "ubuntu-os-cloud" ^
    --boot-disk-size "10" ^
    --boot-disk-type "pd-standard" ^
    --boot-disk-device-name "mhn-admin"
```
> Once completed, the user should be able to have SSH access to the MHN-Admin VM

> Make note of mhn-admin internal and external IP

![image](https://user-images.githubusercontent.com/111927957/202284333-51922308-8473-46eb-9c7b-836dcbe4b47e.png)


> Use this command to SSH into MHN-Admin:
```http
gcloud compute ssh mhn-admin
```

> Once inside mhm-admin VM, update and install these packages:

```http
sudo apt update
sudo apt install git python-magic -y
```

> Once complete, cd to opt and mhn then install these package:

```http
cd /opt/
sudo git clone https://github.com/pwnlandia/mhn.git
cd mhn/

sudo sed -i 's/Flask-SQLAlchemy==2.3.2/Flask-SQLAlchemy==2.5.1/g' server/requirements.txt

sudo ./install.sh
```

> The script will run for 20 minutes 
> The user will be asked to a series of values:

- Do you wish to run in Debug mode? y/n : n
- Superuser email: You can use any email -- this will be your username to login to the admin console.
- Superuser password: Choose any password -- you'll be asked to confirm.

> Once complete, accept the default values and answer n for any y/n prompts:

- Server base url ["http://#.#.#.#"]:
- Honeymap url ["http://#.#.#.#:3000"]:
- Mail server address ["localhost"]:
- Mail server port [25]:
- Use TLS for email?: y/n n
- Use SSL for email?: y/n n
- Mail server username [""]:
- Mail server password [""]:
- Mail default sender [""]:
- Path for log file ["/var/log/mhn/mhn.log"]:


- Would you like to integrate with Splunk? (y/n) n
- Would you like to install ELK? (y/n) n
- Would you like to add MHN rules to UFW? (y/n) n

> Once this is complete, the user should be able to use mhn-admin's external IP to log into the admin console.

Dioanaea Honeypot Deployment

Create firewall rules for the honeypot

> Use these commands:

```http
gcloud compute firewall-rules create wideopen ^
    --description="Allow TCP and UDP from Anywhere" ^
    --direction ingress ^
    --priority=1000 ^
    --network=default ^
    --action=allow ^
    --rules=tcp,udp ^
    --source-ranges=0.0.0.0/0 ^
    --target-tags="honeypot"
```

Create Honeypot VM

> Use these commands:

```http
gcloud compute instances create "honeypot-1" ^
    --machine-type "n1-standard-1" ^
    --subnet "default" ^
    --maintenance-policy "MIGRATE" ^
    --tags "honeypot" ^
    --image-family "ubuntu-minimal-1804-lts" ^
    --image-project "ubuntu-os-cloud" ^
    --boot-disk-size "10" ^
    --boot-disk-type "pd-standard" ^
    --boot-disk-device-name "honeypot-1"
```

![image](https://imgur.com/b5O6CJu.gif)

> Once this is complete, the user should be able to ssh to honeypot-1

> Use this command to ssh to honeypot-1:
```http
gcloud compute ssh honeypot-1
```
> Go to the mhn-admin console via external IP
> Deploy tab, choose ubuntu/Raspberry Pi - Dionaea
> Make sure to copy the wget command at the top

![image](https://imgur.com/PUPBYjb.gif)

> Once complete, head over to the honeypot VM and execute the wget command.
> Go to mhn-admin console in the browser, Sensors tab and view sensors.

![image](https://user-images.githubusercontent.com/111927957/202296237-67007e14-8f41-4cc0-ac0b-c8c6daad96c7.png)

Attacking the honeypot

> In Kali, open a terminal
> Use this nmap command to scan the honeypot for open ports/running services:

```http
nmap -A -T4 <honeypot ip>
```
> The results should look like this

![image](https://imgur.com/vi2nTXr.gif)

> In mhn-admin console, under the attack tab, the user should see the attacks report.

![image](https://user-images.githubusercontent.com/111927957/202297455-7ab0e939-9d2c-4f03-adc0-95cacb929bcd.png)

Database Backup (session.json)

> in mhn-admin vm, use this command to export the data in JSON format:

```http
mongoexport --db mnemosyne --collection session > session.json
```

> use this command to copy the data to the user's local machine
- Change "Bill" to the local machine's username

```http
gcloud compute scp mhn-admin:/home/Bill/session.json ./session.json
```








