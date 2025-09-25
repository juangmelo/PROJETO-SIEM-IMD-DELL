# PROJETO-SIEM-IMD-DELL
Implementação de Solução de SIEM Usando Micro Serviços.

Este repositório contém o projeto de implementação de uma solução de 
um laboratório de observalidade baseado em microsserviços e Docker para um cenário de prova de conceito. O projeto foi desenvolvido como parte do módulo 
Monitoramento e Análise de Ameaças.

## Lab 01 (Observabilidade): Implementação de uma infraestrutura para coleta de métricas e logs, visualização e geração de alertas.

### Arquitetura
A infraestrutura do Lab 01 utiliza uma arquitetura de microsserviços baseada em Docker Compose para monitorar métricas e logs de servidores e contêineres.

### Componentes:

* **Node Exporter: Coleta métricas do sistema operacional da máquina host.**

* **cAdvisor: Coleta métricas de uso de recursos dos contêineres.**

* **Prometheus: Servidor que coleta e armazena as métricas do Node Exporter e cAdvisor.**

* **Promtail: Coleta logs do sistema e os envia para o Loki.**

* **Loki: Sistema de armazenamento de logs.**

* **Grafana: Interface de visualização para métricas e logs.**

* **AlertManager: Gerencia e envia alertas disparados pelo Prometheus para um webhook.**

### Pré-requisitos
Certifique-se de que o Docker e o Docker Compose estão instalados e em execução na sua máquina.

### Guia de Implantação (Deploy)
#### 1. Clonar o Repositório
```bash

git clone https://github.com/juangmelo/PROJETO-SIEM-IMD-DELL
cd PROJETO-SIEM-IMD-DELL
```
#### 2. Ajustar Permissões
O contêiner do Grafana precisa de permissões de escrita em sua pasta de dados. Execute o comando abaixo para garantir que tudo funcione corretamente.

```bash

sudo chmod -R 777 ./grafana
```
#### 3. Iniciar a Infraestrutura
Execute o comando abaixo no diretório raiz do projeto. O Docker fará o download das imagens necessárias e iniciará todos os serviços em segundo plano.

```bash

sudo docker compose up -d
```
### Configuração e Uso
#### Acessar as interfaces:

* **Prometheus: http://localhost:9090**

* **Grafana: http://localhost:3000 (Usuário/Senha padrão: admin/admin)**

* **AlertManager: http://localhost:9093**

#### Configurar fontes de dados no Grafana:
Ao acessar o Grafana, você deve conectar o Prometheus e o Loki como fontes de dados.

* **Prometheus: Vá em Connections > Add new connection > Prometheus. Na URL, insira http://prometheus:9090 e clique em Save & test.**

* **Loki: Vá em Connections > Add new connection > Loki. Na URL, insira http://loki:3100 e clique em Save & test.**

#### Ajustar URL do Webhook:
Lembre-se de que a URL do Webhook no arquivo alertmanager/config.yml é específica para cada usuário. Cada membro da equipe deve substituir a URL pela sua própria para testar o recebimento dos alertas.

#### Visualizar métricas e logs:
No Grafana, você pode criar painéis para visualizar as métricas do Prometheus (por exemplo, node_cpu_seconds_total) e os logs do Loki (por exemplo, {job="varlogs"}).

#### Simulação de Anomalia e Alerta
Para demonstrar o funcionamento do sistema de alerta, o nosso arquivo de configuração de alertas simula um alto uso de CPU, que dispara uma notificação via webhook.

#### Simular anomalia:
No terminal da máquina host, execute o comando abaixo para forçar o uso de CPU.

```bash

while true; do :; done &
```
#### Verificar o alerta:

Após cerca de 1 minuto, verifique a interface do AlertManager em http://localhost:9093 para ver o status do alerta como "FIRING".

Acesse a URL do webhook configurado no alertmanager/config.yml para confirmar o recebimento da notificação.

#### Finalizar a simulação:
Para parar o processo, use o comando kill com o ID do processo que foi retornado.

```bash

kill <ID_do_processo>
```

### Demonstração de um caso de uso do laboratório de observalidade.

 [![Demonstração da Simulação](https://img.youtube.com/vi/uDn3Wtw2nBs/0.jpg)](https://www.youtube.com/watch?v=uDn3Wtw2nBs)


-----

# Projeto Final de SIEM com Wazuh e Shuffle

Este projeto tem como objetivo principal a criação de uma infraestrutura completa de **SIEM (Security Information and Event Management)** utilizando as ferramentas de código aberto Wazuh e Shuffle. A infraestrutura é construída e gerenciada inteiramente com **Docker Compose** para garantir a portabilidade e a facilidade de configuração.

## Objetivos do Laboratório

* **Criação da Infraestrutura:** Montar um sistema SIEM completo com Wazuh e Shuffle.
* **Simulação de Eventos:** Simular um evento de segurança (ataque de força bruta).
* **Análise com IA:** Utilizar um modelo de IA para analisar e gerar uma resposta inteligente ao evento detectado.

## Requisitos Técnicos

* Toda a infraestrutura é baseada em **Docker Compose**.
* O Wazuh é configurado no modo **Single Node**.
* Os contêineres devem estar na mesma rede para garantir a comunicação entre os serviços e o host.
* É obrigatória a coleta de informações através de um **agente do Wazuh**.
* Um **workflow no Shuffle** deve ser criado para coletar os alertas do Wazuh e acionar o modelo de IA para análise.

---

## Passo a Passo da Instalação e Simulação

### Passo 1: Instalação do Wazuh

1.  Ajuste o valor do `max_map_count` no seu sistema host (Linux):
    ```bash
    sudo sysctl -w vm.max_map_count=262144
    ```

2.  Clone o repositório oficial do Wazuh, utilizando a branch `v4.12.0` conforme o guia.
    ```bash
    git clone [https://github.com/wazuh/wazuh-docker.git](https://github.com/wazuh/wazuh-docker.git) -b v4.12.0
    ```

3.  Acesse a pasta do modo single-node:
    ```bash
    cd wazuh-docker/single-node
    ```

4.  Rode o comando para gerar os certificados necessários:
    ```bash
    docker compose -f generate-indexer-certs.yml run --rm generator
    ```

5.  Edite o arquivo `docker-compose.yml` e altere a porta do host de `9200` para `9100`:
    ```yaml
    "9100:9200"
    ```

6.  Suba o ambiente com Docker Compose:
    ```bash
    docker compose up -d
    ```
Após a inicialização (cerca de 1 minuto), acesse o dashboard do Wazuh no browser usando `https://localhost`. As credenciais padrão são: `Login: admin` e `Senha: SecretPassword`.

### Passo 2: Instalação do Agente Wazuh

1.  No painel do Wazuh, vá para **`Agents management`** e depois em **`Summary`**.
2.  Clique em **`Deploy new agent`** e siga as instruções para o seu sistema operacional.

### Passo 3: Instalação do Shuffle

1.  Clone o repositório oficial do Shuffle:
    ```bash
    git clone [https://github.com/Shuffle/Shuffle](https://github.com/Shuffle/Shuffle)
    cd Shuffle
    ```

2.  Altere as linhas `55`, `56` e `57` no arquivo `docker-compose.yml`:
    ```yaml
    SHUFFLE_STATS_DISABLED=false
    SHUFFLE_LOGS_DISABLED=false
    #- SHUFFLE_SWARM_CONFIG=run
    ```

3.  Conceda as permissões necessárias para a pasta do banco de dados do Shuffle:
    ```bash
    sudo chown 1000:1000 -R shuffle-database
    ```

4.  Suba o ambiente com Docker Compose:
    ```bash
    docker compose up -d
    ```
Após a inicialização, acesse o Shuffle no seu navegador usando `http://<seu_ip>:3001`.

### Passo 4: Integração do Wazuh com o Shuffle

1.  Acesse o contêiner do Wazuh Manager:
    ```bash
    docker exec -it single-node-wazuh.manager-1 bash
    ```

2.  Instale o editor de texto nano:
    ```bash
    dnf install -y nano
    ```

3.  Abra o arquivo de configuração `ossec.conf`:
    ```bash
    nano /var/ossec/etc/ossec.conf
    ```

4.  Adicione o seguinte bloco de código em qualquer parte do arquivo, alterando a URL do `hook_url` com a URI do Webhook que você criou no Shuffle:
    ```xml
    <integration>
      <name>shuffle</name>
      <hook_url>http://<seu_ip_do_shuffle>:3001/api/v1/hooks/webhook_ID</hook_url>
      <level>10</level>
      <alert_format>json</alert_format>
    </integration>
    ```

5.  Salve e saia do arquivo, depois reinicie o contêiner do Wazuh Manager para aplicar as alterações:
    ```bash
    exit
    docker restart single-node-wazuh.manager-1
    ```

### Passo 5: Instalação do SSH e Hydra

1.  Atualize os pacotes e instale o SSH e o Hydra na sua VM:
    ```bash
    sudo apt update
    sudo apt install -y ssh hydra
    ```

2.  Inicie o serviço do SSH:
    ```bash
    sudo /etc/init.d/ssh start
    ```

### Passo 6: Simulação de Ataque de Força Bruta

1.  Crie um arquivo de texto com senhas falsas:
    ```bash
    touch senhas.txt
    ```

2.  Preencha o arquivo `senhas.txt` com várias senhas falsas, uma por linha.

3.  Simule o ataque ao SSH usando o Hydra com o usuário `badguy`:
    ```bash
    sudo hydra -l badguy -P senhas.txt <IP_DA_SUA_VM> ssh
    ```

### Passo 7: Resultados e Análise

1.  **No Wazuh:** Acesse **`Explore > Discover`** e pesquise por `"badguy"` para ver os alertas gerados pelo ataque.
2.  **No Shuffle:** Verifique as execuções do workflow para ver se os alertas foram recebidos e processados.
3.  **Análise com IA:** Observe o `Output IA` nas execuções do workflow para ver a resposta inteligente gerada pelo modelo Llama2.

---

## Desinstalação

Para remover os contêineres e volumes criados, execute os seguintes comandos:

**Wazuh:**
```bash
docker compose down
docker volume rm $(docker volume ls -q -f name=single-node)
```

**Shuffle:**
```bash
docker compose down
docker volume rm $(docker volume ls -q -f name=shuffle)
```

Se houver containers de worker, você pode pará-los com:
```bash
docker stop $(docker ps -q --filter "name=worker-")
```

### Demonstração de um caso de uso do Projeto Final.

[![Demonstração da Simulação](https://img.youtube.com/vi/fam5Zf5RLCs/0.jpg)](https://www.youtube.com/watch?v=fam5Zf5RLCs&feature=youtu.be)
