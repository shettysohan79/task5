# task5
Step 1 — Add Elastic repo and install ELK

bash
sudo apt install apt-transport-https -y
bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
bash
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
bash
sudo apt update
bash
sudo apt install elasticsearch kibana logstash -y
bash
sudo systemctl enable --now elasticsearch
bash
sudo systemctl enable --now kibana

Open Firefox in Kali:

http://localhost:5601

Step 2 — Create Logstash config file

bash
sudo nano /etc/logstash/conf.d/auth.conf

Paste this inside the editor:

input { file { path => "/var/log/auth.log" start_position => "beginning" } }
output { elasticsearch { hosts => ["localhost:9200"] index => "authlog-%{+YYYY.MM.dd}" } }

Save and exit: Ctrl+O, Enter, Ctrl+X

bash
sudo systemctl restart logstash

Step 3 — Simulate attack to detect in Kibana

bash
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt ssh://192.168.56.101

Then in Kibana (http://localhost:5601) → Discover → select index authlog-* → search Failed password

Step 4 — Contain the “attacker”

bash
sudo iptables -A INPUT -s 10.10.3.15 -j DROP
