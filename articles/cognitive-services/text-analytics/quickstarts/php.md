---
title: 'Início Rápido: Usando PHP para chamar a API de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Análise de Texto nos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: a1e3484c544478fa6e9414eea1e139b81652ace3
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135003"
---
# <a name="quickstart-using-php-to-call-the-text-analytics-cognitive-service"></a>Início Rápido: Usando PHP para chamar o Serviço Cognitivo de Análise de Texto
<a name="HOLTop"></a>

Este artigo mostra como [detectar o idioma](#Detect), [analisar sentimento](#SentimentAnalysis), [extrair frases-chave](#KeyPhraseExtraction) e [identificar entidades vinculadas](#Entities) usando as  [APIs de Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711)  com PHP.

Consulte as [definições da API](//go.microsoft.com/fwlink/?LinkID=759346) para obter a documentação técnica das APIs.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Detectar o idioma

A API de Detecção de Idioma detecta o idioma de um documento de texto, usando o [método Detectar Idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Criar variáveis de ambiente `TEXT_ANALYTICS_SUBSCRIPTION_KEY` e `TEXT_ANALYTICS_ENDPOINT` para a chave de assinatura e o Ponto de Extremidade do Azure do seu recurso. Se você tiver criado essas variáveis de ambiente depois que começou a editar o aplicativo, precisará fechar e reabrir o editor, o IDE ou o shell que você está usando para acessar as variáveis.
1. Crie um projeto PHP em seu IDE favorito.
1. Adicione o código fornecido abaixo.
1. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/languages';

function DetectLanguage ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'text' => 'This is a document written in English.' ),
        array ( 'id' => '2', 'text' => 'Este es un document escrito en Español.' ),
        array ( 'id' => '3', 'text' => '这是一个用中文写的文件')
    )
);

print "Please wait a moment for the results to appear.";

$result = DetectLanguage ($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Resposta da detecção de idioma**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analisar sentimento

A API de Análise de Sentimento detecta o sentimento de um conjunto de registros de texto usando o [método Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). O exemplo a seguir pontua dois documentos, um em inglês e outro em espanhol.

1. Criar variáveis de ambiente `TEXT_ANALYTICS_SUBSCRIPTION_KEY` e `TEXT_ANALYTICS_ENDPOINT` para a chave de assinatura e o Ponto de Extremidade do Azure do seu recurso. Se você tiver criado essas variáveis de ambiente depois que começou a editar o aplicativo, precisará fechar e reabrir o editor, o IDE ou o shell que você está usando para acessar as variáveis.
1. Crie um projeto PHP em seu IDE favorito.
1. Adicione o código fornecido abaixo.
1. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/sentiment';

function GetSentiment ($host, $path, $key, $data) {
    // Make sure all text is UTF-8 encoded.
    foreach ($data as &$item) {
        foreach ($item as $ignore => &$value) {
            $value['text'] = utf8_encode($value['text']);
        }
    }

    $data = json_encode ($data);

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . strlen($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetSentiment($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Resposta da análise de sentimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extraia frases-chave

A API de Extração de Frases-chave extrai as frases-chave de um documento de texto, usando o [método Frases-chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). O exemplo a seguir extrai frases-chave para documentos em inglês e em espanhol.

1. Criar variáveis de ambiente `TEXT_ANALYTICS_SUBSCRIPTION_KEY` e `TEXT_ANALYTICS_ENDPOINT` para a chave de assinatura e o Ponto de Extremidade do Azure do seu recurso. Se você tiver criado essas variáveis de ambiente depois que começou a editar o aplicativo, precisará fechar e reabrir o editor, o IDE ou o shell que você está usando para acessar as variáveis.
1. Crie um projeto PHP em seu IDE favorito.
1. Adicione o código fornecido abaixo.
1. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/keyPhrases';

function GetKeyPhrases ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' ),
        array ( 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetKeyPhrases($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Resposta da extração de frases-chave**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identificar entidades 

A API de Entidades identifica as entidades conhecidas em um documento de texto, usando o [método de Entidades](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). O exemplo a seguir identifica as entidades de documentos em inglês.

1. Criar variáveis de ambiente `TEXT_ANALYTICS_SUBSCRIPTION_KEY` e `TEXT_ANALYTICS_ENDPOINT` para a chave de assinatura e o Ponto de Extremidade do Azure do seu recurso. Se você tiver criado essas variáveis de ambiente depois que começou a editar o aplicativo, precisará fechar e reabrir o editor, o IDE ou o shell que você está usando para acessar as variáveis.
1. Crie um projeto PHP em seu IDE favorito.
1. Adicione o código fornecido abaixo.
1. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
if (!getenv($key_var)) {
    throw new Exception ("Please set/export the following environment variable: $key_var");
} else {
    $subscription_key = getenv($key_var);
}
$endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
if (!getenv($endpoint_var)) {
    throw new Exception ("Please set/export the following environment variable: $endpoint_var");
} else {
    $endpoint = getenv($endpoint_var);
}

$path = '/text/analytics/v2.1/entities';

function GetEntities ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . Length($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";
    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is and It company.' ),
    )
);

print "Please wait a moment for the results to appear.";

$result = GetEntities($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Resposta de extração de entidade**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também 

 [Visão geral da Análise de Texto](../overview.md)  
 [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)
