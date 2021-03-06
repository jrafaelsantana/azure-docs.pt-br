---
title: Avaliação offline – personalizador
titleSuffix: Azure Cognitive Services
description: Saiba como analisar o loop de aprendizado com uma avaliação offline
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: a157b36ad2b4f5a7f2e50a9609d8acd621efd5a8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884446"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analise seu loop de aprendizagem com uma avaliação offline

Saiba como concluir uma avaliação offline e entender os resultados.

As avaliações offline permitem medir a eficácia do Personalizador em comparação com o comportamento padrão do aplicativo, saber quais recursos estão contribuindo mais para a personalização e descobrir novas configurações de aprendizado de máquina automaticamente.

Leia sobre [avaliações offline](concepts-offline-evaluation.md) para saber mais.


## <a name="prerequisites"></a>Pré-requisitos

* Um loop personalizado configurado
* O loop personalizador deve ter uma quantidade representativa de dados – como um aproximada, é recomendável pelo menos 50.000 eventos em seus logs para obter resultados significativos de avaliação. Opcionalmente, talvez você também tenha exportado anteriormente arquivos da _política de aprendizado_, que podem ser comparados e testados na mesma avaliação.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Etapas para iniciar uma nova avaliação offline

1. No [portal do Azure](https://azure.microsoft.com/free/), localize o recurso de personalização.
1. Na portal do Azure, vá para a seção **avaliações** e selecione **criar avaliação**.
    ![Na portal do Azure, vá para a seção * * avaliações * * e selecione * * criar avaliação * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Selecione as seguintes configurações:

    * Um nome de avaliação
    * Data de início e de término – essas são as datas no passado, que especificam o intervalo de dados a ser usado na avaliação. Esses dados precisam estar presentes nos logs, conforme especificado na configuração [Retenção de Dados](how-to-settings.md).
    * Descoberta de otimização definida como **Sim**

    ![Escolher configurações de avaliação offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Inicie a avaliação selecionando **OK**. 

## <a name="results"></a>Resultados

As avaliações podem levar muito tempo para serem executadas, dependendo da quantidade de dados a serem processados, do número de políticas de aprendizado para comparação e se uma otimização foi solicitada.

Depois de concluído, você pode selecionar a avaliação na lista de avaliações. 

As comparações das políticas de aprendizado incluem:

* **Política Online**: A Política de Aprendizado atual usada no Personalizador
* **Linha de Base**: O padrão do aplicativo (conforme determinado pela primeira ação enviada nas Chamadas de classificação).
* **Política Aleatória**: Um comportamento de Classificação imaginário que sempre retorna uma escolha aleatória de ações daquelas fornecidas.
* **Políticas Personalizadas**: Políticas de Aprendizado adicionais carregadas ao iniciar a avaliação.
* **Política Otimizada**: Se a avaliação tiver sido iniciada com a opção para descobrir uma política otimizada, ela também será comparada, e você poderá baixá-la ou torná-la a política de aprendizado online, substituindo a atual.

![Gráfico de resultados das configurações de avaliação offline](./media/offline-evaluation/evaluation-results.png)

Eficácia de [recursos](concepts-features.md) para ações e contexto.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como funcionam as avaliações offline](concepts-offline-evaluation.md).
