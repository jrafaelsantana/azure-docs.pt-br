---
title: Usar Apache Hive com PowerShell no HDInsight - Azure
description: Usar o PowerShell para executar consultas de Apache Hive no Apache Hadoop no Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 89fa7976b922ba0e40e97b72de5d4eb9a02f0dfd
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736076"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Executar consultas do Apache Hive usando o PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo do uso do Azure PowerShell no modo Grupo de Recursos do Azure para executar consultas do Hive em um Apache Hadoop no cluster do HDInsight.

> [!NOTE]  
> Esse documento não fornece uma descrição detalhada do que as instruções HiveQL que usadas nos exemplos fazem. Para obter informações sobre o HiveQL usado neste exemplo, veja [Usar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Um Apache Hadoop baseado em Linux no cluster HDInsight versão 3.4 ou superior.

* Um cliente com o PowerShell do Azure.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

O PowerShell do Azure fornece *cmdlets* que lhe permitem executar remotamente as consultas Hive no HDInsight. Internamente, os cmdlets fazem chamadas REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster HDInsight.

Os cmdlets a seguir são usados ao executar consultas do Hive em um cluster HDInsight remoto:

* `Connect-AzAccount`: Autentica o Azure PowerShell para a sua assinatura do Azure.
* `New-AzHDInsightHiveJobDefinition`: Cria uma *definição de trabalho* usando as instruções HiveQL especificadas.
* `Start-AzHDInsightJob`: Envia a definição do trabalho para HDInsight e inicia o trabalho. Um objeto *job* é retornado.
* `Wait-AzHDInsightJob`: Usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.
* `Get-AzHDInsightJobOutput`: Usado para recuperar a saída do trabalho.
* `Invoke-AzHDInsightHiveJob`: Usado para executar instruções de HiveQL. Esse cmdlet bloqueia a consulta até que ela seja concluída e, em seguida, retorna os resultados.
* `Use-AzHDInsightCluster`: Define o cluster atual para usar o comando `Invoke-AzHDInsightHiveJob`.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight:

1. Usando um editor, salve o código a seguir como `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra um novo prompt de comando do **PowerShell do Azure** . Altere os diretórios para o local do arquivo `hivejob.ps1` e use o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando o script é executado, você é solicitado a inserir o nome do cluster e as credenciais da conta de administrador de HTTPS/Cluster. Você também pode ser solicitado a entrar em sua assinatura do Azure.

3. Quando o trabalho for concluído, ele deverá retornar informações semelhantes ao seguinte texto:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como mencionado anteriormente, `Invoke-Hive` pode ser usado para executar uma consulta e aguardar a resposta. Use o script a seguir para ver como funciona o Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A saída se parece com o seguinte texto:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Para consultas HiveQL mais longas, é possível usar o cmdlet **Here-Strings** do PowerShell do Azure ou arquivos de script HiveQL. O snippet a seguir mostram como usar o cmdlet `Invoke-Hive` para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado em wasb://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre **Here-Strings**, consulte <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Como usar Here-Strings do Windows PowerShell</a>.

## <a name="troubleshooting"></a>Solução de problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os logs de erro. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo `hivejob.ps1`, salve-o e execute-o novamente.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Esse cmdlet retorna as informações gravadas em STDERR durante o processamento do trabalho.

## <a name="summary"></a>Resumo

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar consultas do Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

## <a name="next-steps"></a>Próximas etapas

Para obter informações gerais sobre o Hive no HDInsight:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
