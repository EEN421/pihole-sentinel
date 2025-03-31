# pihole-sentinel

A simple Python script and cron task to push Pi-Hole's FTL query log into Azure Sentinel.

It copies the FTL database to /tmp (otherwise Pi-Hole can stop writing while read operations are in progress), and transforms the queries to Azure Sentinel Information Model [(ASIM)](https://docs.microsoft.com/en-us/azure/sentinel/dns-normalization-schema). Processed logs appear in the `Normalized_CL` table in the Log Analytics workspace.

There's also an example Analytic rule, which creates an incident when Failures occur (for example, if you use Quad9, it'll return NXDOMAIN for blocked domains).



## Setup

You will need:

- To run the below as the pihole user (or whatever user pihole runs as)
- A working Sentinel instance
- the Workspace ID and Secret Key that your Sentinel instance is attached to

```bash

cd /opt
git clone https://github.com/EEN421/pihole-sentinel.git
cd pihole-sentinel
sudo python3 -m venv .env
sudo chown -R $USER:$USER .env
source .env/bin/activate
pip install -r requirements.txt
echo 'AZURE_WORKSPACE_ID = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"' | sudo tee local_settings.py
echo 'AZURE_SECRET_KEY = " xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=="' | sudo tee -a local_settings.py


touch /var/log/pihole-sentinel.log
sudo chown pihole:pihole /var/log/pihole-sentinel.log

echo '* * * * * pihole /opt/pihole-sentinel/cron.sh >> /var/log/pihole-sentinel.log 2>&1' | sudo tee /etc/cron.d/pihole-sentinel

```
<br/>
<br/>

&#x1F449; Check out the [Sentinel Workbook](https://github.com/EEN421/pihole-sentinel/blob/main/piholeWorkbook_GalleryTemplate.json) and [Analytics Rule](https://github.com/EEN421/pihole-sentinel/blob/main/Azure_Sentinel_analytic_rule.json) included above to hunt through your DNS queries and protect your network like a pro! 🔥
