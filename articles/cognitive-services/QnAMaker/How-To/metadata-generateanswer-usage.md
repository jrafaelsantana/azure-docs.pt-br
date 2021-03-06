---
title: Metadados com a API de GenerateAnswer – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite adicionar metadados na forma de pares chave-valor aos conjuntos de perguntas/respostas. Você pode filtrar os resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 03e04853e93bb78391476a365b20550d471e1dbb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971803"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obtenha uma resposta com a API e os metadados do GenerateAnswer

Para obter a resposta prevista para a pergunta de um usuário, use a API GenerateAnswer. Ao publicar uma base de dados de conhecimento, você pode ver informações sobre como usar essa API na página **publicar** . Você também pode configurar a API para filtrar as respostas com base nas marcas de metadados e testar a base de dados de conhecimento do ponto de extremidade com o parâmetro de cadeia de caracteres de consulta de teste.

QnA Maker permite adicionar metadados, na forma de pares de chave e valor, aos seus conjuntos de perguntas e respostas. Você pode usar essas informações para filtrar os resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento. Para mais informações, consulte a [Base de dados de conhecimento](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Armazenar perguntas e respostas com uma entidade QnA

É importante entender como QnA Maker armazena os dados de pergunta e resposta. A ilustração a seguir mostra uma entidade QnA:

![Ilustração de uma entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem uma ID exclusiva e persistente. Você pode usar a ID para fazer atualizações em uma entidade QnA específica.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obter previsões de resposta com a API GenerateAnswer

Você usa a [API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) em seu bot ou aplicativo para consultar sua base de dados de conhecimento com uma pergunta de usuário, para obter a melhor correspondência dos conjuntos de perguntas e respostas.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter ponto de extremidade GenerateAnswer 

Depois de publicar sua base de dados de conhecimento, seja no [portal de QnA Maker](https://www.qnamaker.ai)ou usando a [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), você pode obter os detalhes do seu ponto de extremidade GenerateAnswer.

Para obter os detalhes do ponto de extremidade:
1. Entre em [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Em **minhas bases de dados de conhecimento**, selecione **Exibir código** para sua base de dados de conhecimento.
    ![Screenshot de minhas bases de dados de conhecimento @ no__t-1
1. Obtenha os detalhes do ponto de extremidade do GenerateAnswer.

    ![Captura de tela dos detalhes do ponto de extremidade](../media/qnamaker-how-to-metadata-usage/view-code.png)

Também é possível obter os detalhes de ponto de extremidade na guia **Configurações** da base de dados de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de solicitação do GenerateAnswer

Você chama o GenerateAnswer com uma solicitação HTTP POST. Para código de exemplo que mostra como chamar o GenerateAnswer, consulte os [inícios rápidos](../quickstarts/create-publish-kb-csharp-sdk.md#generate-an-answer-from-the-knowledge-base). 

A solicitação POST usa:

* [Parâmetros de URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters) necessários
* [Propriedade de cabeçalho](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer)necessária, `Authorization`, para segurança
* [Propriedades do corpo](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)necessárias. 

A URL GenerateAnswer tem o seguinte formato: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Lembre-se de definir a propriedade de cabeçalho HTTP de `Authorization` com um valor da cadeia de caracteres `EndpointKey` com um espaço à direita, em seguida, a chave do ponto de extremidade foi encontrada na página de **configurações** .

Um exemplo de corpo JSON é semelhante a:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriedades de resposta do GenerateAnswer

A [resposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) é um objeto JSON, incluindo todas as informações de que você precisa para exibir a resposta e o próximo ativar a conversa, se disponível.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

## <a name="use-qna-maker-with-a-bot-in-c"></a>Usar QnA Maker com um bot emC#

A estrutura de bot fornece acesso às propriedades do QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

O bot de suporte tem [um exemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) com esse código.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Usar QnA Maker com um bot no node. js

A estrutura de bot fornece acesso às propriedades do QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

O bot de suporte tem [um exemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) com esse código.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usar metadados para filtrar respostas por marcas de metadados personalizadas

A adição de metadados permite filtrar as respostas por essas marcas de metadados. Adicione a coluna de metadados do menu de **Opções de exibição** . Adicione metadados à sua base de dados de conhecimento selecionando o ícone metadados **+** para adicionar um par de metadados. Esse par consiste em uma chave e um valor.

![Captura de tela da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com strictFilters para marcas de metadados

Considere a pergunta do usuário "quando este hotel é fechado?", em que a intenção é implícita para o restaurante "Paradise".

Como os resultados são necessários apenas para o restaurante "Paradise", você pode definir um filtro na chamada GenerateAnswer nos metadados "nome do restaurante". O exemplo a seguir mostra isso:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usar resultados de perguntas e respostas para manter o contexto da conversa

A resposta para o GenerateAnswer contém as informações de metadados correspondentes do conjunto de perguntas e respostas correspondentes. Você pode usar essas informações em seu aplicativo cliente para armazenar o contexto da conversa anterior para uso em conversas posteriores. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Somente correspondência de perguntas, por texto

Por padrão, QnA Maker pesquisa perguntas e respostas. Se você quiser pesquisar apenas as perguntas, para gerar uma resposta, use o `RankerType=QuestionOnly` no corpo da POSTAgem da solicitação GenerateAnswer.

Você pode pesquisar os KB publicados, usando `isTest=false`, ou na KB de teste usando `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Erros comuns de HTTP

|Código|Explicação|
|:--|--|
|2xx|Êxito|
|400|Os parâmetros da solicitação estão incorretos, o que significa que os parâmetros necessários estão ausentes, errados ou são muitos grandes|
|400|O corpo da solicitação está incorreto, o que significa que o JSON está ausente, errado ou é muito grande|
|401|Chave inválida|
|403|Proibido – você não tem as permissões corretas|
|404|O KB não existe|
|410|Essa API foi preterida e não está mais disponível|

## <a name="next-steps"></a>Próximas etapas

A página **publicar** também fornece informações para gerar uma resposta com o [postmaster](../Quickstarts/get-answer-from-kb-using-postman.md) e a [ondulação](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Criar um bot da base de dados de conhecimento](../tutorials/integrate-qnamaker-luis.md)
