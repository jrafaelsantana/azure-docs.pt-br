---
title: Singletons para Funções Duráveis – Azure
description: Como usar singletons na extensão Durable Functions do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: ba35999d5a7193ba691b14005dc8271120ac2be7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933221"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Funções Duráveis (Azure Functions)

Para trabalhos em segundo plano, normalmente, você precisa garantir que apenas uma instância de determinado orquestrador seja executada por vez. Isso pode ser feito nas [Funções Duráveis](durable-functions-overview.md) atribuindo uma ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo de singleton

Os exemplos de C# e JavaScript a seguir mostram uma função de gatilho HTTP que cria uma orquestração singleton de trabalho em segundo plano. O código garante que apenas uma instância exista para uma ID de instância especificada.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

Aqui está o arquivo function.json:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Aqui está o código JavaScript:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Por padrão, IDs de instância são GUIDs gerados aleatoriamente. Mas, nesse caso, a ID de instância é passada nos dados da rota da URL. O código chama [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) ou `getStatus` (JavaScript) para verificar se uma instância com a ID especificada já está em execução. Se não estiver, uma instância será criada com essa ID.

> [!NOTE]
> Há uma condição de corrida potencial neste exemplo. Se duas instâncias de **HttpStartSingle** forem executadas simultaneamente, as chamadas de função relatarão êxito, mas somente uma instância de orquestração será realmente iniciada. Dependendo dos seus requisitos, isso pode ter efeitos colaterais indesejáveis. Por esse motivo, é importante garantir que nenhum duas solicitações podem executar essa função de gatilho simultaneamente.

Os detalhes da implementação da função de orquestrador, na verdade, não importam. Poderia ser uma função de orquestrador regular que é iniciada e concluída ou poderia ser uma que é executada eternamente (ou seja, um [Orquestração Eterna](durable-functions-eternal-orchestrations.md)). O aspecto importante é que há somente uma instância em execução por vez.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os recursos HTTP nativos de orquestrações](durable-functions-http-features.md)
