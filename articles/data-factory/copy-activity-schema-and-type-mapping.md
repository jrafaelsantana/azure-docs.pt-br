---
title: Mapeamento de esquema na atividade de cópia | Microsoft Docs
description: Saiba mais sobre como a atividade de cópia no Azure Data Factory mapeia esquemas e tipos de dados dos dados de origem para os dados do coletor ao copiar dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: b705123dc6492466c30b3c1ddaf4b330b0d684a1
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272271"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia

Este artigo descreve como a Azure Data Factory atividade de cópia faz mapeamento de esquema e mapeamento de tipo de dados dos dados de origem para coletar dados ao executar a cópia de dados.

## <a name="schema-mapping"></a>Mapeamento de esquema

O mapeamento de coluna se aplica ao copiar dados da origem para o coletor. Por padrão, a atividade copiar **dados de origem do mapa para o coletor por nomes de coluna**. Você pode especificar o [mapeamento explícito](#explicit-mapping) para personalizar o mapeamento de coluna com base em sua necessidade. Mais especificamente, a atividade de cópia:

1. Lê os dados da origem e determina o esquema de origem
2. Use o mapeamento de coluna padrão para mapear colunas por nome ou aplicar mapeamento de coluna explícito, se especificado.
3. Grava os dados no coletor

### <a name="explicit-mapping"></a>Mapeamento explícito

Você pode especificar as colunas a serem mapeadas na Propriedade Copy `translator` ->.  ->  `mappings` O exemplo a seguir define uma atividade de cópia em um pipeline para copiar dados de um texto delimitado para o Azure SQL Database.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

As propriedades a seguir têm suporte `translator` no  ->  `mappings` objeto-> `source` com `sink`e:

| Propriedade | Descrição                                                  | Obrigatório |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nome da coluna de origem ou do coletor.                           | Sim      |
| ordinal  | Índice de coluna. Comece com 1. <br>Aplica-se e é necessário ao usar texto delimitado sem linha de cabeçalho. | Não       |
| path     | Expressão de caminho JSON para cada campo a ser extraído ou mapeado. Aplicar para dados hierárquicos, por exemplo, MongoDB/REST.<br>Para campos sob o objeto raiz, o caminho JSON começa com root $; para campos dentro da matriz escolhida pela `collectionReference` Propriedade, o caminho JSON é iniciado a partir do elemento da matriz. | Não       |
| type     | Data Factory tipo de dados provisório da coluna de origem ou do coletor. | Não       |
| culture  | Cultura da coluna de origem ou do coletor. <br>Aplicar quando o tipo `Datetime` for `Datetimeoffset`ou. O padrão é `en-us`. | Não       |
| format   | Cadeia de caracteres de formato a ser usada `Datetime` quando `Datetimeoffset`o tipo é ou. Consulte [Data personalizada e cadeias de caracteres de formato de hora](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar a data e hora. | Não       |

As propriedades a seguir têm suporte `translator`  ->  `mappings` em adição ao objeto com `source` e `sink`:

| Propriedade            | Descrição                                                  | Obrigatório |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Com suporte apenas quando dados hierárquicos, por exemplo, MongoDB/REST, é origem.<br>Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. | Não       |

### <a name="alternative-column-mapping"></a>Mapeamento de coluna alternativo

Você pode especificar a atividade de cópia `translator` ->  ->  `columnMappings` para mapear entre dados em formato tabular. Nesse caso, a seção "Structure" é necessária para os conjuntos de dados de entrada e saída. O mapeamento de coluna dá suporte ao **mapeamento de todas as colunas ou de um subconjunto de colunas na “structure” do conjunto de dados de origem para todas as colunas na “structure” do conjunto de dados do coletor**. Veja a seguir condições de erro que resultam em uma exceção:

* O resultado da consulta do armazenamento de dados de origem não tem um nome de coluna especificado na seção “structure” do conjunto de dados de entrada.
* O armazenamento de dados do coletor (se estiver com o esquema predefinido) não tem um nome de coluna especificado na seção “structure” do conjunto de dados de saída.
* Menos colunas ou mais colunas na seção "structure" do conjunto de dados do coletor do que o especificado no mapeamento.
* Mapeamento duplicado.

No exemplo a seguir, o conjunto de dados de entrada tem uma estrutura, e aponta para uma tabela em um Oracle Database local.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Neste exemplo, o conjunto de dados de saída tem uma estrutura e aponta para uma tabela em Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O JSON a seguir define uma atividade de cópia em um pipeline. As colunas da origem são mapeadas para as colunas no coletor usando a propriedade do **Tradutor** -> **ColumnMappings** .

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Se você está utilizando a sintaxe de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` para especificar o mapeamento de coluna, ainda há suporte no estado em que se encontra.

### <a name="alternative-schema-mapping"></a>Mapeamento de esquema alternativo

Você pode especificar a atividade de cópia `translator` ->  ->  `schemaMapping` para mapear entre dados em formato hierárquico e dados em formato de tabela, por exemplo, copiar do MongoDB/REST para o arquivo de texto e copiar do Oracle para a API do Azure Cosmos DB para MongoDB. As propriedades a seguir têm suporte na seção `translator` da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade type do tradutor da atividade de cópia deve ser definida como: **TabularTranslator** | Sim |
| schemaMapping | Uma coleção de pares chave-valor, que representa a relação de mapeamento **do lado da origem para o lado do coletor**.<br/>- **Chave:** representa a origem. Para **fonte de tabela**, especifique o nome da coluna conforme definido na estrutura do conjunto de dados; para **fonte hierárquica**, especifique a expressão de caminho JSON para cada campo a ser extraído e mapeado.<br>- **Valor:** representa o coletor. Para o **coletor de tabela**, especifique o nome da coluna conforme definido na estrutura do conjunto de dados; para **coletor hierárquico**, especifique a expressão de caminho JSON para cada campo a ser extraído e mapeado. <br>No caso de dados hierárquicos, para campos em objeto raiz, o caminho JSON começa com root $; para campos dentro da matriz escolhida pela `collectionReference` Propriedade, o caminho JSON é iniciado a partir do elemento da matriz.  | Sim |
| collectionReference | Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. Essa propriedade só terá suporte quando os dados hierárquicos forem a origem. | Não |

**Exemplo: copiar do MongoDB para a Oracle:**

Por exemplo, se você tiver o documento do MongoDB com o seguinte conteúdo:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

e você deseja copiá-lo para uma tabela do Azure SQL no formato a seguir, ao nivelar os dados de dentro da matriz *(order_pd e order_price)* e fazer uma união cruzada com as informações de raiz comuns *(número, data e cidade)* :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configure a regra de mapeamento de esquema como o seguinte exemplo JSON de atividade de cópia:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

A atividade de cópia executa o mapeamento dos tipos de origem para os tipos de coletor com a abordagem de duas etapas a seguir:

1. Converter de tipos de origem nativos para tipos de dados provisórios do Azure Data Factory
2. Converter de tipos de dados provisórios do Azure Data Factory para o tipo de coletor nativo

Você pode encontrar o mapeamento entre o tipo nativo para o tipo provisório na seção "Mapeamento de tipo de dados" em cada tópico de conector.

### <a name="supported-data-types"></a>Tipos de dados com suporte

O Data Factory dá suporte aos seguintes tipos de dados provisórios: você pode especificar os valores a seguir ao configurar informações de tipo na configuração da [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure-or-schema):

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Duplo
* Guid
* Int16
* Int32
* Int64
* Simples
* String
* Timespan

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
