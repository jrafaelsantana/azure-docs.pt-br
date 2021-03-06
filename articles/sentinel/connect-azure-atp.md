---
title: Conectar dados do Azure ATP ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Azure ATP ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 764fb4c22bcce5fc5b045e68dc512243e783020e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261837"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Conectar dados da ATP (proteção avançada contra ameaças) do Azure

> [!IMPORTANT]
> O conector de dados da proteção avançada contra ameaças do Azure no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir logs da [proteção avançada contra ameaças do Azure](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) para o Azure Sentinel com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador global ou administrador de segurança
- Você deve ser um cliente de visualização do Azure ATP

## <a name="connect-to-azure-atp"></a>Conectar-se ao Azure ATP

Verifique se a versão de visualização do Azure ATP está [habilitada em sua rede](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se o Azure ATP for implantado e ingerir seus dados, os alertas suspeitos poderão ser facilmente transmitidos para o Azure Sentinel. Pode levar até 24 horas para que os alertas comecem a transmitir para o Azure Sentinel.


1. Para conectar o Azure ATP ao Azure Sentinel, você deve primeiro habilitar a integração entre o Azure ATP e o Microsoft Cloud App Security. Para obter informações sobre como fazer isso, consulte [integração da proteção avançada contra ameaças do Azure](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **proteção avançada contra ameaças do Azure (versão prévia)** .

1. Você pode selecionar se deseja que os alertas do Azure ATP gerem automaticamente incidentes no Azure Sentinel automaticamente. Em **criar incidentes** , selecione **habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Você pode editar essa regra em **análise** e em **regras ativas**.

1. Clique em **Conectar**.

1. Para usar o esquema relevante no Log Analytics para os alertas do Azure ATP, procure **SecurityAlert**.

> [!NOTE]
> Se os alertas forem maiores que 30 KB, o Azure Sentinel interromperá a exibição do campo entidades nos alertas.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar a proteção avançada contra ameaças do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

