# 1- Introdução
A empresa Brasil317 é uma empresa de licitações com sistema ainda em construção. Sendo assim ela ainda não possui um sistema de suportes, tendo como consequência que todas as alterações dos pregões precisam ser feitas diretamente no banco de dados, aumentando a possibilidade de erros e um enorme prejuízo financeiro a empresa.
## 1.1- Definição do problema
Conforme o problema citado na introdução, foi proposto o desafio de criação de um sistema de suporte para diminuir as alterações feitas diretamente no Banco de Dados. Dessa forma, somente o líder do setor de suportes terá a permissão de fazer alterações diretamente no Banco de Dados. Para os demais integrantes da empresa, como operações e estagiários, somente será permitida a alteração através do sistema.
## 1.2- Objetivo
O objetivo deste trabalho consiste em construir uma API REST e posteriormente um Fron end para executar suportes diminuindo assim a necessidade de alterações diretamente no Banco de Dados.
# 2- Desenvolvimento
Para o desenvolvimento desse trabalho está sendo utilizado ASP NET CORE 3.1, API REST e programação Front end por VUE. O banco de dados é Postgresql.
## 2.1- Arquitetura
A arquitetura REST possui uma série de regras e princípios que devem ser seguidas:
* **Cliente-Servidor:** Trata de separar as preocupações de interface do usuário (User Interface) do banco de dados, abstraindo a dependência entre os lados clientes/servidor e permitindo a evolução desses componentes sem impacto e quebra de contrato.
* **Interface Uniforme:** É a interoperabilidade entre os componentes cliente e servidor. Como o cliente e servidor compartilham da mesma interface, é necessário estabelecer um contrato para a comunicação entre essas partes.
* **Stateless:** Cada requisição acionada entre a comunicação cliente-servidor deve possuir toda a informação necessária e compreensível para realizar a origem da requisição, não sendo de responsabilidade do servidor armazenar qualquer tipo de contexto. Isso pode gerar alto tráfego de dados e impacto na performance da aplicação, porém pode-se utilizar recursos de cache nesses casos.
* **Cache:** É utilizado para melhorar a performance de comunicação entre aplicações, otimizando o tempo de resposta na comunicação entre cliente-servidor.
* **Camadas:** Nesse modelo, o cliente não deve conectar-se diretamente ao servidor da aplicação, porém uma camada de balanceamento de carga deverá ser acionada para essa responsabilidade.
![image](https://user-images.githubusercontent.com/51200164/171039474-1a83ebe1-d8df-4288-b37c-f886d648c597.png)
