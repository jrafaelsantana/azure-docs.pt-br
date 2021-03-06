---
title: Configurar contêineres-Análise de Texto
titleSuffix: Azure Cognitive Services
description: A Análise de Texto fornece uma estrutura de configuração comum a cada contêiner para que você possa facilmente definir e gerenciar configurações de armazenamento, registro em log, telemetria e segurança de seus contêineres.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: f1c42002343de1dd3b3ef6b9c9e35f458db925f4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051156"
---
# <a name="configure-text-analytics-docker-containers"></a>Configurar contêineres de docker de Análise de Texto

A Análise de Texto fornece uma estrutura de configuração comum a cada contêiner para que você possa facilmente definir e gerenciar configurações de armazenamento, registro em log, telemetria e segurança de seus contêineres.

## <a name="configuration-settings"></a>Parâmetros de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. É necessário especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Análise de Texto_ especificado para a configuração [`Billing`](#billing-configuration-setting).

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Análise de texto** o gerenciamento de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração `Billing` especifica o URI do ponto de extremidade da _Análise de Texto_ no Azure usado para medir as informações de cobrança do contêiner. É necessário especificar um valor para essa definição de configuração e o valor deve ser um URI do ponto de extremidade válido para um recurso __Análise de Texto_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Análise de texto** Visão geral, rotulada`Endpoint`

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | O URI do ponto de extremidade de cobrança necessário |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de registro em log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres de Análise de Texto não usam montagens de entrada ou saída para armazenar dados de treinamento ou serviço. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, o local de montagem do [computador host](how-tos/text-analytics-how-to-install-containers.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões do local de montagem do host. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia | Contêineres de Análise de Texto não usam.|
|Opcional| `Output` | Cadeia | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](how-tos/text-analytics-how-to-install-containers.md#stop-the-container).

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de `Text Analytics` extremidade do recurso disponível na `Text Analytics` página chaves do Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está `Text Analytics` disponível na página Visão geral do Azure.| Consulte [coletando parâmetros necessários](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](how-tos/text-analytics-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página `Text Analytics` de chaves de recurso do Azure. 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Próximas etapas

* Reveja [Como instalar e executar contêineres](how-tos/text-analytics-how-to-install-containers.md)
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
