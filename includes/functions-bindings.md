---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 87c438403934df34a02678c805d3622956654b4e
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172043"
---
Esta tabela mostra as associações com suporte nas duas versões principais do tempo de execução de Azure Functions:

| type | 1.x | 2.x<sup>1</sup> | Disparador | Entrada | Saída |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Armazenamento de Blobs](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Grade de Eventos](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Hubs de Evento](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [WebHooks de & de HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Hub IoT](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Tabelas do Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>Arquivos do OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Email do Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Eventos<br/>de Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Tokens de autenticação](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Aplicativos Móveis](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Hubs de Notificação](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Armazenamento de filas](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Barramento de Serviço](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Armazenamento de tabelas](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> Em 2.x, todas as associações, exceto HTTP e Timer, precisam ser registradas. Confira [Registrar as extensões de associação](../articles/azure-functions/functions-bindings-register.md).
