---
title: Transformação de Chave Alternativa do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Como usar a transformação de chave substituta do fluxo de dados de mapeamento de Azure Data Factory para gerar valores de chave sequencial
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 45e2d35a3b0a3f3c89913bbe70d7c43c17cbcee0
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029183"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapeando a transformação de chave substituta do fluxo de dados



Use a Transformação de Chave Alternativa para adicionar um valor de chave crescente, arbitrário e não comercial ao conjunto de linhas de seu fluxo de dados. Isso é útil durante a criação de tabelas de dimensões em um modelo de dados analítico de esquema em estrela em que cada membro das tabelas precise ter uma chave exclusiva que seja não comercial, parte importante da metodologia Kimball de DWs.

![Transformar Chave Alternativa](media/data-flow/surrogate.png "Transformação de Chave Alternativa")

"Coluna de Chave" é o nome que você dará à sua nova coluna de chave alternativa.

"Valor Inicial" é o ponto de partida do valor incremental.

## <a name="increment-keys-from-existing-sources"></a>Incrementar chaves de fontes existentes

Se você quiser iniciar sua sequência a partir de um valor que existe em uma fonte, poderá usar uma transformação de coluna derivada imediatamente após a transformação de chave substituta e adicionar os dois valores juntos:

![SK Add Max](media/data-flow/sk006.png "substituto Key Transformation Add Max")

Para propagar o valor de chave com o máximo anterior, há duas técnicas que você pode usar:

### <a name="database-sources"></a>Fontes de banco de dados

Use a opção "consulta" para selecionar MAX () de sua origem usando a transformação origem:

Consulta de ![chave]alternativa consulta de(media/data-flow/sk002.png "transformação de chave substituta")

### <a name="file-sources"></a>Fontes de arquivo

Se o valor máximo anterior estiver em um arquivo, você poderá usar sua transformação de origem junto com uma transformação agregação e usar a função de expressão MAX () para obter o valor máximo anterior:

Arquivo de chave(media/data-flow/sk008.png "substituta") do ![arquivo de chave substituta]

Em ambos os casos, você deve unir seus novos dados de entrada junto com sua fonte que contém o valor máximo anterior:

(media/data-flow/sk004.png "Junção de chave substituta") de ![junção de chave substituta]

## <a name="next-steps"></a>Próximas etapas

Esses exemplos usam as transformações de [junção](data-flow-join.md) e [coluna derivada](data-flow-derived-column.md) .
