---
title: 'Floresta de decisão multiclasse: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo floresta de decisão multiclasse no serviço Azure Machine Learning para criar um modelo de aprendizado de máquina com base no algoritmo de *floresta de decisão* .
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b56f08dfd1a14ffedffb612bb8974086ee08ede7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128608"
---
# <a name="multiclass-decision-forest-module"></a>Módulo de floresta de decisão multiclasse

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para criar um modelo de aprendizado de máquina com base no algoritmo de *floresta de decisão* . Uma floresta de decisão é um modelo Ensemble que cria rapidamente uma série de árvores de decisão e, ao mesmo tempo, aprende com dados marcados.

## <a name="more-about-decision-forests"></a>Mais sobre as florestas de decisão

O algoritmo de floresta de decisão é um método de aprendizado Ensemble para classificação. O algoritmo funciona criando várias árvores de decisão e, em seguida, votando a classe de saída mais popular. A votação é uma forma de agregação, na qual cada árvore em uma floresta de decisão de classificação gera um histograma de frequência não normalizado de rótulos. O processo de agregação soma esses histogramas e normaliza o resultado para obter as "probabilidades" para cada rótulo. As árvores que têm alta confiança de previsão têm um peso maior na decisão final do Ensemble.

As árvores de decisão em geral são modelos não paramétricos, o que significa que dão suporte a dados com distribuições variadas. Em cada árvore, uma sequência de testes simples é executada para cada classe, aumentando os níveis de uma estrutura de árvore até que um nó folha (decisão) seja atingido.

As árvores de decisão têm muitas vantagens:

+ Eles podem representar limites de decisão não lineares.
+ Eles são eficientes na computação e no uso de memória durante o treinamento e a previsão.
+ Eles executam seleção e classificação de recursos integrados.
+ Eles são resilientes na presença de recursos ruidosas.

O classificador de floresta de decisão em Azure Machine Learning consiste em um Ensemble de árvores de decisão. Em geral, os modelos de Ensemble fornecem melhor cobertura e precisão do que árvores de decisão única. Para obter mais informações, consulte [árvores de decisão](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Como configurar a floresta de decisão multiclasse



1. Adicione o módulo **floresta de decisão multiclasse** ao seu experimento na interface. Você pode encontrar esse módulo em **Machine Learning**, **inicializar modelo**e **classificação**.

2. Clique duas vezes no módulo para abrir o painel **Propriedades** .

3. Para o **método**de reamostragem, escolha o método usado para criar as árvores individuais.  Você pode escolher entre bagging ou replicação.

    + **Bagging**: Bagging também é chamado de agregação de *Bootstrap*. Nesse método, cada árvore é expandida em um novo exemplo, criada pela amostragem aleatória do conjunto de datas original com substituição, até que você tenha um conjunto de um DataSet com o tamanho original. As saídas dos modelos são combinadas por *votação*, que é uma forma de agregação. Para obter mais informações, consulte a entrada da Wikipédia para agregação de bootstrap.

    + **Replicar**: Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado de divisão é usado para cada nó de árvore permanece aleatório, criando árvores diversas.

   

4. Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .

    + **Parâmetro único**: Selecione esta opção se você souber como deseja configurar o modelo e fornecer um conjunto de valores como argumentos.


5. **Número de árvores de decisão**: Digite o número máximo de árvores de decisão que podem ser criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento pode aumentar.

    Esse valor também controla o número de árvores exibidas nos resultados, ao visualizar o modelo treinado. Para ver ou imprimir uma única árvore, você pode definir o valor como 1; no entanto, isso significa que apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.

6. **Profundidade máxima das árvores de decisão**: Digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de algum superajuste e um tempo de treinamento maior.

7. **Número de divisões aleatórias por nó**: Digite o número de divisões a ser usado ao compilar cada nó da árvore. Uma *divisão* significa que os recursos em cada nível da árvore (nó) são divididos aleatoriamente.

8. **Número mínimo de amostras por nó folha**: Indique o número mínimo de casos necessários para criar qualquer nó de terminal (folha) em uma árvore. Ao aumentar esse valor, você aumenta o limite para a criação de novas regras.

    Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos cinco casos que atendam às mesmas condições.



10. Conecte um DataSet rotulado e um dos módulos de treinamento:

    + Se você definir **criar modo de instrutor** como **um único parâmetro**, use o módulo [treinar modelo](./train-model.md) .

11. Execute o experimento.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, clique com o botão direito do mouse na saída do módulo [modelo de treinamento](./train-model.md) e selecione **Visualizar**.
+ Para ver as regras para cada nó, clique em cada árvore para fazer uma busca detalhada nas divisões.


## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 