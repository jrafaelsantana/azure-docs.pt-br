---
title: Serviço de Aplicativo do Azure - configuração de rede de sincronização | Microsoft Docs
description: Este artigo descreve como sincronizar sua configuração de rede a o plano de hospedagem do Serviço de Aplicativo do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 920851e33255b0bcb5b085c541ae636c5988be72
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567321"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Sincronizar a configuração de rede ao plano de hospedagem do Serviço de Aplicativo do Azure

Pode acontecer que, embora você tenha [integrado seu aplicativo à uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md), você não consiga estabelecer conexão à Instância Gerenciada. Algo que você pode é tentar atualizar a configuração de rede para o plano de serviço.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Sincronizar a configuração de rede ao plano de hospedagem de Serviço de Aplicativo do Azure

Para fazer isso, siga estas etapas:  

1. Vá até o seu plano do Serviço de Aplicativo dos aplicativos web.

   ![plano do serviço de aplicativo](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Clique em **Rede** e, em seguida, clique em **Clique aqui para gerenciar**.

   ![gerenciar plano de serviço](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selecione seu **VNet** e clique em **Sincronizar rede**.

   ![sincronizar rede](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Aguarde até que a sincronização seja feita.
  
   ![sincronização concluída](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Agora você está pronto para tentar restabelecer a conexão à Instância Gerenciada.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como configurar sua VNet para Instância Gerenciada, confira [Arquitetura de VNet da Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md) e [Como configurar a VNet existente](sql-database-managed-instance-configure-vnet-subnet.md).
