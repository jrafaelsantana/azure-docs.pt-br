---
title: Associações para Funções Duráveis – Azure
description: Como usar gatilhos e associações para a extensão de Durable Functions do Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: f297c89d2c3ba5692a44fab631c0d46c75f48692
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033595"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associações para Funções Duráveis (Azure Functions)

A extensão [Funções Duráveis](durable-functions-overview.md) introduz duas novas associações de gatilho que controlam a execução de funções de orquestrador e atividade. Ela também introduz uma associação de saída que atua como um cliente para o tempo de execução das Funções Duráveis.

## <a name="orchestration-trigger"></a>Gatilho de orquestração

O gatilho de orquestração permite que você crie [funções de orquestrador duráveis](durable-functions-types-features-overview.md#orchestrator-functions). Esse gatilho dá suporte a iniciar novas instâncias de função de orquestrador e a retomar instâncias de função de orquestrador existentes que estão "aguardando" uma tarefa.

Quando você usa as ferramentas do Visual Studio para o Azure Functions, o gatilho de orquestração é configurado usando o atributo .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html).

Quando você escreve funções de orquestrador em linguagens de script (por exemplo, script em JavaScript ou C#), o gatilho de orquestração é definido pelo seguinte objeto JSON na matriz `bindings` do arquivo *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` é o nome da orquestração. Esse é o valor que os clientes devem usar quando quiserem iniciar novas instâncias dessa função de orquestrador. Essa propriedade é opcional. Se não for especificada, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão do aplicativo de funções. Essas filas são detalhes da implementação interna da extensão e, por isso, elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Veja algumas observações sobre o gatilho de orquestração:

* **Threading único** – um único thread dispatcher é usado para toda a execução de função de orquestrador em uma única instância de host. Por esse motivo, é importante garantir que o código da função de orquestrador seja eficiente e não execute nenhuma E/S. Também é importante garantir que esse thread não faça nenhum trabalho assíncrono, exceto ao aguardar tipos de tarefas específicas das Funções Duráveis.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas nos acionadores de orquestração.
* **Visibilidade da mensagem** – as mensagens do gatilho de orquestração são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores retornados** – os valores retornados são serializados em JSON e são persistidos na tabela de histórico de orquestração no Armazenamento de Tabelas do Azure. Esse valores retornados podem ser consultados pela associação do cliente de orquestração, descrita posteriormente.

> [!WARNING]
> Funções de orquestrador nunca devem usar nenhuma associação de entrada ou saída que não seja a associação do gatilho de orquestração. Fazer isso tem o potencial de causar problemas com a extensão de Tarefa Durável, pois essas associações podem não cumprir as regras de E/S e de thread único. Se você quiser usar outras associações, adicione-as a uma função de atividade chamada de sua função de orquestrador.

> [!WARNING]
> Funções de orquestrador JavaScript nunca devem ser declaradas como `async`.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de orquestração dá suporte a entradas e saídas. Estas são algumas coisas que você precisa saber sobre a manipulação de entradas e saídas:

* **entradas** – as funções de orquestração .NET dão suporte apenas a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) como um tipo de parâmetro. Não há suporte para entradas de desserialização diretamente na assinatura da função. O código deve usar o método [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) (.NET) ou `getInput` (JavaScript) para buscar as entradas de função de orquestrador. Essas entradas devem ser tipos serializáveis em JSON.
* **saídas** – gatilhos de orquestração dão suporte a valores de saída, bem como entradas. O valor retornado da função é usado para atribuir o valor de saída e deve ser serializável em JSON. Se uma função .NET retornar `Task` ou `void`, um valor `null` será salvo como a saída.

### <a name="trigger-sample"></a>Exemplo de gatilho

O código de exemplo a seguir mostra como a função de orquestrador "Olá, Mundo" mais simples pode ser semelhante a:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> O `context` objeto em JavaScript não representa o DurableOrchestrationContext, mas o [contexto de função como um todo](../functions-reference-node.md#context-object). Você pode acessar os métodos de orquestração por meio da propriedade `df` do objeto `context`.

> [!NOTE]
> Os orquestradores JavaScript devem usar `return`. A biblioteca `durable-functions` é responsável por chamar o método `context.done`.

A maioria das funções de orquestrador chamam funções de atividade, sendo assim, este é um exemplo de "Olá, Mundo" que demonstra como chamar uma função de atividade:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Gatilho de atividade

O gatilho de atividade permite que você crie funções que são chamadas por funções de orquestrador, conhecidas como [funções de atividade](durable-functions-types-features-overview.md#activity-functions).

Se você estiver usando o Visual Studio, o gatilho de atividade será configurado usando o atributo .NET [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html).

Se você estiver usando o VS Code ou o Portal do Azure para desenvolvimento, o gatilho de atividade será definido pelo seguinte objeto JSON na matriz `bindings` de *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` é o nome da atividade. Esse valor é o nome que as funções de orquestrador usam para invocar essa função de atividade. Essa propriedade é opcional. Se não for especificada, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma fila na conta de armazenamento padrão para o aplicativo de funções. Essa fila é um detalhe da implementação interna da extensão e, por isso, ela não é configurada explicitamente nas propriedades da associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Veja algumas observações sobre o gatilho de atividade:

* **Threading** – diferente do gatilho de orquestração, os gatilhos de atividade não têm restrições quanto a threading ou E/S. Eles podem ser tratados como funções regulares.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas nos gatilhos de atividade.
* **Visibilidade da mensagem** – as mensagens do gatilho de atividade são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores retornados** – os valores retornados são serializados em JSON e são persistidos na tabela de histórico de orquestração no Armazenamento de Tabelas do Azure.

> [!WARNING]
> O back-end de armazenamento das funções de atividade é um detalhe da implementação e o código do usuário não deve interagir diretamente com essas entidades de armazenamento.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de atividade dá suporte a entradas e saídas, assim como no caso do gatilho de orquestração. Estas são algumas coisas que você precisa saber sobre a manipulação de entradas e saídas:

* **entradas** – nativamente, as funções de atividade .NET usam [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como um tipo de parâmetro. Como alternativa, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que possa ser serializado em JSON. Quando usa `DurableActivityContext`, você pode chamar [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) para buscar e desserializar a entrada da função de atividade.
* **saídas** – Funções de atividade dão suporte a valores de saída, bem como entradas. O valor retornado da função é usado para atribuir o valor de saída e deve ser serializável em JSON. Se uma função .NET retornar `Task` ou `void`, um valor `null` será salvo como a saída.
* **metadados** – funções de atividade .NET podem ser associadas a um parâmetro `string instanceId` para obter a ID da instância da orquestração pai.

### <a name="trigger-sample"></a>Exemplo de gatilho

O código de exemplo a seguir mostra como uma função de atividade "Olá, Mundo" simples pode ser semelhante a:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

O tipo de parâmetro padrão para a associação `ActivityTriggerAttribute` .NET é `DurableActivityContext`. No entanto, os gatilhos de atividade .NET também dão suporte à associação direta com tipos serializáveis em JSON (incluindo tipos primitivos), de modo que a mesma função poderia ser simplificada da seguinte forma:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Associações de JavaScript também podem ser passadas como parâmetros adicionais para que a mesma função possa ser simplificada da seguinte maneira:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Usando associações de entrada e saída

Você pode usar associações de entrada e saída regulares, além da Associação de gatilho de atividade. Por exemplo, você pode pegar a entrada para sua associação de atividade e enviar uma mensagem para um EventHub usando a associação de saída do EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Cliente de orquestração

A associação de cliente de orquestração permite que você grave funções que interagem com funções de orquestrador. Essas funções são, às vezes, chamadas de [funções de cliente](durable-functions-types-features-overview.md#client-functions). Por exemplo, você pode atuar em instâncias de orquestração das maneiras a seguir:

* Iniciá-las.
* Consultar seu status.
* Encerrá-las.
* Enviar eventos a elas durante sua execução.
* Limpar o histórico de instância.

Se você estiver usando o Visual Studio, poderá associar ao cliente Orchestration usando o atributo [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net para Durable Functions 1,0. A partir da versão prévia do Durable Functions 2,0, você pode associar ao cliente Orchestration usando o `DurableClientAttribute` atributo .net.

Se você estiver usando linguagens de script (por exemplo, arquivos *. CSX* ou *. js* ) para desenvolvimento, o gatilho de orquestração será definido pelo seguinte `bindings` objeto JSON na matriz de *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` – usado em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão de `host.json` será usado. Esse valor deve corresponder ao valor usado pelas funções de orquestrador de destino.
* `connectionName` – O nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. A conta de armazenamento representada por essa cadeia de conexão deve ser a mesma usada pelas funções de orquestrador de destino. Se não for especificada, a cadeia de conexão da conta de armazenamento padrão do aplicativo de funções será usada.

> [!NOTE]
> Na maioria dos casos, é recomendável omitir essas propriedades e contar com o comportamento padrão.

### <a name="client-usage"></a>Uso do cliente

Em funções .NET, normalmente você associa a `DurableOrchestrationClient`, que oferece acesso completo a todas as APIs de cliente com suporte de Durable Functions. A partir do Durable Functions 2,0, você associa à `IDurableOrchestrationClient` interface. No JavaScript, as mesmas APIs são expostas pelo objeto retornado `getClient`de. APIs no objeto cliente incluem:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

Como alternativa, as funções .NET podem associar a `IAsyncCollector<T>`, em que `T` é [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) ou `JObject`.

Para obter mais informações sobre essas operações, consulte a documentação da API do [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) .

### <a name="client-sample-visual-studio-development"></a>Exemplo de cliente (desenvolvimento no Visual Studio)

Veja um exemplo de função disparada em fila que é iniciada com uma orquestração "Olá, Mundo".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Exemplo de cliente (fora do Visual Studio)

Se não estiver usando o Visual Studio para desenvolvimento, você poderá criar o seguinte arquivo *function.json*. Este exemplo mostra como configurar uma função disparada em fila que usa a associação de cliente de orquestração durável:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

A seguir, temos exemplos específico a um idioma que iniciam novas instâncias de função do orquestrador.

#### <a name="c-sample"></a>Exemplo do C#

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de função de uma função de script em C#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Exemplo de JavaScript

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de função de uma função JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Mais detalhes sobre como iniciar instâncias podem ser encontrados em [Gerenciamento de instâncias](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Gatilho de entidade

Os gatilhos de entidade permitem que você crie [funções de entidade](durable-functions-entities.md). Esse gatilho dá suporte ao processamento de eventos para uma instância de entidade específica.

Quando você usa as ferramentas do Visual Studio para Azure functions, o gatilho de entidade é configurado `EntityTriggerAttribute` usando o atributo .net.

> [!NOTE]
> Os gatilhos de entidade estão disponíveis no Durable Functions 2,0 e superior. Os gatilhos de entidade ainda não estão disponíveis para JavaScript.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão do aplicativo de funções. Essas filas são detalhes da implementação interna da extensão e, por isso, elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Aqui estão algumas observações sobre o gatilho de entidade:

* **Thread único**: Um único thread de Dispatcher é usado para processar operações para uma entidade específica. Se várias mensagens forem enviadas a uma única entidade simultaneamente, as operações serão processadas uma por vez.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas em gatilhos de entidade.
* **Visibilidade de mensagem** -as mensagens de gatilho de entidade são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores de retorno** -as funções de entidade não dão suporte a valores de retorno. Há APIs específicas que podem ser usadas para salvar os valores de estado ou passar de volta para orquestrações.

Qualquer alteração de estado feita em uma entidade durante sua execução será persistida automaticamente após a conclusão da execução.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

Cada função de entidade tem um tipo de `IDurableEntityContext`parâmetro, que tem os seguintes membros:

* **EntityName**: Obtém o nome da entidade atualmente em execução.
* **EntityKey**: Obtém a chave da entidade em execução no momento.
* **EntityId**: Obtém a ID da entidade em execução no momento.
* **OperationName**: Obtém o nome da operação atual.
* **IsNewlyConstructed**: retorna `true` se a entidade não existia antes da operação.
* **GetState\<TState > ()** : Obtém o estado atual da entidade. O `TState` parâmetro deve ser um tipo primitivo ou serializável por JSON.
* **SetState (objeto)** : atualiza o estado da entidade. O `object` parâmetro deve ser um objeto primitivo ou serializável por JSON.
* **Getinput\<TInput > ()** : Obtém a entrada para a operação atual. O `TInput` parâmetro de tipo deve representar um tipo primitivo ou serializável por JSON.
* **Return (Object)** : retorna um valor para a orquestração que chamou a operação. O `object` parâmetro deve ser um objeto primitivo ou serializável por JSON.
* **DestructOnExit ()** : exclui a entidade depois de concluir a operação atual.
* **SignalEntity (EntityId, String, Object)** : envia uma mensagem unidirecional para uma entidade. O `object` parâmetro deve ser um objeto primitivo ou serializável por JSON.

Ao usar o modo de programação de entidade baseada em classe `IDurableEntityContext` , o objeto pode ser referenciado usando a `Entity.Current` Propriedade thread-static.

### <a name="trigger-sample---entity-function"></a>Exemplo de gatilho – função de entidade

O código a seguir é um exemplo de uma entidade de *contador* simples implementada como uma função padrão. Essa função define três *operações*, `add` `reset`, e `get`, cada uma operando em um valor de estado inteiro, `currentValue`.

```csharp
[FunctionName(nameof(Counter))]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

### <a name="trigger-sample---entity-class"></a>Exemplo de gatilho – classe de entidade

O exemplo a seguir é uma implementação equivalente da entidade `Counter` anterior usando classes e métodos .net.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> O método de ponto de entrada de `[FunctionName]` função com o atributo `static` *deve* ser declarado ao usar classes de entidade. Métodos de ponto de entrada não estáticos podem resultar em várias inicializações de objeto e potencialmente outros comportamentos indefinidos.

As classes de entidade têm mecanismos especiais para interagir com associações e injeção de dependência do .NET. Para obter mais informações, consulte o artigo sobre [entidades duráveis](durable-functions-entities.md) .

## <a name="entity-client"></a>Cliente de entidade

A associação de cliente de entidade permite que você dispare de forma assíncrona [funções de entidade](#entity-trigger). Essas funções são, às vezes, chamadas de [funções de cliente](durable-functions-types-features-overview.md#client-functions).

Se você estiver usando o Visual Studio, poderá associar ao cliente da entidade usando o `DurableClientAttribute` atributo .net.

> [!NOTE]
> O `[DurableClientAttribute]` também pode ser usado para associar ao [cliente de orquestração](#orchestration-client).

Se você estiver usando linguagens de script (por exemplo, arquivos *. CSX* ou *. js* ) para desenvolvimento, o gatilho de entidade será definido pelo seguinte `bindings` objeto JSON na matriz de *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` – usado em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão de `host.json` será usado. Esse valor deve corresponder ao valor usado pelas funções de entidade de destino.
* `connectionName` – O nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. A conta de armazenamento representada por essa cadeia de conexão deve ser a mesma usada pelas funções de entidade de destino. Se não for especificada, a cadeia de conexão da conta de armazenamento padrão do aplicativo de funções será usada.

> [!NOTE]
> Na maioria dos casos, recomendamos que você omita as propriedades opcionais e dependa do comportamento padrão.

### <a name="entity-client-usage"></a>Uso de cliente de entidade

Em funções do .net, você normalmente se `IDurableEntityClient`associa a, que fornece acesso completo a todas as APIs de cliente com suporte de entidades duráveis. Você também pode associar à `IDurableClient` interface, que fornece acesso a APIs de cliente para entidades e orquestrações. APIs no objeto cliente incluem:

* **ReadEntityStateAsync\<T >** : lê o estado de uma entidade.
* **SignalEntityAsync**: envia uma mensagem unidirecional para uma entidade e aguarda que ela seja enfileirada.
* **SignalEntityAsync\<TEntityInterface >** :igual`TEntityInterface`a ,masusaumobjetoproxy`SignalEntityAsync` gerado do tipo.
* **CreateEntityProxy\<TEntityInterface >** : gera dinamicamente um proxy dinâmico do tipo `TEntityInterface` para fazer chamadas de tipo seguro para entidades.

> [!NOTE]
> É importante entender que as operações de "sinal" anteriores são assíncronas. Não é possível invocar uma função de entidade e recuperar um valor de retorno de um cliente. Da mesma forma `SignalEntityAsync` , o pode retornar antes que a entidade comece a executar a operação. Somente as funções de orquestrador podem invocar funções de entidade de forma síncrona e processar valores de retorno.

As `SignalEntityAsync` APIs exigem a especificação do identificador exclusivo da entidade como um `EntityId`. Essas APIs também usam opcionalmente o nome da operação de entidade como um `string` e a carga da operação como uma `object`serializável por JSON. Se a entidade de destino não existir, ela será criada automaticamente com a ID de entidade especificada.

### <a name="client-sample-untyped"></a>Exemplo de cliente (não tipado)

Aqui está um exemplo de função disparada por fila que invoca uma entidade "Counter".

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>Exemplo de cliente (digitado)

É possível gerar um objeto proxy para acesso de tipo seguro a operações de entidade. Para gerar um proxy de tipo seguro, o tipo de entidade deve implementar uma interface. Por exemplo, suponha que `Counter` a entidade mencionada anteriormente `ICounter` implementou uma interface, definida da seguinte maneira:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

O código do cliente poderia `SignalEntityAsync<TEntityInterface>` , então, `ICounter` usar e especificar a interface como o parâmetro de tipo para gerar um proxy de tipo seguro. Esse uso de proxies de tipo seguro é demonstrado no exemplo de código a seguir:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

No exemplo anterior, `proxy` o parâmetro é uma instância gerada dinamicamente do `ICounter`, que converte internamente a chamada para `Add` na chamada equivalente (não tipada) para `SignalEntityAsync`.

Há algumas regras para definir interfaces de entidade:

* O parâmetro `TEntityInterface` de tipo `SignalEntityAsync<TEntityInterface>` em deve ser uma interface.
* As interfaces de entidade devem definir apenas métodos.
* Os métodos de interface de entidade não devem definir mais de um parâmetro.
* Os métodos de interface de `void`entidade devem retornar `Task<T>` , `T` ou onde é um valor de `Task`retorno.
* As interfaces de entidade devem ter exatamente uma classe de implementação concreta dentro do mesmo assembly (ou seja, a classe de entidade).

Se qualquer uma dessas regras for violada, uma `InvalidOperationException` será lançada em tempo de execução. A mensagem de exceção explicará qual regra foi quebrada.

> [!NOTE]
> As `SignalEntityAsync` APIs representam operações unidirecionais. Se uma interface de entidade `Task<T>`retornar, o valor `T` do parâmetro será sempre nulo ou `default`.

<a name="host-json"></a>

## <a name="hostjson-settings"></a>configurações de host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de API HTTP interna para gerenciamento de instância](durable-functions-http-api.md)