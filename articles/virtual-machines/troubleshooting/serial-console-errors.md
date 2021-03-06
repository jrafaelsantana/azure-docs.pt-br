---
title: Erros do console serial do Azure | Microsoft Docs
description: Erros comuns no console serial do Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e4596ae2f92e5dfd99dc7c83857e0c9874358fd4
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949696"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Erros comuns no console serial do Azure
Há um conjunto de erros conhecidos no console serial do Azure. Esta é uma lista desses erros e etapas de mitigação para eles.

## <a name="common-errors"></a>Erros comuns

Erro                            |   Atenuação
:---------------------------------|:--------------------------------------------|
Não é possível recuperar as configurações de diagnóstico de inicialização para *&lt;VMNAME &gt;* . Para usar o console serial, verifique se o diagnóstico de inicialização está habilitado para essa VM. erro de diagnóstico de @no__t 0Boot @ no__t-1 | Verifique se a VM ou o conjunto de dimensionamento de máquinas virtuais tem o [diagnóstico de inicialização](boot-diagnostics.md) habilitado. Se você estiver usando o console serial em uma instância de conjunto de dimensionamento de máquinas virtuais, verifique se sua instância tem o modelo mais recente.
A VM está em estado desalocado interrompido. Inicie a máquina virtual e tente estabelecer novamente a conexão de console serial. ![Erro desalocado](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | A instância do conjunto de dimensionamento de máquinas virtuais ou VM deve estar em um estado iniciado para acessar o console serial. Inicie a instância da VM ou do conjunto de dimensionamento de máquinas virtuais e tente novamente.
Uma resposta "Proibido" foi encontrada ao acessar a conta de armazenamento do diagnóstico de inicialização desta VM. ![Erro de firewall da conta de armazenamento](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Certifique-se de que o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento do diagnóstico de inicialização acessível é necessária para o console serial funcione. Por design, o console serial não pode funcionar com firewalls de conta de armazenamento habilitados na conta de armazenamento do diagnóstico de inicialização.
Você não tem as permissões necessárias para usar esta VM com o console serial. Certifique-se de ter pelo menos permissões de função de Colaborador da Máquina Virtual.| O acesso ao console serial exige que você tenha acesso ao nível de colaborador ou acima em sua VM ou conjunto de dimensionamento de máquinas virtuais. Para obter mais informações, consulte a [página Visão geral](serial-console-overview.md).
Não foi possível encontrar a conta de armazenamento ' ' usada para o diagnóstico de inicialização nesta VM. Verifique se o diagnóstico de inicialização está habilitado para esta VM, se essa conta de armazenamento não foi excluída e se você tem acesso a essa conta de armazenamento. | Verifique se você não excluiu a conta de armazenamento de diagnóstico de inicialização para sua VM ou conjunto de dimensionamento de máquinas virtuais
O provisionamento para esta VM ainda não foi bem-sucedido. Verifique se a VM está totalmente implantada e repita a conexão do console serial. | Sua VM ou conjunto de dimensionamento de máquinas virtuais ainda pode estar Provisionando. Aguarde algum tempo e tente novamente.
Você não tem as permissões necessárias para gravar na conta de armazenamento de diagnóstico de inicialização para essa VM. Verifique se você tem pelo menos permissões de colaborador de VM em ' '. | Console serial acesso requer acesso no nível de colaborador na conta de armazenamento do diagnóstico de inicialização. Para obter mais informações, consulte a [página Visão geral](serial-console-overview.md).
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnósticos de inicialização *&lt;STORAGEACCOUNTNAME&gt;* . Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | Console serial acesso requer acesso no nível de colaborador na conta de armazenamento do diagnóstico de inicialização. Para obter mais informações, consulte a [página Visão geral](serial-console-overview.md).
O soquete da Web está fechado ou não pôde ser aberto. | Talvez seja necessário adicionar acesso de firewall a `*.console.azure.com`. Uma abordagem mais detalhada, mas mais longa, é permitir o acesso de firewall aos [intervalos de IP do datacenter Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são alterados com bastante frequência.
Console serial não funciona com uma conta de armazenamento usando Azure Data Lake Storage Gen2 com namespaces hierárquicos. | Esse é um problema conhecido com namespaces hierárquicos. Para atenuar, verifique se a conta de armazenamento do diagnóstico de inicialização da VM não foi criada usando Azure Data Lake Storage Gen2. Essa opção só pode ser definida na criação da conta de armazenamento. Talvez seja necessário criar uma conta de armazenamento de diagnóstico de inicialização separada sem Azure Data Lake Storage Gen2 habilitado para atenuar esse problema.


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [console serial do Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre o [console serial do Azure para VMs do Windows](./serial-console-windows.md)