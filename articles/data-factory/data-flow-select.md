---
title: Transformação de seleção do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de seleção do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 289f98fdc2f39449cdeede9ee46fb39847ae2cb5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029277"
---
# <a name="mapping-data-flow-select-transformation"></a>Mapeando fluxo de dados selecionar transformação


Use essa transformação para a seletividade da coluna (reduzindo o número de colunas), colunas de alias e nomes de fluxo e reordenar colunas.

## <a name="how-to-use-select-transformation"></a>Como usar a transformação selecionar
A Transformação de seleção permite que você crie um alias para um fluxo inteiro ou para colunas nesse fluxo, atribua nomes diferentes (aliases) e, em seguida, referencie esses novos nomes posteriormente em seu fluxo de dados. Essa transformação é útil para cenários de autojunção. Uma maneira de implementar uma autojunção no Fluxo de Dados do ADF é usar um fluxo, ramificá-lo com “Novo branch” e, imediatamente depois, adicionar uma transformação “de seleção”. Esse fluxo agora terá um novo nome que pode ser usado para ingressar de volta ao fluxo original, criando uma autojunção:

![Autojunção](media/data-flow/selfjoin.png "Autojunção")

No diagrama acima, a Transformação de seleção está na parte superior. Isso é atribuir alias ao fluxo original como “OrigSourceBatting”. Na transformação junção realçada abaixo dela, você pode ver que usamos essa transmissão de alias Select como a junção à direita, permitindo que possamos fazer referência à mesma chave tanto no lado esquerdo & direito da junção interna.

A seleção também pode ser usada como uma maneira de anular a seleção de colunas do seu fluxo de dados. Por exemplo, se você tiver seis colunas definidas no seu coletor, mas desejar escolher apenas três específicas para transformar e, em seguida, fluir para o coletor, será possível selecionar apenas essas três usando a transformação de seleção.

![Transformação de seleção](media/data-flow/newselect1.png "Alias de seleção")

## <a name="options"></a>Opções
* A configuração padrão para "Seleção" é incluir todas as colunas de entrada e manter esses nomes originais. É possível atribuir um alias ao fluxo definindo o nome da transformação de seleção.
* Para atribuir alias a colunas individuais, desmarque “Selecionar tudo” e use o mapeamento de colunas na parte inferior.
* Escolha ignorar duplicatas para eliminar colunas duplicadas dos metadados de entrada ou saída.

![Ignorar]duplicatas(media/data-flow/select-skip-dup.png "ignoram duplicatas")

* Quando você opta por ignorar duplicatas, os resultados ficarão visíveis na guia inspecionar. O ADF manterá a primeira ocorrência da coluna e você verá que cada ocorrência subsequente dessa mesma coluna foi removida do seu fluxo.

> [!NOTE]
> Para limpar as regras de mapeamento, pressione o botão **Redefinir** .

## <a name="mapping"></a>Mapeamento
Por padrão, a transformação selecionar mapeará automaticamente todas as colunas, que passarão por todas as colunas de entrada para o mesmo nome na saída. O nome do fluxo de saída definido em selecionar configurações definirá um novo nome de alias para o fluxo. Se você mantiver a seleção definir para mapa automático, poderá alias de todo o fluxo com todas as colunas iguais.

![Selecionar regras de transformação](media/data-flow/rule2.png "mapeamento baseado em regras")

Se você quiser criar um alias, remover, renomear ou reordenar colunas, primeiro será necessário desligar o "mapa automático". Por padrão, você verá uma regra padrão inserida para você chamada "todas as colunas de entrada". Você pode deixar essa regra em vigor se pretende sempre permitir que todas as colunas de entrada sejam mapeadas para o mesmo nome em sua saída.

No entanto, se você quiser adicionar regras personalizadas, clique em "Adicionar mapeamento". O mapeamento de campo fornecerá uma lista de nomes de coluna de entrada e saída para mapear e alias. Escolha "mapeamento baseado em regras" para criar regras de correspondência de padrões.

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras
Ao escolher o mapeamento baseado em regras, você está instruindo o ADF a avaliar sua expressão de correspondência para corresponder às regras de padrão de entrada e definir os nomes de campo de saída. Você pode adicionar qualquer combinação de mapeamentos com base em campo e em regra. Os nomes de campo são então gerados em tempo de execução pelo ADF com base nos metadados de entrada da origem. Você pode exibir os nomes dos campos gerados durante a depuração e usando o painel de visualização de dados.

Mais detalhes sobre a correspondência de padrões estão disponíveis na [documentação do padrão de coluna](concepts-data-flow-column-pattern.md).

## <a name="next-steps"></a>Próximas etapas
* Depois de usar Select para renomear, reordenar e alias de colunas, use a [transformação do coletor](data-flow-sink.md) para colocar seus dados em um repositório de dados.
