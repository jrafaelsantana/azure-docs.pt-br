---
title: 'Início Rápido: baixar um relatório de auditoria usando o portal do Azure | Microsoft Docs'
description: Saiba como baixar um relatório de auditoria usando o portal do Azure
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f3e5dd1c42537ce6ff419d7d81d69d824242ec4
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989688"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Início Rápido: Baixar um relatório de auditoria usando o portal do Azure

Neste início rápido, você aprenderá a baixar um arquivo CSV dos logs de auditoria do seu locatário nas últimas 24 horas. É possível baixar até 250.000 registros do portal do Azure. Os registros são classificados por mais recentes; por padrão, você obterá os 250.000 registros mais recentes. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de:

* Um locatário do Azure Active Directory. 
* Um usuário, que está na função de **Administrador de Segurança**, **Leitor de Segurança** ou **Administrador Global** para o locatário. Além disso, qualquer usuário no locatário pode acessar seus próprios logs de auditoria.

## <a name="quickstart-download-an-audit-report"></a>Início Rápido: Baixar um relatório de auditoria

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** do painel de navegação à esquerda e use o botão **Mudar diretório** para selecionar seu Active Directory.
3. No painel, selecione **Azure Active Directory** e, em seguida, selecione **Logs de auditoria**. 
4. Escolha **Últimas 24 horas** na lista suspensa do filtro **Intervalo de datas** e selecione **Aplicar** para exibir os logs de auditoria nas últimas 24 horas. 
5. Selecione o botão **Baixar**, selecione **CSV** como o formato de arquivo e especifique um nome de arquivo para baixar um arquivo CSV contendo os registros filtrados. 

![Relatórios](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Próximas etapas

* [Relatórios de atividades de entrada no portal do Azure Active Directory](concept-sign-ins.md)
* [Retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md)
* [Latências de relatórios do Azure Active Directory](reference-reports-latencies.md)
