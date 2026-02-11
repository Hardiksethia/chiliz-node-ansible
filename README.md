 Overview
This project deploys a Chiliz CCV2 Geth node using:
Docker & Docker Compose


Nginx reverse proxy


Ansible roles


Ngrok for public RPC exposure


Prometheus metrics enabled
The node exposes:
JSON-RPC on /rpc


Metrics on /metrics


Public HTTPS endpoint via ngrok
Architecture
Internet
   │
   ▼
Ngrok (HTTPS)
   │
   ▼
Nginx (Port 80)
   ├── /rpc     →  localhost:8545
   └── /metrics →  localhost:6060/debug/metrics/prometheus
   │
   ▼
Docker Container (chilizchain/ccv2-geth)



Deployment Steps

Step 1 — Clone Repository
git clone https://github.com/YOUR_USERNAME/chiliz-node-ansible.git
cd chiliz-node-ansible


Step 2 — Run Docker Node
cd /opt/chiliz
sudo docker compose up -d

Check:
docker ps


Step 3 — Test Local RPC
curl -X POST http://localhost:8545 \
-H "Content-Type: application/json" \
--data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'


Step 4 — Configure Nginx (Ansible Role)
Run:
ansible-playbook -i inventory/hosts.ini nginx.yml

This deploys:
/etc/nginx/sites-available/chiliz.conf


Enables reverse proxy


Restarts nginx



Step 5 — Test Reverse Proxy
curl -X POST http://localhost/rpc \
-H "Content-Type: application/json" \
--data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'

Metrics:
curl http://localhost/metrics


Step 6 — Expose with Ngrok
ngrok http 80

Example public URL:
https://crissy-committable-edmund.ngrok-free.dev


Step 7 — Test Public RPC
curl -X POST https://YOUR-NGROK-URL/rpc \
-H "Content-Type: application/json" \
--data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'


Monitoring
Metrics endpoint:
http://localhost:6060/debug/metrics/prometheus

Via Nginx:
http://localhost/metrics


Ansible Structure
roles/
 ├── common/
 ├── chiliz/
 └── nginx/
     ├── defaults/
     ├── handlers/
     ├── tasks/
     └── templates/

Inventory:
inventory/hosts.ini

Example:
[chiliz]
localhost ansible_connection=local




