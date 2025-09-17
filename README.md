PROJETO-SIEM-IMD-DELL - Implementação de Solução de SIEM Usando Microsserviços
Este repositório contém o projeto de implementação de uma solução de um laboratório de observalidade baseada em microsserviços e Docker para um cenário de prova de conceito. O projeto foi desenvolvido como parte do módulo Monitoramento e Análise de Ameaças.

Lab 01 (Observabilidade): Implementação de uma infraestrutura para coleta de métricas e logs, visualização e geração de alertas.

Lab 01 - Infraestrutura de Observabilidade
Arquitetura
A infraestrutura do Lab 01 utiliza uma arquitetura de microsserviços baseada em Docker Compose para monitorar métricas e logs de servidores e contêineres.

Componentes:

Node Exporter: Coleta métricas do sistema operacional da máquina host.

cAdvisor: Coleta métricas de uso de recursos dos contêineres.

Prometheus: Servidor que coleta e armazena as métricas do Node Exporter e cAdvisor.

Promtail: Coleta logs do sistema e os envia para o Loki.

Loki: Sistema de armazenamento de logs.

Grafana: Interface de visualização para métricas e logs.

AlertManager: Gerencia e envia alertas disparados pelo Prometheus para um webhook.

Pré-requisitos
Certifique-se de que o Docker e o Docker Compose estão instalados e em execução na sua máquina.

Guia de Implantação (Deploy)
Clone o repositório:

Bash

git clone https://github.com/juangmelo/PROJETO-SIEM-IMD-DELL
cd PROJETO-SIEM-IMD-DELL
Ajustar Permissões (Obrigatório)
O contêiner do Grafana precisa de permissões de escrita em sua pasta de dados. Execute o comando abaixo para garantir que tudo funcione corretamente.

Bash

sudo chmod -R 777 ./grafana
Inicie a infraestrutura:
Execute o comando abaixo para iniciar todos os serviços definidos no docker-compose.yaml. As pastas de dados serão criadas automaticamente.

Bash

sudo docker compose up -d
Configuração e Uso
Acesse as interfaces:

Prometheus: http://localhost:9090

Grafana: http://localhost:3000 (Usuário/Senha padrão: admin/admin)

AlertManager: http://localhost:9093

Configurar fontes de dados no Grafana:
Ao acessar o Grafana, você deve conectar o Prometheus e o Loki como fontes de dados.

Prometheus: Vá em Connections > Add new connection > Prometheus. Na URL, insira http://prometheus:9090 e clique em Save & test.

Loki: Vá em Connections > Add new connection > Loki. Na URL, insira http://loki:3100 e clique em Save & test.

Ajustar URL do Webhook
Lembre-se de que a URL do Webhook no arquivo alertmanager/config.yml é específica para cada usuário. Você deve substituir a URL pela sua própria para testar o recebimento dos alertas.

Visualizar métricas e logs:
No Grafana, você pode criar painéis para visualizar as métricas do Prometheus (por exemplo, node_cpu_seconds_total) e os logs do Loki (por exemplo, {job="varlogs"}).

Simulação de Anomalia e Alerta
Para demonstrar o funcionamento do sistema de alerta, o projeto simula um alto uso de CPU, que dispara uma notificação via webhook.

Simular anomalia:
No terminal da máquina host, execute o comando abaixo para forçar o uso de CPU.

Bash

while true; do :; done &
Verificar o alerta:

Após cerca de 1 minuto, verifique a interface do AlertManager em http://localhost:9093 para ver o status do alerta como "FIRING".

Acesse a URL do webhook configurado no alertmanager/config.yml para confirmar o recebimento da notificação.

Finalizar a simulação:
Para parar o processo, use o comando kill com o ID do processo que foi retornado.

Bash

kill <ID_do_processo>
