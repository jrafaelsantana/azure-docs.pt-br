---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2498711a5b7e5bce29cd0054ba40257f8f996d43
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266816"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Habilitar registro em log com as configurações de diagnóstico

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

2. Selecionar **configurações de Diagnóstico**.

3. Selecione **Ativar diagnóstico**.

   ![Ligar diagnósticos](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nomeie as configurações de diagnóstico.

5. Escolha para onde você deseja enviar os logs. Você pode selecionar qualquer combinação das três opções abaixo:

   * Arquivar em uma conta de armazenamento
   * Transmitir para um hub de eventos
   * Enviar para o Log Analytics

6. Escolha as operações que você deseja monitorar e habilite os logs para essas operações. As operações que podem ser informadas em configurações de diagnóstico são:

   * Conexões
   * Telemetria de dispositivo
   * Mensagens da nuvem para o dispositivo
   * Operações de identidade do dispositivo
   * Carregamentos de arquivos
   * Roteamento de mensagens
   * Operações de dispositivo gêmeo para nuvem
   * Operações de nuvem gêmea para dispositivo
   * Operações de gêmeos
   * Operações de trabalho
   * Métodos diretos  
   * Rastreamento distribuído (versão prévia)
   * Configurações
   * Fluxos de dispositivo
   * Métricas do dispositivo

6. Salve as novas configurações. 

Se você deseja ativar as configurações de diagnóstico com o PowerShell, use o seguinte código:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecerão no destino de arquivamento configurado, na folha **Configurações de diagnóstico**. Para obter mais informações sobre como configurar o diagnóstico, confira [Coletar e consumir dados de log com os recursos do Azure](../articles/azure-monitor/platform/resource-logs-overview.md).
