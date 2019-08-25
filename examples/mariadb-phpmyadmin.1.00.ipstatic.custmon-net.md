# mariadb-phpmyadmin.1.00.ipstatic.custmon-net.yml

In this example i used a custom network named "MyPrvNet" set as 172.18.0.0/16. This docker  command was used to create this network, before launch docker-compose.

```
docker network create --subnet=172.18.0.0/16 myPrvNet
```