1) Fazendo uso da imagem padrão.
```
	docker run -p 80:80 jasonrivers/nagios
```

2) Inicializar ambiente de produção

	Servidor de Banco de Dados
	```
		cd ~/temp/devops-extra/cap-02/docker-mysql-image
		docker run --name mysql-server -v data:/var/lib/mysql -p 3306:3306 mysql-server-img
	```
	Servidor Web
	```
		cd ~/temp/devops-extra/cap-02/docker-tomcat-image
		docker run --name tomcat-server -p 8080:8080 tomcat-server-img
	```

3) Verificar IPs dos servidores
```
	docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mysql-server

	docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' tomcat-server
```

4) Criar arquivos de configuração do Nagios

	Arquivo loja-virtual.cfg

```
# HOST DEFINITION
define host{
        use				linux-server
        host_name 			mysql-server
        alias				mysql-server
        address				172.17.0.2
        }

define host{
        use				linux-server
        host_name 			tomcat-server
        alias				tomcat-server
        address				172.17.0.3
        }
```

5) Editar o arquivo nagios.cfg

```
	cfg_file=/opt/nagios/etc/objects/loja_virtual.cfg
```

6) Construir a imagem atualizada

```
	docker build -t nagios-server-img .
```

7) Invocar o contêiner com disco compartilhado com as configurações desejadas

```
	cd ~/temp/devops-extra/cap-03/docker-nagios-image/
	docker run --name nagios-server -v /home/auri/temp/devops-extra/cap-03/docker-nagios-image/lojacfg/:/opt/nagios/etc/objects/lojacfg/ -p 80:80 nagios-server-img
```

8) Acessar o servidor de monitoramento Nagios 
	
	http://localhost:80/

9) Conectar no prompt do servidor em execução

```
	cd ~/temp/devops-extra/cap-03/docker-nagios-image/
	docker exec -it nagios-server /bin/bash
```
