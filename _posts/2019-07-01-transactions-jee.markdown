---
layout: post
title:  "Transações JEE"
date:   2019-10-14 22:30:00 -0300
categories: [programming]
tags: [code, java, jee]
---

Nese artigo vamos descrever um pouco sobre transações em uma aplicação Java EE.


# Definição

Transações são operações que permitem entre outras coisas, executar um processamento em lote. Segue o princípio de sigla ACID: Atomicy, Consistency, Isolation, Durability.

Maiores detalhes sobre transações em aplicações Java EE podem ser encontrados na especificação JSR 907. Antes de divulgar este artigo, pretendo escrever uma análise sobre os conceitos apresentados nesta JSR.

# Contexto de uso

Se durante o desenvolvimento de uma aplicação Java EE nenhum cenário de concorrência teve que ser implementado, então provavelmente você não precisou declarar um bloco de execução transacionado. Exemplos de cenários desse tipo são: 
- operações com custo relevante em termos de tempo que podem ser executados simultaneamente
- processamento em lote/massa de dados
- garantir qual operação deve ser persistida independente de operações futuras/anteriores.

Em uma aplicação Java EE, transações são implicitamente gerenciadas pelo servidor(wildfly por exemplo), ou seja, dada uma requisição a um serviço REST que retorna uma lista de itens qualquer, para acessar o banco de dados, uma transação será criada e gerida pelo servidor. Antes de realizar o acesso a um banco de dados, uma operação de 'begin' será iniciada. Então, todas as operações de banco de dados podem ser efetuadas(select's, insert's, etc). Antes de retornar os dados para o serializer da camada REST, um comando de 'commit' é invocado, a fim de que as operação realizadas sejam persistidas no banco de dados. Basicamente, este é um ciclo de vida de uma transação gerenciada pelo servidor para atender um requisição REST.

# Programação em contexto síncrono/assíncrono

O cenário acima descreve um contexto básico de execução síncrona. Um cenário de execução assíncrona pode ser o exemplo de gerenciador de importação de dados via arquivos csv. Cada linha do arquivo importado deve ser entendido como uma transação, pois não deve haver relação entre o início, processamento e resultado para cada linha do arquivo. Para clarificar um pouco, em outras palavras, se um arquivo com 10 linhas for importado, e apenas 2 linhas forem consideradas inválidas, então as 8 restantes devem ser persitidas normalmente.

Ainda no mesmo cenário, as transações podem ser usadas para se obter performance na execução do processamento do arquivo. Ao envés de uma transação validar e persitir cada linha do arquivo, pode-se determinar um fluxo em que várias transações simultaneamente realizem a validação das linhas. O resultado validado pode ser coletado em pool de dados que a ser persistido em lote ou ao término da validação. 

# Conclusão

O gerenciamento explícito de transações só vale a pena em contextos de execução assíncrono. 

Noutro artigo, veremos como uma aplicação Java EE pode aplicar o controle de transações na execução dos serviços.

