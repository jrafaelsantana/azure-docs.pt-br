---
title: Interface visual
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os termos, os conceitos e o fluxo de trabalho que compõem a interface visual (versão prévia) para Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: f560887a48ce4754b26a54ef0e18093c5577af34
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128815"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>O que é a interface visual para Azure Machine Learning? 

A interface visual (visualização) para Azure Machine Learning permite preparar dados, treinar, testar, implantar, gerenciar e acompanhar modelos de aprendizado de máquina sem escrever código.

Não há nenhuma programação necessária, você conecta visualmente [conjuntos](#dataset) de os e [módulos](#module) para construir seu modelo.

A interface visual usa seu [espaço de trabalho](concept-workspace.md) Azure Machine Learning para:

+ Os artefatos de gravação do [experimento](#experiment) são executados no espaço de trabalho.
+ Acessar [conjuntos](#dataset)de os.
+ Use os [recursos de computação](#compute) no espaço de trabalho para executar o experimento. 
+ Registrar [modelos](concept-azure-machine-learning-architecture.md#models).
+ [Implante](#deployment) modelos como serviços Web em recursos de computação no espaço de trabalho.

![Visão geral da interface visual](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Fluxo de Trabalho

A interface visual oferece uma tela Visual interativa para criar, testar e iterar rapidamente em um modelo. 

+ Você arrasta e solta [módulos](#module) na tela.
+ Conecte os módulos em conjunto para formar um [experimento](#experiment).
+ Execute o experimento usando o recurso de computação do espaço de trabalho do Machine Learning Service.
+ Itere em seu design de modelo editando o experimento e executando-o novamente.
+ Quando estiver pronto, converta seu **teste de treinamento** em um **experimento de previsão**.
+ [Implante](#deployment) o teste de previsão como um serviço Web para que seu modelo possa ser acessado por outras pessoas.

## <a name="experiment"></a>Experimento

Crie um experimento do zero ou use um experimento de exemplo existente como modelo.  Cada vez que você executa um experimento, os artefatos são armazenados em seu espaço de trabalho.

Um experimento consiste em conjuntos de valores e módulos analíticos, que você conecta juntos para construir um modelo. Especificamente, um teste válido possui três características:

* Os conjuntos de linhas só podem estar conectados a módulos.
* Os módulos podem estar conectados a conjuntos de os ou outros módulos.
* Todas as portas de entrada para módulos devem ter alguma conexão com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem ser definidos.


Para saber como começar a usar a interface visual, consulte [tutorial: Prever o preço de automóveis com a interface visual](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Conjunto de dados

Um conjunto de dados é dado que foi carregado na interface visual para uso no processo de modelagem. Vários conjuntos de exemplos de conjunto de informações são incluídos para você experimentar, e você pode carregar mais conjuntos de informações conforme necessário.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que você pode executar em seus dados. A interface visual tem vários módulos que variam de funções de entrada de dados para treinamento, pontuação e processos de validação.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Quando você seleciona um módulo na tela, os parâmetros do módulo são exibidos no painel Propriedades à direita da tela. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.

![Propriedades do módulo](media/ui-concept-visual-interface/properties.png)

Para obter ajuda para navegar pela biblioteca de algoritmos de aprendizado de máquina disponíveis, consulte [visão geral de referência do módulo & do algoritmo](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Recursos de computação

Use os recursos de computação do seu espaço de trabalho para executar seu experimento ou hospedar seus modelos implantados como serviços Web. Os destinos de computação com suporte são:


| Destino de computação | Treinamento | Implantação |
| ---- |:----:|:----:|
| Computação do Azure Machine Learning | ✓ | |
| Serviço de Kubernetes do Azure | | ✓ |

Os destinos de computação são anexados ao seu [espaço de trabalho](concept-workspace.md)do Machine Learning. Você gerencia seus destinos de computação em seu espaço de trabalho na [portal do Azure](https://portal.azure.com) ou na [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com).

## <a name="deployment"></a>Implantação

Quando seu modelo de análise preditiva estiver pronto, implante-o como um serviço Web diretamente da interface visual.

Os serviços Web fornecem uma interface entre um aplicativo e seu modelo de pontuação. Um aplicativo externo pode se comunicar com o modelo de pontuação em tempo real. Uma chamada para um serviço Web retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada a um serviço Web, você passa uma chave de API que foi criada quando você implantou o serviço Web. O serviço Web é baseado em REST, uma opção de arquitetura popular para projetos de programação da Web.

Para saber como implantar seu modelo, consulte [tutorial: Implante um modelo de aprendizado de máquina com a](ui-tutorial-automobile-price-deploy.md)interface visual.

## <a name="next-steps"></a>Próximas etapas

* Aprenda as noções básicas de análise preditiva e aprendizado de máquina [com o tutorial: Prever o preço de automóveis com a interface visual](ui-tutorial-automobile-price-train-score.md)
* Use um dos exemplos e modifique para a suíte de suas necessidades:
    * [Amostra 1-regressão: Preço de previsão](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [Exemplo 2-regressão: Prever o preço e comparar algoritmos](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exemplo 3-classificação: Prever risco de crédito](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [Amostra 4-classificação: Prever o risco de crédito (sensível ao custo)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exemplo 5-classificação: Prever a rotatividade, a desejo e a venda vertical](how-to-ui-sample-classification-predict-churn.md)
