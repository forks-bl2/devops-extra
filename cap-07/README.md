# Capítulo 7

## Configuração do Ambiente

### Instalação k0s

Baixar executável:
```
curl -sSLf https://get.k0s.sh | sudo sh
```

Criar cluster com um único nó:
```
sudo k0s install controller --single
```

Executar o cluster:
```
sudo k0s start
```

Usar o kubectl do próprio k0s:
```
sudo k0s kubectl COMANDO_DO_KUBECTL
```

Exemplo para listagem de nós do cluster:
```
sudo k0s kubectl get nodes
```

### Instalação do kubectl

Instalar dependências necessárias:
```
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

Baixar chave GPG:
```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

Acrescentar repositório do kubectl:
```
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Instalar kubectl
```
sudo apt-get update
sudo apt-get install -y kubectl
```

### Criação de usuário no cluster para acesso pelo kubectl

Criar arquivo de configuração para um determinado usuário:
```
sudo k0s kubeconfig create --groups "system:masters" NOME_USUARIO > k0s.config
```

Criar usuário no cluster com papel de admin:
```
sudo k0s kubectl create clusterrolebinding NOME_USUARIO-binding --clusterrole=admin --user=NOME_USUARIO > k0s.config
```

Mover arquivo para localização padrão:

```
mv k0s.config $HOME/.kube/config
```

Definir contexto utilizado:
```
kubectl config use-context k0s
```

Ver contexto atual:
```
kubectl config current-context
```

### Criação do Registry do Docker

Rodar container:
```
docker run -d -p 5005:5000 --restart=always --name registry registry:2
```

Abrir no editor de texto o arquivo /etc/hosts:
```
nano /etc/hosts
```

Adicionar registry-local na linha do localhost (127.0.1.1):
```
127.0.1.1 nome-da-maquina registry-local
```

Testar registro:
curl registry-local:5005/v2/


Gerar configuração padrão do containerd:
sudo containerd config default > containerd.toml

Alterar o arquivo para que fique assim:
```
root = "/var/lib/k0s/containerd" state = "/var/lib/k0s/run/containerd"
...
address = "/var/lib/k0s/run/containerd.sock"
```

Acrecentar o trecho abaixo logo após `endpoint = ["https://registry-1.docker.io"]`:

```
[plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry-local:5005"]
          endpoint = ["http://registry-local:5005"]
        [plugins."io.containerd.grpc.v1.cri".registry.configs]
          [plugins."io.containerd.grpc.v1.cri".registry.configs."registry-local:5005".tls]
            insecure_skip_verify = true
```

Mover arquivo para diretório de configuração do k0s
```
sudo mkdir -p /etc/k0s/
sudo mv containerd.toml /etc/k0s/
```

Reiniciar serviço do k0s:
```
sudo service k0scontroller stop
sudo service k0scontroller start
```

Verificar status:
```
sudo service k0scontroller status
```


### Instalação do Skaffold

Baixar e instalar o skafold:
```
curl -Lo skaffold https://storage.googleapis.com/skaffold/releases/latest/skaffold-linux-amd64 && sudo install skaffold /usr/local/bin/
```

Checar versão:
```
skaffold version
```

Definir registry padrão, e aceitar modo inseguro:
```
skaffold config set --global default-repo registry-local:5005
skaffold config set --global insecure-registries registry-local:5005
```
Verificar configurações:
```
skaffold config list
```

### Instalação do Helm

Instalar helm:

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

Checar versão:
```
helm version
```

### Instalação do Terraform:

Adicionar chave GPG:
```
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
```

Adicionar repositório do Terraform:

```
sudo sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
```

Instalar Terraform:
```
sudo apt-get update && sudo apt-get install terraform
```

Verificar versão:

```
terraform -v
```

## Kubernetes

### Criação do primeiro deployment

Implantar o deployment:
```
kubectl apply -f cap-7/arquivos/kubernetes/primeiro-deployment.yaml
```

Verificar que foi implantado:
```
kubectl get deployments
```

Verificar pods criados:
```
kubectld get pods
```

### Criação do primeiro service

Implantar o service:
```
kubectl apply -f cap-07/arquivos/kubernetes/primeiro-service.yaml
```

Verificar service:
```
kubectl get services
```

Mapear porta do service para porta da máquina:

```
kubectl port-forward service/aplicacao-a 8888:80
```

### Criação do primeiro ingress

Implantar o ingress:
```
kubectl apply -f cap-07/arquivos/kubernetes/primeiro-ingress.yaml
```

Verificar ingress:
```
kubectl get ingress
```

### Implantação do NGINX Ingress

Adicionar repositório Helm:
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```
Instalar o ingress NGINX
```
helm install ingress-nginx ingress-nginx/ingress-nginx 
```

Obter portas do nó:
```
kubectl describe service/ingress-nginx-controller   | grep NodePort
```

Editar /etc/localhost
```
127.0.1.1 nome-da-maquina registry-local meuservico.ufscar.br
```

Acessar no navegador:
```
http://meuservico.ufscar.br/app-a
```



## Helm

Criar chart:

```
helm create chart
```

Implantar o chart:

```
helm install minha-aplicacao chart
```

Obter informações:
```
helm get all minha-aplicacao   
```

Desinstalar:

```
helm uninstall minha-aplicacao
```

## Jib

Adicionar na seção properties:

```xml
<jib.maven-plugin-version>3.1.4</jib.maven-plugin-version>
```

Adicionar na seção plugins:
```xml
<plugin>
	<groupId>com.google.cloud.tools</groupId>
	<artifactId>jib-maven-plugin</artifactId>
	<version>${jib.maven-plugin-version}</version>

	<configuration>
		<from>
			<image>registry-local:5005/loja-virtual-base</image>
		</from>
		<to>
			<image>registry-local:5005/loja-virtual</image>
		</to>
		<allowInsecureRegistries>true</allowInsecureRegistries>
		<container>
			<entrypoint>catalina.sh</entrypoint>
			<args>run</args>
			<jvmFlags>
				<jvmFlag>-Djava.security.egd=file:/dev/./urandom</jvmFlag>
			</jvmFlags>
			<appRoot>/usr/local/tomcat/webapps/devopsnapratica</appRoot>
			<creationTime>USE_CURRENT_TIMESTAMP</creationTime>
		</container>
	</configuration>
</plugin>
```

Alterar o Dockerfile do Tomcat e comentar o trecho a seguir:

```dockerfile
ADD devopsnapratica.war /usr/local/tomcat/webapps/
```

Gerar a imagem com o comando:

```
docker build -t registry-local:5005/loja-virtual-base .
```


Publicar a imagem no registry local (registry-local:5005):

```
docker push registry-local:5005/loja-virtual-base
```

Gerar imagem com o comando:

```
mvn compile package com.google.cloud.tools:jib-maven-plugin:3.2.1:build 
```

Obter a imagem do registry:

```
docker pull registry-local:5005/loja-virtual
```