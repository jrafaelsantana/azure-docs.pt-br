---
title: 'Armazenamento Premium do Azure: Design para desempenho em VMs do Windows | Microsoft Docs'
description: Crie aplicativos de alto desempenho usando o Armazenamento Premium do Azure. O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7fbeb12f5b221685aad5cac5352318db10bd69a7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036262"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho de disco é, na verdade, um gargalo de rede. Nessas situações, você deve otimizar seu [desempenho de rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se você pretende avaliar o benchmark de seu disco, consulte nosso artigo sobre o parâmetro de [comparação de um disco](disks-benchmarks.md).
>
> Se sua VM oferecer suporte a rede acelerada, verifique se ela está ativada. Se não estiver ativado, você poderá ativá-lo em VMs já implementadas nos [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) e [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se você for novo no armazenamento Premium, leia primeiro o [tipo de disco do Azure para VMs de IaaS](disks-types.md) e [metas de desempenho e escalabilidade do armazenamento do Azure para contas de armazenamento](../../storage/common/storage-scalability-targets.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se você pretende avaliar o benchmark de seu disco, consulte nosso artigo sobre o parâmetro de [comparação de um disco](disks-benchmarks.md).

Saiba mais sobre os tipos de disco disponíveis: [Selecione um tipo de disco](disks-types.md)  

Para usuários do SQL Server, leia os artigos sobre Práticas recomendadas de desempenho para o SQL Server:

* [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](sql/virtual-machines-windows-sql-performance.md)
* [Armazenamento Premium do Azure fornece desempenho mais alto para SQL Server na VM do Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)