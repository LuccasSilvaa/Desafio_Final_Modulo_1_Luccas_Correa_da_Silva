Análise de Rede em Ambiente Simulado
Este repositório contém os resultados e a documentação de uma análise de segurança realizada num ambiente de rede corporativa simulado, como parte de um estudo em Cibersegurança.

🎯 Sobre o Projeto
O objetivo deste projeto foi assumir o papel de um analista de segurança para mapear uma rede interna, identificar os ativos, analisar a segmentação e diagnosticar vulnerabilidades. A análise foi conduzida a partir de um container Docker (analyst) com acesso a três sub-redes distintas.

🌐 Topologia da Rede Analisada
O ambiente simulado é composto por três segmentos de rede:

corp_net (10.10.10.0/24): Rede destinada às estações de trabalho.

guest_net (10.10.30.0/24): Rede destinada a visitantes.

infra_net (10.10.50.0/24): Rede destinada à infraestrutura de servidores críticos.

🛠️ Metodologia e Ferramentas Utilizadas
A análise seguiu um fluxo de trabalho metódico, dividido em quatro fases principais:

Reconhecimento Inicial: Identificação do ponto de partida e validação da conectividade.

Descoberta de Ativos: Mapeamento de todos os hosts ativos em cada sub-rede.

Scan de Portas e Serviços: Enumeração de portas abertas e identificação dos serviços e versões.

Análise Aprofundada: Investigação de cada serviço em busca de vulnerabilidades específicas.

As seguintes ferramentas foram utilizadas:

Nmap: Para descoberta de hosts e análise detalhada de serviços.

Rustscan: Para a varredura inicial e rápida de portas.

Net-Tools (ip, ping, curl): Para tarefas básicas de rede.

Utilitários de linha de comando (grep, awk, tee): Para tratamento e armazenamento dos resultados.

🚨 Principais Achados (Vulnerabilidades)
A análise revelou diversas falhas de segurança, sendo as mais importantes:

1. (CRÍTICO) Falha de Segmentação de Rede
Descrição: A descoberta mais grave foi a inversão completa das redes guest_net e infra_net. Todos os servidores críticos (LDAP, MySQL, Samba, etc.) foram encontrados na rede que deveria ser para visitantes, enquanto os dispositivos de utilizadores (laptops, MacBooks) estavam na rede que deveria ser a mais segura.

Impacto: Anula completamente o propósito da segmentação, expondo os ativos mais valiosos da empresa a ameaças diretas.

2. (CRÍTICO) Fuga de Informação no Servidor LDAP
Localização: 10.10.30.17 (na rede errada).

Descrição: O servidor LDAP permite conexões anónimas ("anonymous bind"), o que possibilitou a extração de informações sobre a estrutura do diretório (dc=example,dc=org).

Impacto: Permite que um atacante mapeie toda a estrutura de utilizadores e grupos da organização.

3. (ALTO) Servidor de Base de Dados MySQL Exposto
Localização: 10.10.30.11 (na rede errada).

Descrição: O servidor MySQL está diretamente acessível na rede e vaza informações detalhadas da sua versão (8.0.42), facilitando a busca por exploits conhecidos.

Impacto: Permite tentativas de ataques de força bruta e exploração de vulnerabilidades específicas da versão.

4. (MÉDIO) Painel Zabbix Exposto com Fuga de Informação
Localização: 10.10.30.117 (na rede errada).

Descrição: A interface web do servidor de monitorização Zabbix está exposta e revela a versão do PHP (7.3.14) em uso.

Impacto: Risco de acesso com credenciais padrão (Admin/zabbix) e exploração de vulnerabilidades do PHP.
