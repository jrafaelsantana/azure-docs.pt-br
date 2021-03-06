---
title: Habilitar a área de trabalho remota para Linux no Azure Lab Services | Microsoft Docs
description: Saiba como habilitar a área de trabalho remota para máquinas virtuais Linux em um laboratório no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: c67ca111bf87c9dbfa69c93149d29dbd32767fbd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350758"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Habilitar a área de trabalho remota para máquinas virtuais Linux em um laboratório no Azure Lab Services
Este artigo mostra como realizar as seguintes tarefas:

- Habilitar área de trabalho remota para VM Linux
- Como o professor pode se conectar à VM do modelo via Conexão de Área de Trabalho Remota (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Habilitar área de trabalho remota para VM Linux
Durante a criação do laboratório, os professores podem habilitar a **conexão de área de trabalho remota** para imagens do **Linux** . A opção **habilitar conexão de área de trabalho remota** é mostrada quando uma imagem do Linux é selecionada para o modelo. Quando essa opção está habilitada, os professores podem se conectar à VM de modelo e VMs de estudante via RDP (Área de Trabalho Remota). 

![Habilitar a conexão de área de trabalho remota para uma imagem do Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Na caixa de mensagem **habilitando conexão de área de trabalho remota** , selecione **continuar com área de trabalho remota**. 

![Habilitar a conexão de área de trabalho remota para uma imagem do Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Habilitar a **conexão de área de trabalho remota** abre apenas a porta **RDP** em computadores Linux. Se o RDP já estiver instalado e configurado na imagem da máquina virtual (por exemplo: Ubuntu Máquina Virtual de Ciência de Dados Image), você/alunos podem se conectar a VMs via RDP sem seguir nenhuma etapa adicional.
> 
> Se a imagem da VM não tiver o RDP instalado e configurado, você precisará se conectar ao computador Linux usando o SSH pela primeira vez e instalar os pacotes RDP e GUI para que você/alunos possam se conectar ao computador Linux usando o RDP mais tarde. Para obter mais informações, consulte [instalar e configurar o área de trabalho remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em seguida, você publica a imagem para que os alunos possam RDP nas VMs do Linux do aluno. 

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
Atualmente, a conexão de área de trabalho remota tem suporte para os seguintes sistemas operacionais:

- openSUSE Leap 42.3
- Baseado em CentOS 7,5
- "Stretch" do Debian 9
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Professores conectando-se à VM de modelo usando o RDP
Os professores devem se conectar à VM de modelo usando o SSH primeiro e instalar os pacotes RDP e GUI nela. Em seguida, os professores podem usar as seguintes etapas para se conectar às VMs do Linux usando o RDP: 

Você verá a opção **área de trabalho remota** para se conectar à VM do modelo no momento da criação do laboratório. 

![Conectar-se ao modelo via RDP no momento da criação](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Você verá a opção **área de trabalho remota** no Home Page do laboratório depois que o laboratório for criado e a VM do modelo for iniciada. Inicie a VM do modelo se ela ainda não tiver começado. 

![Conectar-se ao modelo via RDP após a criação do laboratório](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Para obter mais informações sobre como se conectar à VM usando SSH ou RDP, consulte [conectar-se usando SSH ou RDP] ((#connect-using-SSH-ou-RDP). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Professores conectando-se a uma VM de aluno usando o RDP
Um professor/professor pode se conectar a uma VM de estudante alternando para a exibição de **máquinas virtuais** e selecionando o ícone **conectar** . Antes disso, os professores devem **publicar** a imagem de modelo com pacotes RDP e GUI instalados nele. 

![Professores conectando-se à VM do aluno](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Para obter mais informações sobre como se conectar à VM usando SSH ou RDP, consulte [conectar-se usando SSH ou RDP] ((#connect-using-SSH-ou-RDP). 

## <a name="connect-using-ssh-or-rdp"></a>Conectar-se usando SSH ou RDP
Se você selecionar a opção **SSH** , você verá a seguinte caixa de diálogo **conectar-se à sua máquina virtual** :  

![Cadeia de conexão SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selecione o botão **copiar** ao lado da caixa de texto para copiá-la para a área de transferência. Salve a cadeia de conexão SSH. Use essa cadeia de conexão de um terminal SSH (como [Putty](https://www.putty.org/)) para conectar a máquina virtual.

Se você selecionar a opção **RDP** , um arquivo RDP será baixado em seu computador. Salve-o e abra-o para se conectar ao computador. 

## <a name="next-steps"></a>Próximas etapas
Depois que um instrutor habilitou o recurso de conexão de área de trabalho remota, os alunos podem se conectar às suas VMs por meio de RDP/SSH. Para obter mais informações, consulte [usar a área de trabalho remota para VMs do Linux em um laboratório de sala de aula](how-to-use-remote-desktop-linux-student.md). 