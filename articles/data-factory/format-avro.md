---
title: Formato Avro no Azure Data Factory | Microsoft Docs
description: Este tópico descreve como lidar com o formato Avro no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: c1f55da2f1aada8c7e4a0d979b2e38e6782e564c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294224"
---
# <a name="avro-format-in-azure-data-factory"></a>Formato Avro no Azure Data Factory

Siga este artigo quando desejar **analisar os arquivos Avro ou gravar os dados no formato Avro**. 

O formato Avro tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de conjuntos Avro.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **Avro**. | Sim      |
| localização         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com `location`suporte em. **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |
| avroCompressionCodec | O codec de compactação a ser usado ao gravar em arquivos Avro. Ao ler de arquivos Avro, Data Factory determinará automaticamente o codec de compactação com base nos metadados do arquivo.<br>Os tipos com suporte são "**None**" (padrão), "deflate", "encaixar". | Não       |

> [!NOTE]
> Não há suporte para o espaço em branco no nome da coluna para arquivos Avro.

A seguir está um exemplo de conjunto de exemplos de Avro no armazenamento de BLOBs do Azure:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor do Avro.

### <a name="avro-as-source"></a>Avro como fonte

As propriedades a seguir têm suporte na seção ***\*origem\**** da atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **AvroSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com `storeSettings`suporte em. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="avro-as-sink"></a>Avro como coletor

As propriedades a seguir têm suporte na seção ***\*coletor\**** de atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **AvroSink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com `storeSettings`suporte em. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

## <a name="data-type-support"></a>Suporte ao tipo de dados

Não há suporte para [tipos de dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) do Avro (registros, enumerações, matrizes, mapas, uniões e fixos).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
