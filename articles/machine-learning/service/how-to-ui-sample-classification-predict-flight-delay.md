---
title: 'Exemplo de interface visual #6: Classificação para prever atrasos de voo'
titleSuffix: Azure Machine Learning
description: Este artigo mostra como criar um modelo de aprendizado de máquina para prever atrasos de voo usando a interface visual do tipo "arrastar e soltar" e o código R personalizado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 257f6034df7d1974f3964c4d07ca96d17c7fe509
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131643"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Exemplo 6-classificação: Prever atrasos de voo usando o R

Esse experimento usa dados históricos de vôo e clima para prever se um vôo de passageiro agendado será atrasado em mais de 15 minutos.

Esse problema pode ser abordado como um problema de classificação, prevendo duas classes – atrasadas ou em tempo. Para criar um classificador, esse modelo usa um grande número de exemplos de dados de vôo históricos.

Este é o grafo de experimento final para este exemplo:

[![Grafo do experimento](media/how-to-ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o botão **abrir** para o experimento de exemplo 6:

    ![Abrir o experimento](media/how-to-ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Obter os dados

Esse experimento usa o conjunto de **dados de atrasos de voo** . Faz parte da coleta de dados TranStats dos EUA Departamento de transporte. O conjunto de dados contém informações de atraso de voo de abril a 2013 de outubro. Antes de carregar os dados na interface visual, ele foi processado previamente da seguinte maneira:

* Filtrado para incluir os aeroportos 70 mais ocupados no Estados Unidos continental.
* Para vôos cancelados, rerotulados como atrasados em mais de 15 minutos.
* Filtro de vôos de diversões filtrados.
* 14 colunas selecionadas.

Para complementar os dados de vôo, o conjunto de dado **meteorológico** é usado. Os dados meteorológicos contêm observações de clima com base em hora de NOAA e representam observações das estações de clima do aeroporto, cobrindo o mesmo período de abril a 2013. Antes de carregar para a interface visual do Azure ML, ela foi previamente processada da seguinte maneira:

* As IDs da estação do tempo foram mapeadas para as IDs correspondentes do aeroporto.
* As estações de clima não associadas a 70 aeroportos mais ocupados foram removidas.
* A coluna de data foi dividida em colunas separadas: Ano, mês e dia.
* As 26 colunas selecionadas.

## <a name="pre-process-the-data"></a>Pré-processar os dados

Um conjunto de um DataSet geralmente requer algum pré-processamento antes que possa ser analisado.

![processo de dados](media/how-to-ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Dados de vôo

As colunas **Carrier**, **OriginAirportID**e **DestAirportID** são salvas como inteiros. No entanto, eles são atributos categóricos, use o módulo **Editar metadados** para convertê-los em categóricos.

![editar metadados](media/how-to-ui-sample-classification-predict-flight-delay/edit-metadata.png)

Em seguida, use o módulo **selecionar colunas** no conjunto de DataSet para excluir das colunas do conjunto de linhas que são possíveis vazamentores de destino: **DepDelay**, **DepDel15**, **ArrDelay**, **cancelado**, **ano**. 

Para unir os registros de vôo com os registros meteorológicos por hora, use a hora de partida agendada como uma das chaves de junção. Para fazer a junção, a coluna CSRDepTime deve ser arredondada para a hora mais próxima, que é feita pelo no módulo **Executar script R** . 

### <a name="weather-data"></a>Dados de clima

Colunas que têm uma grande proporção de valores ausentes são excluídas usando o módulo **colunas do projeto** . Essas colunas incluem todas as colunas com valor de cadeia de caracteres: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**e **StationPressure**.

O módulo **limpar dados ausentes** é aplicado às colunas restantes para remover linhas com dados ausentes.

Os tempos de observação do clima são arredondados para a hora completa mais próxima. Os tempos de vôo agendados e os tempos de observação do clima são arredondados em direções opostas para garantir que o modelo Use apenas o clima antes do tempo de vôo. 

Como os dados meteorológicos são relatados na hora local, as diferenças de fuso horário são contabilizadas subtraindo-se as colunas de fuso horário do horário de partida agendado e o tempo de observação do clima. Essas operações são feitas usando o módulo **Executar script R** .

### <a name="joining-datasets"></a>Unindo conjuntos de os

Os registros de voo são Unidos com dados meteorológicos na origem do vôo (**OriginAirportID**) usando o módulo **unir dados** .

 ![Junte-se a voo e clima por origem](media/how-to-ui-sample-classification-predict-flight-delay/join-origin.png)


Os registros de voo são Unidos com dados meteorológicos usando o destino do vôo (**DestAirportID**).

 ![Unir voo e clima por destino](media/how-to-ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Preparando exemplos de treinamento e teste

O módulo **dividir dados** divide os dados em abril até registros de setembro para treinamento e registros de outubro para teste.

 ![Dividir dados de treinamento e teste](media/how-to-ui-sample-classification-predict-flight-delay/split.png)

As colunas year, month e TimeZone são removidas do conjunto de módulos de treinamento usando o módulo selecionar colunas.

## <a name="define-features"></a>definir recursos

No aprendizado de máquina, os recursos são propriedades mensuráveis individuais de algo no qual você está interessado. Encontrar um conjunto forte de recursos requer experimentação e conhecimento do domínio. Alguns recursos são melhores para prever o destino do que outros. Além disso, alguns recursos podem ter uma correlação forte com outros recursos e não adicionarão novas informações ao modelo. Esses recursos podem ser removidos.

Para criar um modelo, você pode usar todos os recursos disponíveis ou selecionar um subconjunto dos recursos.

## <a name="choose-and-apply-a-learning-algorithm"></a>escolher e aplicar um algoritmo de aprendizado

Crie um modelo usando o módulo **regressão logística de duas classes** e treine-o no conjunto de módulos de treinamento. 

O resultado do módulo **modelo de treinamento** é um modelo de classificação treinado que pode ser usado para pontuar novas amostras para fazer previsões. Use o conjunto de teste para gerar pontuações dos modelos treinados. Em seguida, use o módulo **avaliar modelo** para analisar e comparar a qualidade dos modelos.

Depois de executar o experimento, você pode exibir a saída do módulo **modelo de Pontuação** clicando na porta de saída e selecionando **Visualizar**. A saída inclui os rótulos pontuados e as probabilidades para os rótulos.

Por fim, para testar a qualidade dos resultados, adicione o módulo **avaliar modelo** à tela do experimento e conecte a porta de entrada à esquerda à saída do módulo modelo de pontuação. Execute o experimento e exiba a saída do módulo **modelo de avaliação** , clicando na porta de saída e selecionando **Visualizar**.

## <a name="evaluate"></a>Avaliar
O modelo de regressão logística tem AUC de 0,631 no conjunto de teste.

 ![avaliar](media/how-to-ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Próximas etapas

Explore os outros exemplos disponíveis para a interface visual:

- [Amostra 1-regressão: Prever o preço de um automóvel](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2-regressão: Comparar algoritmos para previsão de preço de automóvel](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação: Prever risco de crédito](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Amostra 4-classificação: Prever o risco de crédito (sensível ao custo)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-ui-sample-classification-predict-churn.md)
