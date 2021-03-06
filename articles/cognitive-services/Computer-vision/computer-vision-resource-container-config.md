---
title: Configure os contêineres - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Defina várias configurações para contêineres de Reconhecimento de Texto na Pesquisa Visual Computacional.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: aba846ade9e2b5e19304df87ea3e29713aacf4ba
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129958"
---
# <a name="configure-computer-vision-docker-containers"></a>Configurar contêineres do Docker Pesquisa Visual Computacional

Você configura o ambiente de tempo de execução do contêiner de `docker run` pesquisa Visual computacional usando os argumentos de comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Parâmetros de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A `ApiKey` configuração especifica a chave `Cognitive Services` de recurso do Azure usada para rastrear informações de cobrança para o contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado [`Billing`](#billing-configuration-setting) para a definição de configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Serviços cognitivas** Gerenciamento de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A `Billing` configuração especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Serviços cognitivas** Visão geral, rotulada`Endpoint`

Lembre-se de `vision/v1.0` adicionar o roteamento ao URI do ponto de extremidade, conforme mostrado na tabela a seguir. 

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de registro em log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres de Pesquisa Visual Computacional não usam montagens de entrada ou saída para armazenar dados de treinamento ou serviço. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, a localização de montagem do [computador host](computer-vision-how-to-install-containers.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões da localização de montagem do host. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia | Os contêineres de Pesquisa Visual Computacional não usam isso.|
|Opcional| `Output` | Cadeia | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](computer-vision-how-to-install-containers.md#stop-the-container).

* **Caractere de continuação de linha**: Os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: Não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de `Computer Vision` extremidade do recurso na `Computer Vision` página de chaves do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está `Computer Vision` disponível na página Visão geral do Azure.| Consulte [coletando parâmetros necessários](computer-vision-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](computer-vision-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página `Cognitive Services` de chaves de recurso do Azure.

## <a name="container-docker-examples"></a>Exemplos de Docker de contêiner

#### <a name="readtabread"></a>[Ler](#tab/read)

Os exemplos do Docker a seguir são para o contêiner de leitura.

### <a name="basic-example"></a>Exemplo básico

  ```
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log 

  ```
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

#### <a name="recognize-texttabrecognize-text"></a>[Reconhecimento de Texto](#tab/recognize-text)

Os exemplos do Docker a seguir são para o contêiner de Reconhecimento de Texto.

### <a name="basic-example"></a>Exemplo básico

  ```
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log

  ```
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

***

## <a name="next-steps"></a>Próximas etapas

* Examine [como instalar e executar contêineres](computer-vision-how-to-install-containers.md).
