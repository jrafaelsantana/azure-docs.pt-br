---
title: Executar tarefas do Apache Sqoop com o Azure HDInsight (Apache Hadoop)
description: Saiba como usar o Azure PowerShell em uma estação de trabalho para executar importação e exportação do Sqoop entre um cluster do Hadoop e um Banco de Dados SQL do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: f2a153b1eef974c8c73df49a6eed53ef5dbf2353
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076205"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Usar Apache Sqoop com o Hadoop no HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Apache Sqoop no HDInsight para importar e exportar dados entre um cluster HDInsight e um banco de dado SQL do Azure.

Embora Apache Hadoop seja uma opção natural para o processamento de dados não estruturados e semiestruturados, como logs e arquivos, também pode haver a necessidade de processar dados estruturados armazenados em bancos de dados relacionais.

O [Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) é uma ferramenta projetada para transferir dados entre clusters Hadoop e bancos de dados relacionais. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com o MapReduce ou o Apache Hive e, em seguida, exportar os dados de volta para um RDBMS. Neste artigo, você está usando um banco de dados SQL Server para seu banco de dados relacional.

> [!IMPORTANT]  
> Este artigo configura um ambiente de teste para executar a transferência de dados. Em seguida, escolha um método de transferência de dados para esse ambiente de um dos métodos na seção [executar trabalhos do Sqoop](#run-sqoop-jobs), mais adiante.

Para versões do Sqoop com suporte em clusters HDInsight, consulte [novidades nas versões do cluster fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Compreender o cenário

O cluster HDInsight é fornecido com alguns dados de exemplo. Você usa estas duas amostras:

* Um arquivo de log do Apache Log4J, que está `/example/data/sample.log`localizado em. Os seguintes logs são extraídos do arquivo:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Uma tabela do hive `hivesampletable`denominada, que faz referência ao arquivo `/hive/warehouse/hivesampletable`de dados localizado em. A tabela contém alguns dados de dispositivo móvel.
  
  | Campo | Tipo de dados |
  | --- | --- |
  | clientid |cadeia de caracteres |
  | querytime |cadeia de caracteres |
  | market |cadeia de caracteres |
  | deviceplatform |cadeia de caracteres |
  | devicemake |cadeia de caracteres |
  | devicemodel |cadeia de caracteres |
  | state |cadeia de caracteres |
  | country |cadeia de caracteres |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

Neste artigo, você usará esses dois conjuntos de valores para testar a importação e exportação do Sqoop.

## <a name="create-cluster-and-sql-database"></a>Configurar ambiente de teste
O cluster, o banco de dados SQL e outros objetos são criados por meio do portal do Azure usando um modelo de Azure Resource Manager. O modelo pode ser encontrado nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo do Resource Manager chama um pacote bacpac para implantar os esquemas de tabela em um banco de dados SQL.  O pacote bacpac está localizado em um contêiner de blob público, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se você quiser usar um contêiner particular para os arquivos bacpac, use os seguintes valores no modelo:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importar usando um modelo ou o Portal do Azure é compatível apenas com a importação de um arquivo BACPAC do Armazenamento de Blobs do Azure.

1. Selecione a imagem a seguir para abrir o modelo do Resource Manager na portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Insira as seguintes propriedades:

    |Campo |Valor |
    |---|---|
    |Assinatura |Selecione sua assinatura do Azure na lista suspensa.|
    |Grupo de recursos |Selecione o grupo de recursos na lista suspensa ou crie um novo|
    |Location |Selecione uma região na lista suspensa.|
    |Nome do Cluster |Insira um nome para o cluster Hadoop. Use somente letra minúscula.|
    |Nome de usuário de logon do cluster |Mantenha o valor `admin`preenchido previamente.|
    |Senha de logon do cluster |Digite uma senha.|
    |Nome de usuário SSH |Mantenha o valor `sshuser`preenchido previamente.|
    |Senha ssh |Digite uma senha.|
    |Logon de administrador do SQL |Mantenha o valor `sqluser`preenchido previamente.|
    |Senha de administrador do SQL |Digite uma senha.|
    |Local _artifacts | Use o valor padrão, a menos que você queira usar seu próprio arquivo bacpac em um local diferente.|
    |Token SAS do local _artifacts |Deixe em branco.|
    |Nome do arquivo Bacpac |Use o valor padrão, a menos que você queira usar seu próprio arquivo bacpac.|
    |Location |Use o valor padrão.|

    O nome do SQL Server do Azure `<ClusterName>dbserver`será. O nome do banco de `<ClusterName>db`dados será. O nome da conta de armazenamento padrão `e6qhezrh2pdqu`será.

3. Selecione **Concordo com os termos e as condições declarados acima**.

4. Selecione **Comprar**. Você poderá ver um novo bloco intitulado Como enviar a implantação para a implantação do modelo. É preciso sobre cerca de 20 minutos para criar o cluster e o banco de dados SQL.

## <a name="run-sqoop-jobs"></a>Executar trabalhos do Sqoop

O HDInsight pode executar trabalhos do Sqoop usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use** se quiser... | ...um shell **interativo** | ...Processamento em**lotes** | ... desse **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X ou Windows |
| [SDK .NET para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (por enquanto) |
| [PowerShell do Azure](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Limitações

* Exportação em massa — com HDInsight baseado em Linux, o conector Sqoop usado para exportar dados no Microsoft SQL Server ou no Banco de Dados SQL do Azure, atualmente, não permite inserções em massa.
* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop realizará várias inserções em vez de operações de inserção em lotes.

## <a name="next-steps"></a>Próximas etapas
Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

* [Usar o Apache Hive com o HDInsight](../hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](../hdinsight-use-pig.md)
* [Carregar dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para fazer upload de dados para HDInsight/Azure Storage Blob.
