---
title: Matriz de suporte para migrações para Azure
description: Fornece um resumo das configurações de suporte e limitações para o serviço migrações para Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: raynew
ms.openlocfilehash: 3fb36eb9b8507903d1aee00b82c375685a4dfa6c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279465"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte para migrações para Azure

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte geral e as limitações para cenários e implantações de migração do Azure.


## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Há duas versões do serviço de Migrações para Azure:

- **Versão atual**: Usando essa versão, você pode criar novos projetos de migrações para Azure, descobrir avaliações locais e orquestrações e migrações. [Saiba mais](whats-new.md#azure-migrate-new-version).
- **Versão anterior**: para o cliente que usa a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora você deve usar a versão atual. Na versão anterior, você não pode criar novos projetos de migrações para Azure ou executar novas descobertas.

## <a name="supported-migration-scenarios"></a>Cenários de migração com suporte

A tabela resume os cenários de migração com suporte.

**Implantação** | **Detalhes** 
--- | --- 
**Avaliação local** | Avalie as cargas de trabalho e os dados locais em execução em VMs VMware e VMs do Hyper-V. Avalie usando o DMA (avaliação de servidor e Assistente de Migração de Dados da Microsoft) de migrações do Azure, bem como ferramentas de terceiros que incluem Cloudamize, colocação Tech e O turbonomic Server.
**Migração local para o Azure** | Migre cargas de trabalho e dados em execução em servidores físicos, VMs VMware, VMs Hyper-V, servidores físicos e VMS baseadas em nuvem para o Azure. Migre usando a avaliação de servidor de migrações para Azure e o serviço de migração de banco de dados do Azure (DMS), bem como o uso de ferramentas de terceiros que incluem Carbonite e CorentTech.

O suporte a ferramentas específicas é resumido da seguinte maneira.

**Ferramenta** | **Avaliação/migração** | **Detalhes**
--- | --- | ---
Avaliação de Servidor das Migrações para Azure | Avaliação | Experimente a avaliação do servidor para [Hyper-V](tutorial-prepare-hyper-v.md) e [VMware](tutorial-prepare-vmware.md).
Cloudamize | Avaliação | [Saiba mais](https://www.cloudamize.com/platform#tab-0).
CorentTech | Avaliação | [Saiba mais](https://www.corenttech.com/).
Turbonomic | Avaliação | [Saiba mais](https://turbonomic.com/solutions/technologies/azure-cloud/).
Migração de Servidor das Migrações para Azure | Migração | Experimente a migração de servidor para o [Hyper-V](tutorial-migrate-hyper-v.md) e o [VMware](tutorial-migrate-vmware.md).
Carbonite | Migração | [Saiba mais](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migração | [Saiba mais](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Projetos de migrações para Azure

**Suporte** | **Detalhes**
--- | ---
Assinatura | Você pode ter vários projetos de migrações para Azure em uma assinatura.
Permissões do Azure | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.
VMs VMware  | Avalie até 35.000 VMs VMware em um único projeto.
VMs Hyper-V | Avalie até 35.000 VMs do Hyper-V em um único projeto.

Um projeto pode incluir VMs do VMware e VMs do Hyper-V, até os limites de avaliação.


## <a name="vmware-assessment-and-migration"></a>Avaliação e migração do VMware

[Reveja](migrate-support-matrix-vmware.md) a matriz de suporte de migração de servidor e avaliação do Azure Migrations para VMs VMware.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Revise](migrate-support-matrix-hyper-v.md) a matriz de suporte de migração de servidor e avaliação do Azure migrações para VMs do Hyper-V.


## <a name="next-steps"></a>Próximas etapas

- [Avalie as VMs do VMware](tutorial-assess-vmware.md) para migração.
- [Avalie as VMs do Hyper-V](tutorial-assess-hyper-v.md) para migração.

