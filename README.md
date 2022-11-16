# Project Honeypot

Use Google Cloud's Platform Free Tier
> download and install GCP SDK


MHN Admin Deployment
> First set up firewall rules for MHN Admin

Use these commands:
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

Dioanaea Honeypot Deployment



Database Backup (session.json)
