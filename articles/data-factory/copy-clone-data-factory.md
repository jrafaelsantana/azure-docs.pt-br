---
title: Copiar ou clonar um data factory no Azure Data Factory | Microsoft Docs
description: Saiba como copiar ou clonar um data factory no Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 6e23cc583c9b403619ad2e47f2085ef7c0149bd2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142379"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Copiar ou clonar um data factory no Azure Data Factory

Este artigo descreve como copiar ou clonar um data factory no Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Casos de uso para clonar um data factory

Aqui estão algumas das circunstâncias em que você pode considerar útil copiar ou clonar um data factory:

-   **Renomeação de recursos**. O Azure não dá suporte para renomeação de recursos. Se você quiser renomear um data factory, poderá clonar o data factory com um nome diferente e, em seguida, excluir o já existente.

-   **Depurando alterações** quando os recursos de depuração não forem suficientes. Às vezes, para testar suas alterações, você pode querer testar as alterações em um alocador diferente antes de aplicá-las ao principal. Na maioria dos cenários, você pode usar a Depurar. Alterações em gatilhos, no entanto, como a maneira como suas alterações se comportam quando um gatilho é invocado automaticamente ou durante uma janela de tempo, podem não ser facilmente testáveis sem fazer check-in. Nesses casos, clonar o alocador e aplicar suas alterações lá faz muito sentido. Uma vez que o Azure Data Factory cobra principalmente pelo número de execuções, o segundo alocador não gera nenhum encargo adicional.

## <a name="how-to-clone-a-data-factory"></a>Para clonar um data factory

1. A interface do usuário do Data Factory no portal do Azure permite que você exporte todo o conteúdo do seu data factory para um modelo do Resource Manager, junto com um arquivo de parâmetro que permite alterar quaisquer valores que você queira alterar ao clonar seu alocador.

1. Como pré-requisito, você precisará criar seu data factory de destino do portal do Azure.

1. Se você tiver um SelfHosted IntegrationRuntime na fábrica de origem, você precisará precriá-lo com o mesmo nome na fábrica de destino. Se você quiser compartilhar o IRs do SelfHosted entre fábricas diferentes, poderá usar o padrão publicado [aqui](source-control.md#best-practices-for-git-integration).

1. Se você estiver no modo GIT, sempre que você publicar portal, o modelo do Resource Manager do alocador será no GIT no branch adf_publish do repositório.

1. Para outros cenários, o modelo do Resource Manager pode ser baixado clicando no botão **Exportar modelo do Resource Manager** no portal.

1. Depois de baixar o modelo do Resource Manager, você pode implantá-lo usando os métodos de implantação de modelo do Resource Manager.

1. Por motivos de segurança, o modelo do Resource Manager gerado não contém nenhuma informações secretas, como senhas para serviços vinculados. Assim, você precisa fornecer essas senhas como parâmetros de implantação. Se não for desejável fornecer os parâmetros, você precisará obter as cadeias de conexão e as senhas dos serviços vinculados do Azure Key Vault.

## <a name="next-steps"></a>Próximas etapas

Examine as diretrizes para a criação de um data factory no portal do Azure em [Criar um data factory usando a interface do usuário do Azure Data Factory](quickstart-create-data-factory-portal.md).
