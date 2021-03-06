---
title: Monitoramento de métricas e logs no serviço de porta frontal do Azure | Microsoft Docs
description: Este artigo descreve as diferentes métricas e logs de acesso aos quais o serviço de porta frontal do Azure dá suporte
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 229706ff91b776363d3e9de080e02cee5edf9c77
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677905"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitoramento de métricas e logs no serviço de porta frontal do Azure

Usando o serviço de porta frontal do Azure, você pode monitorar os recursos das seguintes maneiras:

- **Métricas**. Atualmente, a porta frontal do Azure tem sete métricas para exibir os contadores de desempenho.
- **Logs**. Os logs de atividade e diagnóstico permitem que o desempenho, o acesso e outros dados sejam salvos ou consumidos de um recurso para fins de monitoramento.

### <a name="metrics"></a>metrics

As métricas são um recurso para determinados recursos do Azure que permitem Exibir contadores de desempenho no Portal. A seguir estão as métricas de porta frontal disponíveis:

| Métrica | Nome de exibição da métrica | Unidade | Dimensões | Descrição |
| --- | --- | --- | --- | --- |
| RequestCount | Contagem de solicitações | Contagem | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de solicitações de cliente atendidas pelo Front Door.  |
| RequestSize | Tamanho da solicitação | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como solicitações de clientes para o Front Door. |
| ResponseSize | Tamanho da resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como respostas do Front Door para clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O tempo calculado a partir da solicitação do cliente recebida pela porta frontal até que o cliente confirme o último byte de resposta da porta frontal. |
| BackendRequestCount | Contagem de solicitações de back-end | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de solicitações enviadas do Front Door aos back-ends. |
| BackendRequestLatency | Latência de solicitação de back-end | Milissegundos | Back-end | O tempo calculado de quando a solicitação foi enviada pelo Front Door ao back-end até o Front Door receber o último byte de resposta do back-end. |
| BackendHealthPercentage | Percentual de integridade do back-end | Percent | Back-end</br>BackendPool | O percentual de investigações de integridade bem-sucedidas do Front Door aos back-ends. |
| WebApplicationFirewallRequestCount | Contagem de solicitações do Firewall de Aplicativo Web | Contagem | PolicyName</br>RuleName</br>Ação | O número de solicitações de cliente processadas pela segurança da camada de aplicativo do Front Door. |

## <a name="activity-log"></a>Logs de atividade

Os logs de atividade fornecem informações sobre as operações realizadas no serviço de porta frontal. Eles também determinam o que, quem e quando para qualquer operação de gravação (put, post ou Delete) realizada no serviço de porta frontal.

>[!NOTE]
>Os logs de atividade não incluem operações de leitura (Get). Eles também não incluem operações que você executa usando o portal do Azure ou a API de gerenciamento original.

Acesse logs de atividade em seu serviço de porta frontal ou em todos os logs dos recursos do Azure no Azure Monitor. Para exibir logs de atividade:

1. Selecione sua instância de porta frontal.
2. Selecione **log de atividades**.

    ![Log de atividades](./media/front-door-diagnostics/activity-log.png)

3. Escolha um escopo de filtragem e, em seguida, selecione **aplicar**.

## <a name="diagnostic-logging"></a>Logs de diagnóstico
Os logs de diagnóstico fornecem informações avançadas sobre operações e erros que são importantes para auditoria e solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades.

Os logs de atividades fornecem informações sobre as operações realizadas nos recursos do Azure. Os logs de diagnóstico fornecem informações sobre as operações executadas pelo recurso. Para obter mais informações, consulte [Azure monitor logs de diagnóstico](../azure-monitor/platform/resource-logs-overview.md).

![Logs de diagnóstico](./media/front-door-diagnostics/diagnostic-log.png)

Para configurar os logs de diagnóstico para o serviço de porta frontal:

1. Selecione o serviço de porta frontal do Azure.

2. Escolha **configurações de diagnóstico**.

3. Selecione **Ativar diagnóstico**. Arquive logs de diagnóstico junto com as métricas para uma conta de armazenamento, transmita-os para um hub de eventos ou envie-os para Azure Monitor logs.

O serviço de porta frontal atualmente fornece logs de diagnóstico (em lote). Os logs de diagnóstico fornecem solicitações de API individuais com cada entrada com o seguinte esquema:

| Propriedade  | Descrição |
| ------------- | ------------- |
| ClientIp | Endereço IP do cliente que fez a solicitação. |
| clientPort | A porta IP do cliente que fez a solicitação. |
| HttpMethod | Método HTTP usado pela solicitação. |
| HttpStatusCode | O código de status HTTP retornado do proxy. |
| HttpStatusDetails | Status resultante na solicitação. O significado desse valor de cadeia de caracteres pode ser encontrado em uma tabela de referência de status. |
| HttpVersion | Tipo de solicitação ou conexão. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação. |
| RequestUri | URI da solicitação recebida. |
| ResponseBytes | Bytes enviados pelo servidor de back-end como a resposta.  |
| RoutingRuleName | O nome da regra de roteamento com a qual a solicitação foi correspondida. |
| SecurityProtocol | A versão do protocolo TLS/SSL usada pela solicitação ou NULL se não houver criptografia. |
| TimeTaken | O período de tempo que a ação levou, em milissegundos. |
| UserAgent | O tipo de navegador que o cliente usou |
| TrackingReference | A cadeia de caracteres de referência exclusiva que identifica uma solicitação servida pela porta da frente, também enviada como o cabeçalho X-Azure-ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |

## <a name="next-steps"></a>Próximas etapas

- [Criar um perfil de porta de front-end](quickstart-create-front-door.md)
- [Como funciona a porta frontal](front-door-routing-architecture.md)
