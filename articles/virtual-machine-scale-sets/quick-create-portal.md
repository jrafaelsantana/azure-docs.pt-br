---
title: Início Rápido – Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure | Microsoft Docs
description: Saiba como criar um rapidamente um conjunto de dimensionamento de máquinas virtuais no Portal do Azure
keywords: conjuntos de escala de máquina virtual
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: a2081bab2aebf0d49f3bde2467dac1fa683452ab
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008720"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Início Rápido: Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. É possível dimensionar o número de VMs manualmente no conjunto de dimensionamento ou definir as regras para o dimensionamento automático com base no uso de recursos, como CPU, demanda de memória ou tráfego de rede. Um balanceador de carga do Azure então distribui o tráfego para as instâncias de VM no conjunto de dimensionamento. Neste início rápido, você criará um conjunto de dimensionamento de máquinas virtuais no Portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no Portal do Azure em https://portal.azure.com.


## <a name="create-virtual-machine-scale-set"></a>Criar conjunto de dimensionamento de máquinas virtuais
Você pode implantar um conjunto de dimensionamento com uma imagem do Windows Server ou do Linux como CentOS, RHEL, Ubuntu ou SLES.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Pesquise por *conjunto de dimensionamento*, escolha **Conjunto de dimensionamento de máquinas virtuais** e selecione **Criar**.
3. Insira um nome para o conjunto de dimensionamento, tal como *myScaleSet*.
4. Selecione o tipo de SO desejado, tal como *Datacenter do Windows Server 2016*.
5. Insira o nome do grupo de recursos desejado, tal como *myResourceGroup*, bem como o local, por exemplo, *Leste dos EUA*.
6. Insira o nome de usuário desejado e selecione o tipo de autenticação que prefere.
   - Uma **Senha** deve ter pelo menos 12 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. Para obter mais informações, consulte [requisitos de senha e nome de usuário](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Se você selecionar uma imagem de disco do SO Linux, em vez disso, você poderá escolher **chave pública SSH**. Apenas forneça sua chave pública, tal como *~/.ssh/id_rsa.pub*. Você pode usar o Azure Cloud Shell por meio do portal para [criar e usar as chaves de SSH](../virtual-machines/linux/mac-create-ssh-keys.md).

     ![Detalhes Básicos para criar um conjunto de dimensionamento de máquinas virtuais no portal do Azure](./media/virtual-machine-scale-sets-create-portal/create-scale-set-basic-details.png)
1. Selecione uma opção de balanceamento de carga, como *Balanceador de carga*, em **Escolher opções de Balanceamento de carga**. Insira os detalhes restantes para a opção de balanceamento de carga. Por exemplo, para o *Balanceador de carga*, você precisa inserir um **Nome do endereço IP público** e um **Rótulo do nome de domínio**.
1. Insira os detalhes da rede virtual em **Configurar Redes Virtuais**. Por exemplo, você pode criar uma rede virtual, *myVirtualNetwork*, e uma nova sub-rede, *default*.
1. Para confirmar as opções do conjunto de dimensionamento, selecione **Criar**.
    ![Detalhes de Rede para criar um conjunto de dimensionamento de máquinas virtuais no portal do Azure](./media/virtual-machine-scale-sets-create-portal/create-scale-set-networking-details.png)



## <a name="connect-to-a-vm-in-the-scale-set"></a>Conectar-se a uma VM no conjunto de dimensionamento
Quando você cria um conjunto de dimensionamento no Portal, um balanceador de carga é criado. Regras de NAT (conversão de endereços de rede) são usadas para distribuir o tráfego para as instâncias do conjunto de dimensionamento para a conectividade remota, tais como RDP ou SSH.

Para exibir essas regras NAT e informações de conexão para as instâncias do conjunto de dimensionamento:

1. Selecione o grupo de recursos que você criou na etapa anterior, tal como *myResourceGroup*.
2. Na lista de recursos, selecione seu **Balanceador de carga**, tal como *myScaleSetLab*.
3. Escolha **Regras NAT de entrada** no menu à esquerda da janela.

    ![Regras NAT de entrada permitem que você se conecte a instâncias de conjunto de dimensionamento de máquinas virtuais](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

Você pode se conectar a cada VM no conjunto de escala usando estas regras NAT. Cada instância de VM lista um endereço IP de destino e um valor de porta TCP. Por exemplo, se o endereço IP de destino é *104.42.1.19* e a porta TCP é *50001*, você se conecta à instância de VM da seguinte maneira:

- Para um conjunto de dimensionamento do Windows, conecte-se à instância de VM com RDP em `104.42.1.19:50001`
- Para um conjunto de dimensionamento do Linux, conecte-se à instância de VM com SSH em `ssh azureuser@104.42.1.19 -p 50001`

Quando solicitado, insira as credenciais especificadas na etapa anterior, quando você criou o conjunto de dimensionamento. As instâncias do conjunto de dimensionamento são VMs regulares com as quais você pode interagir normalmente. Para obter mais informações sobre como implantar e executar aplicativos nas instâncias do conjunto de dimensionamento, consulte [Implantar o aplicativo em conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>Limpar recursos
Quando o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da VM e clique em **Excluir**.


## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criará um conjunto de dimensionamento básico no Portal do Azure. Para saber mais, siga para o tutorial sobre como criar e gerenciar um conjunto de dimensionamento de máquinas virtuais do Azure.

> [!div class="nextstepaction"]
> [Criar e gerenciar um conjunto de dimensionamento de máquinas virtuais do Azure](tutorial-create-and-manage-powershell.md)
