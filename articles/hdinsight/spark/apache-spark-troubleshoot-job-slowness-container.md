---
title: Apache Spark lento quando o contêiner de armazenamento do Azure contém muitos arquivos – HDInsight
description: Apache Spark trabalho é executado lentamente quando o contêiner de armazenamento do Azure contém muitos arquivos no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: 40c5d023647d3592e44588fbc24bf2743da34373
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088608"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark execução de trabalho lentamente quando o contêiner de armazenamento do Azure contém muitos arquivos no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar um cluster HDInsight, o trabalho de Apache Spark que grava no contêiner de armazenamento do Azure torna-se lento quando há muitos arquivos/subpastas. Por exemplo, ele leva 20 segundos ao gravar em um novo contêiner, mas cerca de 2 minutos ao gravar em um contêiner que tem 200 arquivos.

## <a name="cause"></a>Causa

Esse é um problema conhecido do Spark. O lentidão vem das operações `ListBlob` e `GetBlobProperties` durante a execução do trabalho do Spark.

Para rastrear partições, o Spark precisa manter um `FileStatusCache` que contém informações sobre a estrutura do diretório. Usando esse cache, o Spark pode analisar os caminhos e estar atento às partições disponíveis. O benefício do acompanhamento de partições é que o Spark só toca os arquivos necessários quando você lê os dados. Para manter essas informações atualizadas, quando você grava novos dados, o Spark precisa listar todos os arquivos no diretório e atualizar esse cache.

No Spark 2,1, embora não seja necessário atualizar o cache após cada gravação, o Spark verificará se uma coluna de partição existente corresponde ao proposto na solicitação de gravação atual, de modo que também levará à lista de operações no início de cada gravação.

No Spark 2,2, ao gravar dados com o modo de acréscimo, esse problema de desempenho deve ser corrigido.

## <a name="resolution"></a>Resolução

Quando você cria um conjunto de dados particionado, é importante usar um esquema de particionamento que limitará o número de arquivos que o Spark tem a listar para atualizar `FileStatusCache`o.

Para cada enésimo micro batch em que N% 100 = = 0 (100 é apenas um exemplo), mova os dados existentes para outro diretório, que pode ser carregado pelo Spark.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
