---
title: Associar um Azure Cosmos DB ao seu aplicativo Azure Spring Cloud | Microsoft Docs
description: Saiba como associar Azure Cosmos DB ao seu aplicativo Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d051fd431180e9cb86f1df4642fb5e947103c007
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038724"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Tutorial: Associar um Azure Cosmos DB ao seu aplicativo Azure Spring Cloud

O Azure Spring Cloud permite associar os serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de configurar manualmente o aplicativo Spring boot. Este artigo demonstra como associar seu aplicativo a um Azure Cosmos DB.

Pré-requisitos:
* Uma instância do Azure Spring Cloud implantada.  Siga nosso [início rápido](spring-cloud-quickstart-launch-app-cli.md) para começar.
* Uma conta de Azure Cosmos DB com um nível mínimo de permissões de colaborador.

## <a name="bind-azure-cosmos-db"></a>Associar Azure Cosmos DB

Azure Cosmos DB tem cinco tipos de API diferentes que dão suporte à associação:

1. Crie um banco de dados do Azure Cosmos DB. [Consulte este artigo](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) para obter ajuda com a criação do banco de dados. Registre o nome do seu banco de dados. Nosso nome é `testdb`.

1. Adicione uma das seguintes dependências em seu aplicativo Spring Cloud `pom.xml` de acordo com seu tipo de API.
    
    #### <a name="api-type-core-sql"></a>Tipo de API: Núcleo (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>Tipo de API: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>Tipo de API: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>Tipo de API: Gremlin (grafo)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>Tipo de API: tabela do Azure

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Atualize a implantação atual usando `az spring-cloud app update` ou crie uma nova implantação para essa alteração usando `az spring-cloud app deployment create`.  Esses comandos atualizarão ou criarão o aplicativo com a nova dependência.

1. Acesse sua página de serviço de nuvem do Azure Spring no portal do Azure. Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. Localize o **painel do aplicativo** e selecione o aplicativo a ser associado ao cosmos DB. Em seguida, selecione `Service binding` e selecione o botão `Create service binding`. Preencha o formulário, selecionando o **tipo de associação** `Azure Cosmos DB`, o tipo de API, o nome do banco de dados e a conta de Azure Cosmos DB.

    > [!NOTE]
    > Use um espaço de chave para o nome do banco de dados se você estiver usando Cassandra.

1. Reinicie o aplicativo selecionando o botão **reiniciar** na página do aplicativo.

1. Para garantir que o serviço esteja associado corretamente, selecione o nome da associação e verifique seus detalhes. O campo `property` deve ser semelhante a este:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a associar seu aplicativo de nuvem Spring do Azure a um CosmosDB.  Para saber como associar seu aplicativo a um cache Redis do Azure, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Associar um aplicativo de nuvem Spring do Azure a um cache Redis do Azure](spring-cloud-tutorial-bind-redis.md).
