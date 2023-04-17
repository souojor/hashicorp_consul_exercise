# hashicorp_consul_exercise
hashicorp_consul_exercise

docker-compose up -d

docker exec -it consul01 sh

consul agent -dev

consul members

É sempre recomendável ter pelo menos 3 máquinas "server"

curl localhost:8500/v1/catalog/nodes

[
    {
        "ID": "53412d2a-a663-28e5-5f5b-fe686a06671a",
        "Node": "consul01",
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

dig @localhost -p 8600  //há um servidor DNS na porta 8600 no padrão em builds do consul.

;; ANSWER SECTION:
consul01.node.consul.   0       IN      A       127.0.0.1