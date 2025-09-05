# Relatório-de-Exploração-e-Correção-de-Vulnerabilidades-MQTT-Broker
Esse relatório documenta a atividade de exploração e correção de vulnerabilidades em um broker MQTT. Envolvendo a clonagem de um repositório GitHub, construção de containers Docker, configuração de um broker MQTT, envio de mensagens, teste de vulnerabilidades e implementação de controles de acesso para mitigar as vulnerabilidades.

# Disciplina: Pentest em Aplicações Web

Professor: Hebert de Oliveira Silva

Aluno: Bruno Martiliano Ferreira da Silva

Data: 03/09/2025 à 05/09/2025

# 1.0. Passo a Passo para e Realização das Tarefas

## 2.0. Clonar o repositório https://github.com/csai4cps/mqtt-projec.
<img width="903" height="186" alt="image" src="https://github.com/user-attachments/assets/acfe52cb-69f8-46b7-84c9-dd16dc42bfd5" />

Essa evidência mostra o processo de clonagem de um repositório Git chamado "mqtt-project" do GitHub para o diretório local em minha máquina Debian, utilizando o comando git clone, sendo executado como usuário "root".

## 3.0. Construindo os containers, depois da clonagem do repositório do GitHub.
<img width="1015" height="272" alt="image" src="https://github.com/user-attachments/assets/3ace6188-66fd-427a-b89a-b0d756eea94a" />
<img width="953" height="412" alt="image" src="https://github.com/user-attachments/assets/6c3e614f-bafe-4837-857a-a7ce6a3bad14" />

Essas evidências mostram o processo de construção de uma imagem Docker e a execução de contêineres no diretório mqtt-project em minha máquina Debian, utilizando o comando docker compose up -d --build.

## 4.0. Acessando o MQTT Explorer, com o ip da minha máquina.
<img width="966" height="462" alt="image" src="https://github.com/user-attachments/assets/937e2761-359d-4153-807d-dcbc35a015e1" />
<img width="943" height="467" alt="image" src="https://github.com/user-attachments/assets/5fc6a48c-1465-4d07-b611-a17c2dbc1d53" />

Essas evidências mostram a interface do MQTT Explorer, sendo acessado pelo ip 172.16.39.72 da minha máquina atual, mantendo a conexão por meio de um broker MQTT.

## 5.0. Envio de mensagens para o MQTT Explorer.
<img width="641" height="544" alt="image" src="https://github.com/user-attachments/assets/f0a2e919-fce9-46ed-94f9-927966b5bbbf" />
<img width="406" height="328" alt="image" src="https://github.com/user-attachments/assets/f04ad076-960c-44c5-ad18-1bc252cf4ba8" />

As evidências mostram o comando docker compose logs -f mqtt-subscriber exibindo os logs do contêiner mqtt-subscriber, fazendo com que os logs sejam exibidos em tempo real, atualizando automaticamente conforme novas mensagens são geradas pelo contêiner e as enviando para MQTT Explorer.

## 6.0. Testando o Servidor, com o envio de mensagens “tests” para o MQTT.
<img width="916" height="192" alt="image" src="https://github.com/user-attachments/assets/88cdd68e-41c8-4ab1-946c-593c86935069" />

Essa evidência mostra a tela de terminal com um comando MQTT usando o Mosquitto (um broker). O comando mosquitto_pub publica uma mensagem de teste ("test bruno test bruno test bruno") no tópico "sensor/pentest" em um servidor local na porta 1883. Após isso aparecem mensagens recebidas relacionadas com o "sensor/temperature", indicando o uso do comando subscriber para exibir dados de sensores ou testes, com a comunicação com o MQTT.

## 7.0. Enviando Mensagens e Atualizações para a Porta 1883.
<img width="922" height="124" alt="image" src="https://github.com/user-attachments/assets/f95fdbe6-7495-414a-a772-2c6bad6b2faf" />

Essa evidência mostra o uso de um comando MQTT com o “Mosquitto”: mosquitto_pub publicando uma mensagem numérica longa "2497249723472372309472347" no tópico "sensor/test" em um broker local na porta 1883. Em seguida, aparecem mensagens recebidas de um subscriber, incluindo atualizações de temperatura e repetições do número publicado.

## 8.0. Recebendo mensagens e atualizações dentro do meu diretório.
<img width="900" height="93" alt="image" src="https://github.com/user-attachments/assets/66fe919d-6dc3-490f-beed-75ffd0cc14bb" />

Essa evidência mostra o terminal usando o comando subscriber MQTT, que recebe duas mensagens no tópico "sensor/test" com uma sequência numérica longa "24972497234772309472347" e uma mensagem no "sensor/temperature" com o valor 28.35, atualizando a temperatura, retornando os comando no diretório ~/bruno martilliano/mqtt-project.

## 9.0. Fazendo leituras de atualizações da temperatura e dados em tempo real.
<img width="440" height="283" alt="image" src="https://github.com/user-attachments/assets/5896d25c-58ad-41b4-9690-b8989c8c547c" />

Essa evidência mostra o terminal executando o comando mosquitto_sub para subscrever o tópico "sensor/#" em um broker MQTT local na porta 1883. Exibindo uma sequência de valores numéricos como 22.02, 22.1, 22.17, que são atualizações contínuas de leituras, de temperatura ou de dados de sensor recebidos em tempo real.

# 10.0. Tentativa de acesso, para evidenciar a vulnerabilidade.

## 10.1. Comando de teste, para tentar acesso não autorizado.
.\mosquitto_pub.exe -h localhost -t sensor/hacked -m "malware"

## 10.2. Resultado ou Retorno do comando, antes da edição do ACL.
mqtt-subscriber | sensor/hacked malware 

### Evidenciando vulnerabilidade:
<img width="625" height="196" alt="image" src="https://github.com/user-attachments/assets/1eb4a193-59e9-4ad6-99df-4b29cafa1be0" />

## 11.0. Edição do Arquivo de Configuração ACL para a Identificação e Correção de Vulnerabilidades.
<img width="515" height="303" alt="image" src="https://github.com/user-attachments/assets/40b5c4d1-d2cf-4666-b28a-6ae8a42c716e" />

A evidência mostra o editor de texto GNU nano 8.4 editando o arquivo de configuração ACL para o broker MQTT Mosquitto, definindo permissões de acesso ao usuário "tudo", que pode ler e escrever no "sensor/temperature", acesso ao usuário "leitor" que pode apenas ler no "sensor/temperature", pôr fim o acesso ao usuário "escritor" que pode apenas escrever no "sensor/temperature". Com isso configurando o controle de acesso para a correção das vulnerabilidades existentes nas permissões dos usuários.

# 12.0. Vulnerabilidades Encontradas

## 12.1. Comunicação com a porta 1883, não é criptografada.
Disponibilizando o acesso e a interceptação dos dados para qualquer atacante.

## 12.1. Tem fraca autenticação, ao mesmo tempo sendo anônima (allow_anonymous true).
Tendo o risco de qualquer usuário e cliente assinar e publicar.

## 12.3. Os tópicos são abertos e não tem ACL.
Quaisquer dos tópicos não tem restrições, sendo disponível a publicação e assinatura.

# 13.0. Fazendo Correções necessárias 

## 13.1. Atualizando o Mosquitto
<img width="781" height="140" alt="image" src="https://github.com/user-attachments/assets/ecfb0277-7c7d-47ad-8b74-5fd2d2ac8d35" />
Essa evidência mostra a necessária alteração da versão do mosquitto para a 2.0.22, que garante mais segurança, com os últimos patches de segurança.

## 13.2. Habilitando o TLS na Configuração (mosquitto.conf).

### listener 8883
### protocol mqtt
### cafile /mosquitto/certs/ca.crt
### certfile /mosquitto/certs/server.crt
### keyfile /mosquitto/certs/server.key
### require_certificate false
### tls_version tlsv1.2
<img width="438" height="276" alt="image" src="https://github.com/user-attachments/assets/a7263576-0b4b-4ff8-9227-7389fb5bef75" />

A evidência mostra que a comunicação passou a ser realizada de forma criptografada pela porta 8883. O cliente ou usuário pode se conectar utilizando o certificado da CA (ca.crt), automaticamente validando, garantindo confidencialidade e integridade no manuseio dos dados.

# 14.0. Senhas e Usuários.

## 14.1. Com a configuração feita no broker:

### allow_anonymous false
### password_file /mosquitto/passwd
Apenas os usuários conseguem acessar no broker. As conexões anônimas ou com senhas inválidas são recusadas.
<img width="425" height="41" alt="image" src="https://github.com/user-attachments/assets/c0ae8f3f-788f-4608-97d7-4a306666c474" />

# 15.0. Controle do (ACL)

## 15.1. Arquivo ACL 
<img width="515" height="149" alt="image" src="https://github.com/user-attachments/assets/194d604c-fc6c-49ba-a5a5-64052891743d" />

Com essa configuração do ACL apenas o usuário sensor1 pode publicar no tópico sensors/temperature. Além também de o usuário viewer poder apenas assinar os tópicos em "sensors/#".

# Conclusão
<img width="533" height="68" alt="Captura de tela 2025-09-05 202118" src="https://github.com/user-attachments/assets/180e3ac5-b867-43eb-92b9-3c865858a40f" />
<img width="622" height="167" alt="Captura de tela 2025-09-05 201856" src="https://github.com/user-attachments/assets/8d0f910d-2ca5-458f-a58e-fc60d337ce69" />
<img width="586" height="290" alt="Captura de tela 2025-09-05 201750" src="https://github.com/user-attachments/assets/6482d688-3eec-4a2f-9bf5-e4d1e70ec05f" />


### Ao fim desse relatório concluo que consegui realizar as atividades básicas que foram encarregadas a mim, alguns dos objetivos a serem cumpridos com esse relatório, não consegui atingi-los, acredito que tive um desempenho rasuável, não realizei uma atividade que atingiu todas as tarefas propostas, mas pelo menos dei o meu máximo, até o limite de meus conhecimentos na área. Com isso acredito que no fim esse relatório identifica algumas vulnerabilidades específicas e corrige falhas como a configuração do TLS, para tentar garantir confidencialidade e integridade contra ataques aos dados, também a correção na cofiguração do ACL para tentar assegurar que os clientes e usuários só possam publicar ou assinar quando forem autorizados e por fim  atualizar ou alterar configurações como a do Mosquitto.conf para garantir um pouco mais segurança, com patches de segurança.




































