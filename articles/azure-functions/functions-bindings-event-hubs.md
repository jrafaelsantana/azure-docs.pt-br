---
title: Associações de Hubs de Eventos do Azure para o Azure Functions
description: Entenda como usar associações dos Hubs de Eventos do Azure no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: bc75ad08716a001ae0cfd934dbc8d5da668dc1c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086647"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Associações de Hubs de Eventos do Azure para o Azure Functions

Este artigo explica como trabalhar com associações de [Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) para o Azure Functions. O Azure Functions dá suporte a associações de gatilho e de saída para os Hubs de Eventos.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

Para a versão de funções do Azure 1. x, em que as associações de Hubs de eventos são fornecidas a [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) pacote NuGet, versão 2. x.
O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

Para funções 2. x, use o [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) pacote, versão 3. x.
O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
