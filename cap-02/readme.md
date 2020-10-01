# Organização do Capítulo 2

- Imagens do servidor de banco de dados e do servidor Web

- Alterar status do Docker Daemon

sudo update-rc.d docker defaults   # to add
sudo update-rc.d docker disable    # to disable but leave init script available to later
sudo update-rc.d docker enable     # to enable
sudo update-rc.d docker remove     # to remove


- Verificar o status de todos os serviços em execução

sudo service --status-all


https://aurimrv.gitbook.io/pratica-devops-com-docker/
