---
title: Visualize os trabalhos do Azure Stream Analytics no Microsoft Visual Studio
description: Este artigo descreve como exibir e gerenciar Azure Stream Analytics trabalhos no Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ae532ed19c2273e43aa739e84d5a68cadb717b86
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173021"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics

Ferramentas de Stream Analytics do Azure para o Microsoft Visual Studio tornam fácil para os desenvolvedores gerenciarem seus trabalhos do Stream Analytics diretamente do IDE. Com as ferramentas do Azure Stream Analytics é possível:
- [Criar novos trabalhos](stream-analytics-quick-create-vs.md)
- Iniciar, parar, e [monitorar trabalhos](stream-analytics-monitor-jobs-use-vs.md)
- Veriricar resultados de trabalho
- Exportar os trabalhos existentes para um projeto
- Testar conexões de entrada e saídas
- [Executar consultas localmente](stream-analytics-vs-tools-local-run.md)

Saber como [instalar as ferramentas do Azure Stream Analytics Tools para o Microsoft Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Explrar a exibição do trabalho

Você pode usar o modo de exibição de trabalho para interagir com trabalhos do Azure Stream Analytics do Microsoft Visual Studio.

### <a name="open-the-job-view"></a>Abrir a exibição do trabalho

1. No **Gerenciador de Servidores**, selecione **Trabalhos do Stream Analytics** e, em seguida, selecione **Atualizar**. Seu trabalho deve aparecer em **Trabalhos do Stream Analytics**.

    ![Lista de gerenciador de servidores do Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. Expanda o nó de trabalho e clique duas vezes no nó **Exibição de Trabalho** para abrir uma exibição do trabalho.
    
   ![Nó de trabalho expandido](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Iniciar e parar trabalhos

Os trabalhos do Stream Analytics do Azure podem ser gerenciados totalmente da exibição do trabalho no Microsoft Visual Studio. Use os controles para iniciar, parar ou excluir um trabalho.
    
   ![Controles de trabalho do Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>Veriricar resultados de trabalho

Atualmente, as ferramentas do Stream Analytics para o Visual Studio dão suporte a visualização de saída para o Azure Data Lake Storage e o armazenamento de blobs. Para exibir o resultado, simplesmente clique duas vezes no nó de saída do diagrama de trabalho na **Exibição de Trabalho** e insira as credenciais apropriadas.

   ![Saída do Trabalho do Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>Exportar trabalhos para um projeto

Há duas maneiras que você pode usar para exportar um trabalho para um projeto.

1. No **Gerenciador de Servidores**, no nó Trabalhos do Stream Analytics, clique com o botão direito do mouse no nó do trabalho. Selecione **Exportar para Novo Projeto do Stream Analytics**.
    
   ![Exportar trabalhos para o projeto](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    O projeto gerado aparece no **Gerenciador de Soluções**.
    
   ![Gerenciador de soluções](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Na exibição de trabalho, selecione **Gerar Projeto**.
    
   ![Gerar Projeto da exibição de trabalho](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Conexões de teste

Conexões de entrada e saídas podem ser testadas da **Exibição de Trabalho** selecionando uma opção da lista suspensa de **Conexão de teste**.

   ![Lista suspensa da Conexão de Teste](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Os resultados da **Conexão de Teste** são exibidos na janela de **Saída**.

   ![Testar os resultados de conexão](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar e gerenciar trabalhos do Azure Stream Analytics usando o Microsoft Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Início Rápido: Criar um trabalho de Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Tutorial: Implantar um trabalho de Azure Stream Analytics com CI/CD usando Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrar e desenvolver continuamente com ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
