# hashicorp_consul_exercise
hashicorp_consul_exercise

docker-compose up -d

docker exec -it consulserver01 sh

consul agent -dev

consul members

� sempre recomend�vel ter pelo menos 3 m�quinas "server"

curl localhost:8500/v1/catalog/nodes

[
    {
        "ID": "53412d2a-a663-28e5-5f5b-fe686a06671a",
        "Node": "consulserver01",
        "Address": "127.0.0.1",
        "Datacenter": "dc1",
        "TaggedAddresses": {
            "lan": "127.0.0.1",
            "lan_ipv4": "127.0.0.1",
            "wan": "127.0.0.1",
            "wan_ipv4": "127.0.0.1"
        },
        "Meta": {
            "consul-network-segment": ""
        },
        "CreateIndex": 11,
        "ModifyIndex": 12
    }
]



apk -U add bind-tools   //para instalar o comando dig

dig @localhost -p 8600  //h� um servidor DNS na porta 8600 no padr�o em builds do consul.

;; ANSWER SECTION:
consul01.node.consul.   0       IN      A       127.0.0.1

Criado o consulserver01, consulserver02 e consulserver03

docker exec -it consulserver01 sh

ifconfig
172.20.0.4

mkdir /etc/consul.d
mkdir /var/lib/consul

consul agent -server -bootstrap-expect=3 -node=consulserver01 -bind=172.20.0.4 -data-dir=/var/lib/consul -config-dir=/etc/consul.d

# consul members
Node            Address          Status  Type    Build    Protocol  DC   Segment
consulserver01  172.20.0.4:8301  alive   server  1.10.12  2         dc1  <all>

172.20.0.3
consul agent -server -bootstrap-expect=3 -node=consulserver02 -bind=172.20.0.3 -data-dir=/var/lib/consul -config-dir=/etc/consul.d

consul join 172.20.0.3

# consul members
Node            Address          Status  Type    Build    Protocol  DC   Segment
consulserver01  172.20.0.4:8301  alive   server  1.10.12  2         dc1  <all>
consulserver02  172.20.0.3:8301  alive   server  1.10.12  2         dc1  <all>

172.20.0.2
consul agent -server -bootstrap-expect=3 -node=consulserver03 -bind=172.20.0.2 -data-dir=/var/lib/consul -config-dir=/etc/consul.d

consul join 172.20.0.2
# consul members
Node            Address          Status  Type    Build    Protocol  DC   Segment
consulserver01  172.20.0.4:8301  alive   server  1.10.12  2         dc1  <all>
consulserver02  172.20.0.3:8301  alive   server  1.10.12  2         dc1  <all>
consulserver03  172.20.0.2:8301  alive   server  1.10.12  2         dc1  <all>

Criamos o consulclient01 e seu volume;

docker exec -it consulclient01 sh
mkdir /var/lib/consul
consul agent -bind=172.18.0.2 -data-dir=/var/lib/consul -config-dir=/etc/consul.d
consul join 172.20.0.2

criamos o service.json

consul reload

# 2023-04-26T15:36:59.215Z [INFO]  agent: Synced service: service=nginx

# dig @localhost -p 8600 nginx.service.consul

; <<>> DiG 9.16.39 <<>> @localhost -p 8600 nginx.service.consul
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5695
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;nginx.service.consul.          IN      A

;; ANSWER SECTION:
nginx.service.consul.   0       IN      A       172.18.0.2

;; Query time: 0 msec
;; SERVER: 127.0.0.1#8600(127.0.0.1)
;; WHEN: Wed Apr 26 15:39:39 UTC 2023
;; MSG SIZE  rcvd: 65

# curl localhost:8500/v1/catalog/services  #smostra os sevi�os cadastrados
# consul catalog nodes -service nginx
Node            ID        Address     DC
consulclient01  68bc2e31  172.18.0.2  dc1

Criamos o consulclient02 e o novo service nginx2

docker exec -it consulclient02 sh
mkdir /var/lib/consul
consul agent -bind=172.20.0.6 -data-dir=/var/lib/consul -config-dir=/etc/consul.d -retry-join=172.20.0.3 -retry-join=172.20.0.4

dig @localhost -p 8600 nginx.service.consul

;; ANSWER SECTION:
nginx.service.consul.   0       IN      A       172.20.0.6
nginx.service.consul.   0       IN      A       172.20.0.5

Adicionando os checks no services.json

apk add nginx

nginx

apk add vim

mkdir /usr/share/nginx/html -p
vim /etc/nginx/http.d/default.conf

para evitar o 404 e a falha do check, retiramos o trecho location return 404 e adicionamos:
root /usr/share/nginx/html;

criar o index.html no diretório acima.

nginx -s reload

curl localhost

O check deve ter voltado a funcionar e o serviço estar disponível no dig @localhost -p 8600 nginx.service.consul

Criamos o server.json para facilitar subir os servers.

Após configurado o server.json, para iniciar:
consul agent -config-dir=/etc/consul.d

Encriptação:
consul keygen
giWM1bQtzNeqjfjbIbbEY+iGsdWVHVPPiUohbPsmFrU=
adicionamos nos jsons dos servers.

verificar se há criptografia:
apk -U add bind-tools
apk -U add tcpdump

tcpdump -i eth0 -an port 8301 -A

User interface do Consul foi declarada no server.json do server01:

http://localhost:8500/ui



