---
layout: post
title:  "Transações JEE"
date:   2019-07-01 22:43:00 -0300
categories: [programming]
tags: [code, java, jee]
---

Nese artigo vamos descrever um pouco sobre transações em uma aplicação Java EE.


# Definição

Transações são operações que permitem entre outras coisas, executar um processamento em lote. Segue o princípio de sigla ACID: Atomicy, Consistency, Isolation, Durability.

# Contexto de uso

Se durante o desenvolvimento de uma aplicação Java EE nenhum cenário de concorrência teve que ser implementado, então provavelmente você não precisou declarar um bloco de execução transacionado. Exemplos de cenários desse tipo são: 
- operações com custo relevante em termos de tempo que podem ser executados simultaneamente
- processamento em lote/massa de dados
- garantir qual operação deve ser persistida independente de operações futuras/anteriores.

Em uma aplicação Java EE, transações são implicitamente gerenciadas pelo servidor(wildfly por exemplo), ou seja, dada uma requisição a um serviço REST que retorna uma lista de itens qualquer, para acessar o banco de dados, uma transação será criada e gerida pelo servidor. Antes de realizar o acesso a um banco de dados, uma operação de 'begin' será iniciada. Então, todas as operações de banco de dados podem ser efetuadas(select's, insert's, etc). Antes de retornar os dados para o serializer da camada REST, um comando de 'commit' é invocado, a fim de que as operação realizadas sejam persistidas no banco de dados. Basicamente, este é um ciclo de vida de uma transação gerenciada pelo servidor para atender um requisição REST.

# Progação em contexto síncrono/assíncrono

O cenário acima descreve um contexto básico de execução síncrona. Um cenário de execução assíncrona pode ser o exemplo de gerenciador de importação de dados via arquivos csv. Cada linha do arquivo importado deve ser entendido como uma transação, pois não deve haver relação entre o início, processamento e resultado para cada linha do arquivo. Para clarificar um pouco, em outras palavras, se um arquivo com 10 linhas for importado, e apenas 2 linhas forem consideradas inválidas, então as 8 restantes devem ser persitidas normalmente.

Ainda no mesmo cenário da importador, as transações podem ser usadas para se obter performance na execução do processamento do arquivo. Ao envés de uma transação validar e persitir cada linha do arquivo, pode-se determinar um fluxo em que determinadas transações simultaneamente realizam a verificação de validade das linhas de forma a constituir um pool de dados que deve ser persistido em lote. Com isso, o tempo para persitir os dados é resumido em uma operação de persistencia. Mas essa estratégia deve ser empregada com cuidado, pois se na faze de persitência ocorre um erro, todos os dados devem sofre rollback para garantir a consistência da informação.

# Conclusão

Conforme descrito ao longo desse artigo, deve-se analisar se a complexidade de se gerir transações vale apena dado o cenário de de implementação em análise. Noutro artigo, veremos como uma aplicação Java EE pode aplicar o controle de transações na execução dos serviços.

