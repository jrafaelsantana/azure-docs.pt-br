---
title: Executar serviço kubernetes do Azure-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Implante a imagem de contêiner Análise de Texto no serviço kubernetes do Azure e teste-a em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: db1c26805f76b0ca8991a562531b4476b6899d86
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130090"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Implantar um contêiner de Análise de Texto no serviço kubernetes do Azure

Saiba como implantar os serviços cognitivas do Azure [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) a imagem de contêiner no AKs (serviço kubernetes do Azure). Este procedimento mostra como criar um recurso de Análise de Texto, como criar uma imagem de análise de opiniões associada e como exercitar essa orquestração dos dois em um navegador. Usar contêineres pode mudar sua atenção para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não use Azure Cloud Shell. Você precisará do seguinte:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um editor de texto, por exemplo, [Visual Studio Code](https://code.visualstudio.com/download).
* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) instalado.
* A [CLI do kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) instalada.
* Um recurso do Azure com o tipo de preço correto. Nem todos os tipos de preços funcionam com esse contêiner:
    * O recurso **análise de texto do Azure** somente com tipos de preço F0 ou Standard.
    * Recurso de **Serviços cognitivas do Azure** com o tipo de preço S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Próximas etapas

* Usar mais [contêineres de serviços cognitivas](../../cognitive-services-container-support.md)
* Usar o [serviço conectado do análise de texto](../vs-text-connected-service.md)
