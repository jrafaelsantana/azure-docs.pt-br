---
title: Hub IoT do Azure e a Grade de Eventos | Microsoft Docs
description: Use a Grade de Eventos do Azure para acionar processos com base nas ações que ocorrem no Hub IoT.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: f411771fbf39a99642506253fc025d6b29840423
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648634"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagir aos eventos do Hub IoT usando a Grade de Eventos para disparar ações

O Hub IoT do Azure integra-se com a Grade de Eventos do Azure para que você possa enviar notificações de eventos para outros serviços e acionar processos de downstream. Configure seus aplicativos de negócios para escutar eventos do Hub IoT para que você possa reagir a eventos críticos de maneira segura, escalonável e confiável. Por exemplo, crie um aplicativo que atualize um banco de dados, crie um tíquete de trabalho e entregue uma notificação por email toda vez que um novo dispositivo IoT for registrado no Hub IoT.

[Grade de Eventos do Azure](../event-grid/overview.md) é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. Grade de eventos tem suporte interno para os serviços do Azure como [Azure Functions](../azure-functions/functions-overview.md) e [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md)e pode fornecer alertas de eventos para os serviços do Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que dá suporte a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md).

![Arquitetura de Grade de Eventos do Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração da Grade de Eventos está disponível para os hubs IoT localizados nas regiões onde há suporte para a Grade de Eventos. Todos os eventos de dispositivo, exceto os eventos de telemetria do dispositivo, estão geralmente disponíveis. O evento de telemetria do dispositivo está em visualização pública e está disponível em todas as regiões, exceto leste dos EUA, oeste dos EUA, Europa Ocidental, [Azure governamental](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china)e [Azure Alemanha](https://azure.microsoft.com/global-infrastructure/germany/). Para obter a lista mais recente de regiões, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md).

## <a name="event-types"></a>Tipos de eventos

Hub IoT publica os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registrado para um Hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é excluído de um Hub IoT. |
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo é conectado a um Hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desconectado de um Hub IoT. |
| Microsoft.Devices.DeviceTelemetry | Publicado quando uma mensagem de telemetria do dispositivo é enviada a um hub IoT |

Use o portal do Azure ou a CLI do Azure para configurar os eventos para publicar a partir de cada Hub IoT. Para um exemplo, experimente o tutorial [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Esquema do evento

Eventos do Hub IoT contêm todas as informações que você precisa para responder às alterações no ciclo de vida do seu dispositivo. Você pode identificar um evento de Hub IoT, verificando se a propriedade eventType começa com **Microsoft.Devices**. Para obter mais informações sobre como usar propriedades de evento de Grade de Eventos, consulte o [esquema de evento da Grade de Eventos](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Esquema de dispositivo conectado

O exemplo a seguir mostra o esquema de um evento de dispositivo conectado:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Esquema de telemetria do dispositivo

A mensagem de telemetria do dispositivo deve estar em um formato JSON válido com o contentType definido como **Application/JSON** e contentEncoding definido como **UTF-8** nas [Propriedades do sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens. Essas duas propriedades não diferenciam maiúsculas de minúsculas. Se a codificação de conteúdo não estiver definida, o Hub IoT gravará as mensagens no formato codificado 64 base.

Você pode enriquecer os eventos de telemetria do dispositivo antes que eles sejam publicados na grade de eventos selecionando o ponto de extremidade como grade de eventos. Para obter mais informações, consulte [visão geral](iot-hub-message-enrichments-overview.md)de aprimoramentos de mensagem.

O exemplo a seguir mostra o esquema de um evento de telemetria do dispositivo:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Esquema criado de dispositivo

O exemplo a seguir mostra o esquema de um evento criado de dispositivo:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Para obter uma descrição detalhada de cada propriedade, consulte [esquema de evento da grade de eventos do Azure para o Hub IOT](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrar eventos

As assinaturas de evento do Hub IoT podem filtrar eventos com base no tipo de evento, conteúdo de dados e assunto, que é o nome do dispositivo.

A grade de eventos permite [Filtrar](../event-grid/event-filtering.md) tipos de eventos, assuntos e conteúdo de dados. Ao criar a assinatura da grade de eventos, você pode optar por assinar os eventos de IoT selecionados. Filtros de assunto no trabalho da Grade de Eventos com base em correspondências de **Começa com** (prefixo) e **Termina com** (sufixo). O filtro usa um operador `AND`, para que eventos com o assunto que correspondam ao prefixo e ao sufixo sejam entregues ao assinante.

O assunto de Eventos IoT usa o formato:

```json
devices/{deviceId}
```

A grade de eventos também permite a filtragem de atributos de cada evento, incluindo o conteúdo de dados. Isso permite que você escolha quais eventos são entregues com base no conteúdo da mensagem de telemetria. Consulte [filtragem avançada](../event-grid/event-filtering.md#advanced-filtering) para exibir exemplos. Para filtrar o corpo da mensagem de telemetria, você deve definir o contentType como **Application/JSON** e ContentEncoding como **UTF-8** nas [Propriedades do sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens. Essas duas propriedades não diferenciam maiúsculas de minúsculas.

Para eventos que não são de telemetria como DeviceConnected, DeviceDisconnected, DeviceCreated e DeviceDeleted, a filtragem da grade de eventos pode ser usada ao criar a assinatura. Para eventos de telemetria, além da filtragem na grade de eventos, os usuários também podem filtrar no dispositivo gêmeos, propriedades de mensagem e corpo por meio da consulta de roteamento de mensagens. Criamos uma [rota](iot-hub-devguide-messages-d2c.md) padrão no Hub IOT, com base em sua assinatura da grade de eventos para a telemetria do dispositivo. Essa rota única pode lidar com todas as suas assinaturas de grade de eventos. Para filtrar mensagens antes que os dados de telemetria sejam enviados, você pode atualizar sua [consulta de roteamento](iot-hub-devguide-routing-query-syntax.md). Observe que a consulta de roteamento pode ser aplicada ao corpo da mensagem somente se o corpo for JSON. Você também deve definir o contentType como **Application/JSON** e ContentEncoding como **UTF-8** nas propriedades do [sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)de mensagens.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitações para eventos de conexão e desconexão de dispositivo

Para receber eventos de conexão e desconexão de dispositivo, você precisa abrir o link D2C ou o link C2D para o dispositivo. Se o dispositivo estiver usando o protocolo MQTT, o Hub IoT manterá o link C2D aberto. Para AMQP, você pode abrir o link C2D chamando a [API assíncrona Receive](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

Se você está enviando telemetria, o link D2C está aberto. Se a conexão do dispositivo estiver piscando, o que significa que o dispositivo se conecta e desconecta com frequência, não enviaremos todos os Estados de conexão única, mas publicaremos o estado de conexão do qual um instantâneo é levado a cada minuto. Em caso de uma interrupção do Hub IoT, publicaremos o estado de conexão do dispositivo assim que a interrupção acabar. Se o dispositivo for desconectado durante essa interrupção, o evento de dispositivo desconectado será publicado em até dez minutos.

## <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Aplicativos que tratam os eventos de Hub IoT devem seguir essas práticas sugeridas:

* Várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, portanto, não presuma que os eventos sejam de uma fonte específica. Sempre verifique o tópico de mensagem para certificar-se de que eles vêm do hub IoT que você espera.

* Não presuma que todos os eventos que você recebe são os tipos que você espera. Sempre verifique o eventType antes de processar a mensagem.

* As mensagens podem chegar fora de ordem ou após um atraso. Use o campo ETag para entender se suas informações sobre objetos estão atualizadas para dispositivos criados pelo dispositivo ou eventos excluídos do dispositivo.

## <a name="next-steps"></a>Próximas etapas

* [Tente o tutorial de eventos do Hub IoT](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Saiba como ordenar os eventos de conexão e desconexão de dispositivo](iot-hub-how-to-order-connection-state-events.md)

* [Saiba mais sobre a Grade de Eventos](../event-grid/overview.md)

* [Comparar as diferenças entre roteamento de eventos e mensagens do Hub IoT](iot-hub-event-grid-routing-comparison.md)

* [Saiba como usar eventos de telemetria de IoT para implementar análises espaciais de IoT usando o Azure Maps (e o roteamento de mensagens do Hub IoT)](../azure-maps/tutorial-iot-hub-maps.md#filter-events-using-iot-hub-message-routing)
