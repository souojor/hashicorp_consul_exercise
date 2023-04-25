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

