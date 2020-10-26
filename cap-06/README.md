Para a compilação e geração do war da Loja Virtual, é possível simplesmente utilizar uma imagem Docker com maven e jdk 1.8

Basicamente, basta compartilhar a pasta local da loja virtual com o contêiner

```
docker run --rm -it -v /home/auri/temp/devops-extra/cap-05/maven-jdk/loja-virtual-devops:/home/loja-virtual-devops mcr.microsoft.com/java/maven:8-zulu-debian9 mvn -f /home/loja-virtual-devops/pom.xml clean package
```

https://<seu_github_username>@github.com/<seu_github_group>/<seu_github_project>.git

https://aurimrv@github.com/aurimrv/loja-virtual-devops.git
