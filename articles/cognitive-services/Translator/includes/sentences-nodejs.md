---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: d7126acebae02a60e5c485b74716aaa84b99f781
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906873"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos necessários

Crie um novo projeto usando seu IDE ou editor favorito. Em seguida, copie esse snippet de código para seu projeto em um arquivo denominado `sentence-length.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Se você nunca usou esses módulos, você precisará instalá-los antes de executar o programa. Para instalar esses pacotes, execute: `npm install request uuidv4`.

Esses módulos são necessários para construir a solicitação HTTP e criar um identificador exclusivo para o cabeçalho `'X-ClientTraceId'`.

## <a name="set-the-subscription-key-and-endpoint"></a>Definir a chave de assinatura e o ponto de extremidade

Este exemplo tentará ler a chave da sua assinatura e o ponto de extremidade de Tradução de Texto com base nestas variáveis de ambiente: `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` e `TRANSLATOR_TEXT_ENDPOINT`. Se você não estiver familiarizado com as variáveis de ambiente, poderá definir `subscriptionKey` e `endpoint` como cadeias de caracteres e comentar as instruções condicionais.

Copie este código em seu projeto:

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>Configurar a solicitação

O método `request()`, disponibilizado por meio do módulo de solicitação, nos permite passar o método HTTP, a URL, os parâmetros de solicitação, os cabeçalhos e o JSON do corpo como um objeto `options`. Neste snippet de código, configuraremos a solicitação:

>[!NOTE]
> Para saber mais sobre pontos de extremidade, rotas e parâmetros de solicitação, confira [API de Tradução de Texto 3.0: Interromper a frase](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'breaksentence',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'How are you? I am fine. What did you do today?'
    }],
    json: true,
};
```

A maneira mais fácil de autenticar uma solicitação é transmitir sua chave de assinatura como um cabeçalho `Ocp-Apim-Subscription-Key`, que é o que usamos neste exemplo. Como alternativa, você pode trocar sua chave de assinatura por um token de acesso e passar o token de acesso como um cabeçalho `Authorization` para validar sua solicitação.

Se estiver usando uma assinatura de vários serviço cognitivos, você também deve incluir o `Ocp-Apim-Subscription-Region` em seus cabeçalhos de solicitação.

Para obter mais informações, consulte [Autenticação](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Fazer a solicitação e imprimir a resposta

Em seguida, vamos criar a solicitação usando o método `request()`. Ele usa o objeto `options` que criamos na seção anterior como o primeiro argumento e, em seguida, imprime a resposta JSON "embelezada".

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> Neste exemplo, estamos definindo a solicitação HTTP no objeto `options`. No entanto, o módulo de solicitação também dá suporte a métodos de conveniência, como `.post` e `.get`. Para obter mais informações, confira [Métodos de conveniência](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Colocar tudo isso junto

É isso; você montou um programa simples que chamará a API de Tradução de Texto e retornará uma resposta JSON. Agora é hora de executar o programa:

```console
node sentence-length.js
```

Se você quiser comparar seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Resposta de exemplo

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você embutiu sua chave de assinatura no programa, remova-a quando tiver terminado este início rápido.

## <a name="next-steps"></a>Próximas etapas

Confira a referência da API para saber tudo o que você pode fazer com a API de Tradução de Texto.

> [!div class="nextstepaction"]
> [Referência de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
