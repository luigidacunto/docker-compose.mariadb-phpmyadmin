# luigidacunto/docker-compose.mariadb-phpmyadmin

Questa è una base di partenza per la creazione di un Container Docker MariaDB con il supporto dell'interfaccia web PhpMyAdmin. Il file .yml che trovate nel repositorio è già pronto per essere utilizzato, personalizzando semplicemente la password dell'utente root per il DB (se lo desiderate, modificabile ad ogni modo anche successivamente).


## Getting Started

La procedura creerà due container, un MariaDB e un PhpMyAdmin che punterà al primo container per poterlo gestire. Nel file base sono stati definiti i parametri minimi per poter utilizzare tale sistema.

Ovviamente avrai bisogno di aver installato sul tuo sistema il [motore Docker-CE/Docker-EE](https://docs.docker.com/engine/) e il [Docker-Compose](https://docs.docker.com/compose/)

## Installation

```
# mkdir /opt/mariadb-docker
# cd /opt/mariadb-docker
# git clone https://github.com/luigidacunto/docker-compose.mariadb-phpmyadmin.git
```
Ora una volta scaricato il file .yml all'interno del path /opt/mariadb-docker possiamo procedere con la creazione dei container descritti. Possiamo lanciare:
```
# docker-compose -f mariadb-phpmyadmin.1.00.yml up
```
che creerà i due container con i parametri specificati nel file .yml e li farà startare, mostrando a video i log di entrambi i container. I container verranno stoppati interrompendo il processo docker-compose e dovranno essere restartati successivamente (se si vuole che restino up sulla macchina) seguendo il comando:
```
# docker start <container_mariadb_name> <container_phpmyadmin_name>
```
Altrimenti è possibile usare il docker compose facendo in modo che crei soltanto i container senza lo start aggiungendo al comando l'opzione --no-start e lanciando poi l'avvio dei container, in questo modo:
```
# docker-compose -f mariadb-phpmyadmin.hub.yml up --no-start
# docker start <container_mariadb_name> <container_phpmyadmin_name>
```
## Note 
### Utili
Per accedere al Database da riga di comando installare il client, ad esempio (su Debian/Ubuntu):
```
apt install mariadb-client
```
successivamente si potrà utilizzare il classico comando da shell per accedere, specificando nella stringa del client l'host su cui si trova il mariadb-server, ovvero il container docker:
```
mysql -u<user> -p<password> -h <ip_del_container>
```

### Comandi Docker
#### Recuperare l'IP di un Container
Potrebbe essere necessario recuperare l'IP privato del Container che il Docker Engine assegna ad ogni container di default, per poter interagire ad esempio con il DB usando un client da shell. **ATTENZIONE** Gli IP dei container Docker vengono riassegnati randomicamente se non specificati alla creazione del container con un IP statico che sia appartenente alla rete di default del Docker Engine o di una rete virtuale Docker Personalizzata. sarà quindi necessario ogni volta che serve, ricercare l'IP del container se questo è stato restartato per accertarsi a quale IP risponde.
```
# docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <nome_container_mariadb>
172.19.0.2
```
#### Creazione di una classe di rete personalizzata
È possibile creare una classe di rete personalizzata sul Docker Engine, che non vada a interferire con altre classe pre-esistenti o con quella di default che il Docker crea durante la sua inizializzazione (172.17.0.0/16)
```
docker network create --subnet=10.10.0.0/16 my_network
```
### Parametri aggiuntivi da poter specificare 
Si possono aggiungere al file .yml ulteriori parametri di personalizzazione come ad esempio:
#### IP Statici con network personalizzate
Aggiungere per ciascuno dei blocchi relativi ai container (db,phpmyadmin) i seguenti parametri
```
      networks:
        my_network:
          ipv4_address: 10.10.0.<xxx>
```
**Nota** Ciascun container deve avere un IP univoco e non duplicato.
Sostituire la sezione "network:" in fondo al file .yml con quanto segue:
```
networks:
  default:
    external:
      name: my_network
  myPrvNet:
    external: true
```
#### Nome host e nome Dominio ai container
Aggiungere ad uno o più Container una o entrambi i seguenti parametri:
```
  hostname: <host_name>
  domainname: <domain_name.ext>
```
#### Altri Esempi
Nella cartella "examples" si trovani altri file .yml con configurazioni diverse e parametri aggiuntivi.

### Fonti e Documentazione approfondita
* [Docker Documentations](https://docs.docker.com/)
* [Docker Hub - MariaDB](https://hub.docker.com/_/mariadb)
* [Docker Hub - PhpMyAdmin](https://hub.docker.com/r/phpmyadmin/phpmyadmin)