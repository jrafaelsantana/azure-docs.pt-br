---
title: Como adicionar BLOBs a objetos – Azure digital gêmeos | Microsoft Docs
description: Saiba como adicionar blobs a objetos nos Gêmeos Digitais do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/01/2019
ms.custom: seodec18
ms.openlocfilehash: 3a278501f1110da0ab332d0e1acf170892be26ee
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949122"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Adicionar blobs a objetos nos Gêmeos Digitais do Azure

Os blobs são representações não estruturadas de tipos de arquivos comuns, como imagens e logs. Os blobs rastreiam o tipo de dados que eles representam usando um tipo MIME (por exemplo: "image/jpeg") e metadados (nome, descrição, tipo, etc.).

Os Gêmeos Digitais do Azure oferecem suporte à conexão de blobs a dispositivos, espaços e usuários. Os blobs podem representar uma foto de perfil para um usuário, uma foto do dispositivo, um vídeo, um mapa, um zip de firmware, dados JSON, um log etc.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Visão geral do upload de blobs

Você pode usar solicitações multipartes para fazer o upload de blobs para pontos de extremidade específicos e suas respectivas funcionalidades.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadados do blob

Além de **Content-Type** e **Content-Disposition**, as solicitações multipartes de blob dos Gêmeos Digitais do Azure devem especificar o corpo JSON correto. Qual corpo JSON a ser enviado depende do tipo de operação de solicitação HTTP que está sendo executada.

Os quatro esquemas JSON principais são:

[esquemas ![JSON](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

Metadados de blobs JSON são compatíveis com o seguinte modelo:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Atributo | type | Descrição |
| --- | --- | --- |
| **parentId** | String | A entidade pai a ser associada ao blob (espaços, dispositivos ou usuários) |
| **name** |String | Um nome amigável para humanos para o blob |
| **type** | String | O tipo de blob – não é possível usar *type* e *typeId*  |
| **typeId** | Integer | A ID do tipo de blob – não é possível usar *type* e *typeId* |
| **subtype** | String | O subtipo do blob – não é possível usar *subtype* e *subtypeId* |
| **subtypeId** | Integer | A ID do subtipo do blob – não é possível usar *subtype* e *subtypeId* |
| **description** | String | Descrição personalizada do blob |
| **sharing** | String | Se o blob pode ser compartilhado – enum [`None`, `Tree`, `Global`] |

Metadados de blob sempre são fornecidos como a primeira parte, com **Content-Type** `application/json` ou como um arquivo `.json`. Dados de arquivo são fornecidos na segunda parte e podem ser de qualquer tipo MIME com suporte.

A documentação do Swagger descreve esses esquemas de modelo em detalhes.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Aprenda a usar a documentação de referência lendo [Como usar o Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Dados de resposta de blobs

Blobs retornados individualmente estão em conformidade com o seguinte esquema JSON:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Atributo | type | Descrição |
| --- | --- | --- |
| **id** | String | O identificador exclusivo do blob |
| **name** |String | Um nome amigável para humanos para o blob |
| **parentId** | String | A entidade pai a ser associada ao blob (espaços, dispositivos ou usuários) |
| **type** | String | O tipo de blob – não é possível usar *type* e *typeId*  |
| **typeId** | Integer | A ID do tipo de blob – não é possível usar *type* e *typeId* |
| **subtype** | String | O subtipo do blob – não é possível usar *subtype* e *subtypeId* |
| **subtypeId** | Integer | A ID do subtipo do blob – não é possível usar *subtype* e *subtypeId* |
| **sharing** | String | Se o blob pode ser compartilhado – enum [`None`, `Tree`, `Global`] |
| **description** | String | Descrição personalizada do blob |
| **contentInfos** | Array | Especifica as informações de metadados não estruturados, incluindo a versão |
| **fullName** | String | O nome completo do blob |
| **spacePaths** | String | O caminho de espaço |

Metadados de blob sempre são fornecidos como a primeira parte, com **Content-Type** `application/json` ou como um arquivo `.json`. Dados de arquivo são fornecidos na segunda parte e podem ser de qualquer tipo MIME com suporte.

### <a name="blob-multipart-request-examples"></a>Exemplos de solicitação com várias partes do blob

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Para carregar um arquivo de texto como um blob e associá-lo a um espaço, faça uma solicitação HTTP POST autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Com o seguinte corpo:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Valor | Substitua por |
| --- | --- |
| USER_DEFINED_BOUNDARY | Um nome de limite de conteúdo com diversas partes |

O código a seguir é uma implementação .NET do mesmo upload do Blob que usa a classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Por fim, usuários de [cURL](https://curl.haxx.se/) podem fazer solicitações de formulário de várias partes da mesma maneira:

[blobs de @no__t 1Device](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Valor | Substitua por |
| --- | --- |
| YOUR_TOKEN | Seu token OAuth 2.0 válido |
| YOUR_SPACE_ID | A ID do espaço a ser associado ao blob |
| PATH_TO_FILE | O caminho para seu arquivo de texto |

Um POST bem-sucedido retorna a ID do novo blob (destacado em vermelho anteriormente).

## <a name="api-endpoints"></a>Pontos de extremidade de API

As seções a seguir descrevem os pontos de extremidade de API relacionados ao blob principal e suas funcionalidades.

### <a name="devices"></a>Dispositivos

Você pode anexar blobs aos dispositivos. A imagem a seguir mostra a documentação de referência do Swagger para suas APIs de Gerenciamento. Ela especifica os pontos de extremidade da API relacionados ao dispositivo para consumo de blob e qualquer parâmetro de caminho necessário para passar para eles.

[blobs de @no__t 1Device](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Por exemplo, para atualizar ou criar um blob e anexar o blob a um dispositivo, faça uma solicitação HTTP PATCH autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | A ID do blob desejado |

Solicitações bem-sucedidas retornam um objeto JSON como [descrito anteriormente](#blobs-response-data).

### <a name="spaces"></a>Espaços

Você também pode anexar blobs aos espaços. A imagem abaixo lista todos os pontos de extremidade da API de espaços responsáveis pela manipulação dos blobs. Lista também os parâmetros de caminho a serem passados para esses pontos de extremidade.

[blobs de @no__t 1Space](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Por exemplo, para retornar um blob anexado a um espaço, faça uma solicitação HTTP GET autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | A ID do blob desejado |

Solicitações bem-sucedidas retornam um objeto JSON como [descrito anteriormente](#blobs-response-data).

Uma solicitação PATCH para o mesmo ponto de extremidade atualiza descrições de metadados e cria versões do blob. A solicitação HTTP é feita usando o método PATCH juntamente com quaisquer dados de formulário meta e multipartes necessários.

### <a name="users"></a>Usuários

Você pode anexar os blobs aos modelos do usuário (por exemplo, para associar uma imagem de perfil). A imagem abaixo exibe os pontos de extremidade relevantes da API de usuários e quaisquer parâmetros de caminho obrigatório, como `id`:

[blobs de @no__t 1User](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Por exemplo, para buscar um blob anexado a um usuário, faça uma solicitação HTTP GET autenticada com todos os dados de formulário necessários para:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_BLOB_ID* | A ID do blob desejado |

Solicitações bem-sucedidas retornam um objeto JSON como [descrito anteriormente](#blobs-response-data).

## <a name="common-errors"></a>Erros comuns

Um erro comum envolve não fornecer as informações corretas do cabeçalho:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Para resolver esse erro, verifique se a solicitação geral tem um cabeçalho **Content-Type** adequado:

* `multipart/mixed`
* `multipart/form-data`

Além disso, verifique se cada parte com várias partes tem um **Content-Type** correspondente, conforme necessário.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a documentação de referência do Swagger para Gêmeos Digitais do Azure, leia [Como usar o Swagger dos Gêmeos Digitais do Azure](how-to-use-swagger.md).

- Para carregar blobs por meio do Postman, leia [Como configurar o Postman](./how-to-configure-postman.md).