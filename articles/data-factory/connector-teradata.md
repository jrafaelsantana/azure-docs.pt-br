---
title: Copiar dados do Teradata privilegiando usando o Azure Data Factory | Microsoft Docs
description: O conector Teradata do serviço de Data Factory permite copiar dados de um privilegiando Teradata para armazenamentos de dados com suporte do Data Factory como coletores.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: e538c8b00bddc8a2fa35b158c1e76f9033b73a56
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089179"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Copiar dados do Teradata privilegiando usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versão atual](connector-teradata.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do Teradata privilegiando. Ele se baseia na [visão geral da atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Teradata tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Teradata privilegiando para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Teradata dá suporte a:

- Teradata **versão 14,10, 15,0, 15,10, 16,0, 16,10 e 16,20**.
- Copiar dados usando a autenticação **básica** ou do **Windows** .
- Cópia paralela de uma fonte Teradata. Consulte a seção [cópia paralela da Teradata](#parallel-copy-from-teradata) para obter detalhes.

> [!NOTE]
>
> Após o lançamento do tempo de execução de integração auto-hospedado v 3.18, Azure Data Factory atualizado o conector do Teradata. Qualquer carga de trabalho existente que usa o conector do Teradata anterior ainda tem suporte. No entanto, para novas cargas de trabalho, é uma boa ideia usar a nova. Observe que o novo caminho requer um conjunto diferente de serviço vinculado, conjunto de código e fonte de cópia. Para obter detalhes de configuração, consulte as respectivas seções a seguir.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Integration Runtime fornece um driver Teradata interno, começando da versão 3,18. Você não precisa instalar nenhum driver manualmente. O driver requer "Visual C++ redistribuível 2012 atualização 4" no computador do Integration Runtime de hospedagem interna. Se você ainda não o tiver instalado, baixe-o [aqui](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

Para qualquer versão de tempo de execução de integração autohospedada anterior a 3,18, instale o [.net provedor de dados para Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), versão 14 ou posterior, no computador do Integration Runtime. 

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector Teradata.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O serviço vinculado do Teradata dá suporte às seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como **Teradata**. | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância Teradata. Consulte os exemplos a seguir.<br/>Você também pode colocar uma senha em Azure Key Vault e extrair a `password` configuração da cadeia de conexão. Consulte [armazenar credenciais em Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| username | Especifique um nome de usuário para se conectar ao Teradata. Aplica-se quando você está usando a autenticação do Windows. | Não |
| password | Especifique uma senha para a conta de usuário que você especificou para o nome de usuário. Você também pode optar por [fazer referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). <br>Aplica-se quando você estiver usando a autenticação do Windows ou fazendo referência a uma senha em Key Vault para autenticação básica. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Sim |

**Exemplo usando a autenticação básica**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo usando a autenticação do Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> A carga a seguir ainda tem suporte. No entanto, no futuro, você deve usar o novo.

**Carga anterior:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista das propriedades com suporte pelo conjunto de e do Teradata. Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte os [DataSets](concepts-datasets-linked-services.md).

Para copiar dados do Teradata, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida `TeradataTable`como. | Sim |
| database | O nome da instância Teradata. | Não (se "query" na fonte da atividade for especificada) |
| table | O nome da tabela na instância Teradata. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`o tipo de conjunto de texto ainda tem suporte. No entanto, recomendamos que você use o novo conjunto de um.

**Carga anterior:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Esta seção fornece uma lista das propriedades com suporte pela origem do Teradata. Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata como origem

>[!TIP]
>Para carregar dados do Teradata com eficiência usando o particionamento de dados, saiba mais na seção [cópia paralela da Teradata](#parallel-copy-from-teradata) .

Para copiar dados do Teradata, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida `TeradataSource`como. | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Ao habilitar a carga particionada, você precisa vincular quaisquer parâmetros de partição internos correspondentes em sua consulta. Para obter exemplos, consulte a seção [cópia paralela da Teradata](#parallel-copy-from-teradata) . | Não (se a tabela no DataSet for especificada) |
| partitionOptions | Especifica as opções de particionamento de dados usadas para carregar dados do Teradata. <br>Os valores permitidos são: **Nenhum** (padrão), **hash** e **DynamicRange**.<br>Quando uma opção de partição é habilitada (ou seja `None`, não), o grau de paralelismo para carregar dados simultaneamente do Teradata é controlado [`parallelCopies`](copy-activity-performance.md#parallel-copy) pela configuração na atividade de cópia. | Não |
| partitionSettings | Especifique o grupo de configurações para o particionamento de dados. <br>Aplicar quando a opção de `None`partição não for. | Não |
| partitionColumnName | Especifique o nome da coluna de origem que será usada pela partição de intervalo ou pela partição de hash para cópia paralela. Se não for especificado, o índice principal da tabela será detectado automaticamente e usado como a coluna de partição. <br>Aplicar quando a opção de partição `Hash` for `DynamicRange`ou. Se você usar uma consulta para recuperar os dados de origem, `?AdfHashPartitionCondition` o `?AdfRangePartitionColumnName` gancho ou a cláusula WHERE. Consulte o exemplo em [cópia paralela da seção Teradata](#parallel-copy-from-teradata) . | Não |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de `DynamicRange`partição for. Se você usar a consulta para recuperar dados de origem `?AdfRangePartitionUpbound` , conecte a cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela da Teradata](#parallel-copy-from-teradata) . | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição `DynamicRange`for. Se você usar uma consulta para recuperar os dados de origem, `?AdfRangePartitionLowbound` Conecte-se à cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela da Teradata](#parallel-copy-from-teradata) . | Não |

> [!NOTE]
>
> `RelationalSource`a fonte de cópia de tipo ainda tem suporte, mas não dá suporte à nova carga paralela interna do Teradata (opções de partição). No entanto, recomendamos que você use o novo conjunto de um.

**Exemplo: copiar dados usando uma consulta básica sem partição**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Cópia paralela do Teradata

O conector do Data Factory Teradata fornece particionamento de dados interno para copiar dados do Teradata em paralelo. Você pode encontrar opções de particionamento de dados na tabela de **origem** da atividade de cópia.

![Captura de tela das opções de partição](./media/connector-teradata/connector-teradata-partition-options.png)

Quando você habilita a cópia particionada, o Data Factory executa consultas paralelas em sua fonte Teradata para carregar dados por partições. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance.md#parallel-copy) configuração na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, data Factory gera e executa quatro consultas de maneira simultânea com base na opção de partição e nas configurações especificadas, e cada consulta recupera uma parte dos dados do Teradata.

É recomendável habilitar a cópia paralela com o particionamento de dados, especialmente quando você carrega grandes quantidades de dados de seu Teradata. Veja a seguir as configurações sugeridas para cenários diferentes. Ao copiar dados para o armazenamento de dados baseado em arquivo, ele é redirecionado para gravar em uma pasta como vários arquivos (apenas especifique o nome da pasta). nesse caso, o desempenho é melhor do que gravar em um único arquivo.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de uma tabela grande.                                   | **Opção de partição**: Tralha. <br><br/>Durante a execução, Data Factory detecta automaticamente a coluna CP, aplica um hash a ela e copia os dados por partições. |
| Carregar uma grande quantidade de dados usando uma consulta personalizada.                 | **Opção de partição**: Tralha.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para aplicar a partição hash. Se não for especificado, Data Factory detectará automaticamente a coluna CP da tabela que você especificou no conjunto de tabelas Teradata.<br><br>Durante a execução, data Factory `?AdfHashPartitionCondition` substitui pela lógica de partição de hash e envia para Teradata. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, tendo uma coluna de inteiros com valor distribuído uniformemente para o particionamento de intervalo. | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Você pode particionar em relação à coluna com tipo de dados Integer.<br>Limite **superior** da partição e limite **inferior**da partição: Especifique se deseja filtrar a coluna de partição para recuperar dados somente entre o intervalo inferior e superior.<br><br>Durante a execução, data Factory `?AdfRangePartitionColumnName`substitui `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` pelo nome real da coluna e os intervalos de valores para cada partição e envia para o Teradata. <br>Por exemplo, se a sua coluna de partição "ID" estiver definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia paralela como 4, Data Factory recuperará dados por 4 partições. Suas IDs estão entre [1, 20], [21, 40], [41, 60] e [61, 80], respectivamente. |

**Exemplo: consulta com partição de hash**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exemplo: consulta com partição de intervalo dinâmico**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mapeamento de tipo de dados para Teradata

Quando você copia dados do Teradata, os mapeamentos a seguir se aplicam. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Teradata | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Duplo |Duplo |
| Graphic |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Inteiro |Int32 |
| Interval Day |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Day To Hour |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Day To Minute |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Day To Second |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Hour |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Hour To Minute |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Hour To Second |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Minute |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Minute To Second |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Month |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Second |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Year |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Interval Year To Month |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Number |Duplo |
| Período (Data) |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Período (hora) |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Período (hora com fuso horário) |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Período (carimbo de data/hora) |Não compatível. Aplicar conversão explícita na consulta de origem. |
| Período (carimbo de data/hora com fuso horário) |Não compatível. Aplicar conversão explícita na consulta de origem. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Hora com fuso horário |TimeSpan |
| Carimbo de data/hora |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Não compatível. Aplicar conversão explícita na consulta de origem. |
| xml |Não compatível. Aplicar conversão explícita na consulta de origem. |


## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
