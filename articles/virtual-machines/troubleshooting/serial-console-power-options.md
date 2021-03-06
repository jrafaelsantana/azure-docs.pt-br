---
title: Opções de energia do console serial do Azure | Microsoft Docs
description: Opções de energia de VM disponíveis no console serial do Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: f17b96ad880742cf1232b074e4398f3b1d15e5ba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129569"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Opções de energia disponíveis no console serial do Azure

O console serial do Azure fornece várias ferramentas poderosas para o gerenciamento de energia em sua VM ou conjunto de dimensionamento de máquinas virtuais. Essas opções de gerenciamento de energia podem ser confusas para alguns, portanto, este documento é uma visão geral de cada ferramenta e seu caso de uso pretendido.

Recurso de console serial | Descrição | Caso de uso
:----------------------|:------------|:---------
Reiniciar VM | Uma reinicialização normal da sua VM ou da instância do conjunto de dimensionamento de máquinas virtuais. Essa operação é a mesma que chamar o recurso de reinicialização disponível na página Visão geral. | Na maioria dos casos, essa opção deve ser sua primeira ferramenta para tentar reiniciar a VM. A conexão do console serial apresentará uma breve interrupção e será retomada automaticamente assim que a VM for reiniciada.
Redefinir VM | Um ciclo de energia forçado de sua VM ou conjunto de dimensionamento de máquinas virtuais pela plataforma Azure. | Essa opção é usada para reiniciar imediatamente o sistema operacional, independentemente do seu estado atual. Como essa operação não é normal, há um risco de perda ou corrupção de dados. Não há nenhuma interrupção na conexão do console serial, que pode ser útil para enviar comandos no início do tempo de inicialização (por exemplo, ao obter o GRUB em uma VM do Linux ou no modo de segurança em uma VM do Windows).
SysRq-reinicialização (b) | Uma solicitação do sistema para forçar uma reinicialização do convidado. | Esse recurso só é aplicável a sistemas operacionais Linux e requer [que o SysRq esteja habilitado](./serial-console-nmi-sysrq.md#system-request-sysrq) no sistema operacional. Se o sistema operacional estiver configurado corretamente para SysRq, esse comando fará com que o sistema operacional seja reiniciado.
NMI (interrupção não mascarada) | Um comando de interrupção, que será entregue ao sistema operacional | Esta operação está disponível para VMs do [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) e [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) e requer que o NMI esteja habilitado. O envio de uma NMI normalmente fará com que o sistema operacional falhe. Você pode configurar seu sistema operacional para criar um arquivo de despejo e reiniciar após o recebimento do NMI, o que pode ser útil na depuração de baixo nível.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [console serial do Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre o [console serial do Azure para VMs do Windows](./serial-console-windows.md)