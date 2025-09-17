# PROJETO-SIEM-IMD-DELL
Implementação de Solução de SIEM Usando Micro Serviços
Esse arquivo README.md a seguir contém:
 
Uma breve introdução ao projeto.
 
Os pré-requisitos técnicos.
 
Um guia de instalação e inicialização (Getting Started).
 
Instruções para a simulação do evento.
 
# Projeto de Observabilidade: SIEM
 
Este repositório contém uma infraestrutura de observabilidade completa, construída com Docker Compose. O projeto é capaz de coletar métricas e logs de uma máquina host e dos próprios contêineres, além de visualizar os dados e gerar alertas em tempo real.
 
## Requisitos
 
Antes de começar, certifique-se de que sua máquina atende aos seguintes requisitos:
 
* **Docker**
* **Docker Compose Plugin** (versão 1.29.0 ou superior, integrada ao Docker CLI)
 
## Primeiros Passos
 
Siga estes passos para iniciar a infraestrutura de observabilidade.
 
### 1. Clonar o Repositório
 
```bash
##git clone <https://github.com/juangmelo/PROJETO-SIEM-IMD-DELL.git>
##cd <PROJETO-SIEM-IMD-DELL>
2. Verificar a Estrutura do Projeto
Confirme se os arquivos e diretórios estão na estrutura correta. O seu diretório principal deve conter as seguintes pastas e arquivos:
 
.
├── docker-compose.yaml
├── prometheus
│   ├── prometheus.yml
│   └── alerts.yml
├── grafana
├── alertmanager
│   └── config.yml
├── loki
│   └── config.yaml
└── promtail
    └── promtail-config.yaml
    
3. Ajustar Permissões (Obrigatório)
O contêiner do Grafana precisa de permissões de escrita em sua pasta de dados. Execute o comando abaixo para garantir que tudo funcione corretamente.
 
Bash
 
sudo chmod -R 777 ./grafana
4. Iniciar os Contêineres
Execute o comando abaixo no diretório raiz do projeto. O Docker fará o download das imagens necessárias e iniciará todos os serviços em segundo plano.
 
Bash
 
sudo docker compose up -d
5. Verificar o Status dos Serviços
Confirme se todos os serviços estão em execução.
 
Bash
 
sudo docker compose ps
Se todos os serviços (prometheus, node-exporter, cadvisor, loki, promtail, grafana, alertmanager) estiverem com o status Up, sua infraestrutura está pronta.
 
Acessando as Interfaces
Você pode acessar os serviços nos seguintes endereços:
 
Grafana: http://localhost:3000 (Login inicial: admin/admin)
 
Prometheus: http://localhost:9090
 
Alertmanager: http://localhost:9093
 
Simulação de Evento
Para testar o sistema de alertas, vamos simular um evento de alto uso de CPU na máquina host.
 
Instale a ferramenta stress:
 
Bash
 
sudo apt-get update && sudo apt-get install stress -y
Force o uso da CPU: Este comando sobrecarregará todos os núcleos de CPU da sua máquina por 2 minutos.
 
Bash
 
stress --cpu $(nproc) --timeout 120s
Verifique o Alerta: Após cerca de 1 minuto, o Prometheus detectará o alto uso de CPU, disparará um alerta para o Alertmanager, que enviará uma notificação para o webhook configurado. Verifique a URL do seu webhook para confirmar a notificação.
