---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0185710b6255dabd61d6361ed98c658f1e8075c7
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975274"
---
Há alguns limites no número de métricas e eventos por aplicativo, ou seja, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

| Resource | Limite padrão | Observação
| --- | --- | --- |
| Total de dados por dia | 100 GB | Você pode reduzir os dados ao definir um limite. Caso precise de mais dados, é possível aumentar o limite até 1.000 GB. Para capacidades maiores que 1.000 GB, envie um email para AIDataCap@microsoft.com.
| Limitação | 32.000 eventos/segundo | O limite é medido em um minuto.
| Retenção de dados | 90 dias | Este recurso destina-se a [Pesquisa](../articles/azure-monitor/app/diagnostic-search.md), [Análise](../articles/azure-monitor/app/analytics.md) e [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| Retenção de resultados detalhados do [Teste de disponibilidade de várias etapas](../articles/azure-monitor/app/availability-multistep.md) | 90 dias | Esse recurso fornece resultados detalhados de cada etapa.
| Tamanho máximo de evento | 64.000 |
| Tamanho dos nomes de propriedade e métrica | 150 | Veja [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Tamanho da cadeia de caracteres do valor da propriedade | 8\.192 | Veja [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Comprimento da mensagem de rastreamento e de exceção | 32.768  | Veja [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Contagem de [testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) por aplicativo | 100 |
| Retenção de dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) | 5 dias |
| Dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) enviados por dia | 10 GB |

Para obter mais informações, consulte [sobre preços e cotas no Application Insights](../articles/azure-monitor/app/pricing.md).
