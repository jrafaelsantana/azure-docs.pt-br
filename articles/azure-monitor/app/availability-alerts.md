---
title: Configurar alertas de disponibilidade com o Aplicativo Azure insights | Microsoft Docs
description: Configure testes da web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 1d7527d6f52235c6b95ad2e336ea9f9ba85d6344
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114389"
---
# <a name="availability-alerts"></a>Alertas de disponibilidade

[Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md) envia solicitações da Web ao aplicativo em intervalos regulares de pontos no mundo todo. Ele poderá alertá-lo se seu aplicativo não estiver respondendo ou se responder muito devagar.

## <a name="enable-alerts"></a>Habilitar alertas

Os alertas agora são automaticamente habilitados por padrão, mas para configurar completamente o alerta, primeiro você precisa criar inicialmente seu teste de disponibilidade.

![Criar experiência](./media/availability-alerts/create-test.png)

> [!NOTE]
>  com os [novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), as preferências de notificação e a gravidade de regra de alerta com [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **deve ser** configurada no experiência de alertas. Sem as etapas a seguir, você só receberá notificações no portal.

1. Depois de salvar o teste de disponibilidade, na guia Detalhes, clique nas reticências do teste que acabou de criar. Clique em "Editar alerta".

   ![Editar após salvar](./media/availability-alerts/edit-alert.png)

2. Defina o nível de severidade desejados, a descrição da regra e mais importante, o grupo de ação que tem as preferências de notificação que você deseja usar para essa regra de alerta.

   ![Editar após salvar](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alertar sobre X fora de locais de Y relatando falhas

O X fora de locais de Y regra de alerta está habilitada por padrão na [experiência de novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), quando você cria um novo teste de disponibilidade. Você pode recusá-la selecionando a opção "clássica" ou optar por desabilitar a regra de alerta.

> [!NOTE]
> Configure os grupos de ação para receber notificações quando o alerta for disparado, seguindo as etapas acima. Sem essa etapa, você só receberá notificações no portal quando a regra dispara.
>

### <a name="alert-on-availability-metrics"></a>Alertar sobre métricas de disponibilidade

Usando os [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), você pode alertar sobre a disponibilidade de agregação segmentada e métricas de duração de teste:

1. Selecione um recurso do Application Insights na experiência de métricas e selecione uma métrica de disponibilidade:

    ![Seleção de métricas de disponibilidade](./media/availability-alerts/select-metric.png)

2. Configure alertas de opção do menu levara você para a nova experiência de onde você pode selecionar testes específicos ou locais para configurar a regra de alerta no. Você também pode configurar os grupos de ação para esta regra de alerta aqui.

### <a name="alert-on-custom-analytics-queries"></a>Alertar sobre consultas de análises personalizadas

Usando [novos alertas unificados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), você pode alertar sobre [consultas de log personalizado](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Com consultas personalizadas, você pode alertar sobre qualquer critério arbitrário que ajuda você a obter o sinal de mais confiável dos problemas de disponibilidade. Isso também é aplicável se você estiver enviando resultados de disponibilidade personalizados usando o SDK do API trackavailability.

> [!Tip]
> As métricas sobre dados de disponibilidade incluem resultados disponibilidade personalizado que você pode estar enviando chamando nosso SDK TrackAvailability. Você pode usar os alertas de suporte a métricas para alertar sobre resultados de disponibilidade personalizado.
>

## <a name="automate-alerts"></a>Automatizar alertas

Para automatizar esse processo com modelos de Azure Resource Manager, consulte a documentação [criar um alerta de métrica com o modelo do Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-availability-test-along-with-availability-test-alert) .

## <a name="troubleshooting"></a>Solução de problemas

[Artigo de solução de problemas](troubleshoot-availability.md)dedicado.

## <a name="next-steps"></a>Próximas etapas

* [Testes na Web de várias etapas](availability-multistep.md)
* [Testes da Web de ping de URL](monitor-web-app-availability.md)
