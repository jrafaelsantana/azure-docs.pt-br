---
title: Adicionar ou remover uma delegação de sub-rede em uma rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como adicionar ou remover uma sub-rede delegada para um serviço no Azure no Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 5fa340fc3c839d74f292f551b73184ea4df1c0f1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175958"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Adicionar ou remover uma delegação de sub-rede

A delegação de sub-rede dá permissões explícitas ao serviço para criar recursos específicos do serviço na sub-rede, usando um identificador exclusivo ao implantar o serviço. Este artigo descreve como adicionar ou remover uma sub-rede delegada para um serviço do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e a sub-rede que será delegada posteriormente a um serviço do Azure.

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual**.
1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | NOME | Insira *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.0.0.0/16*. |
    | Assinatura | Selecione sua assinatura.|
    | Grupo de recursos | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Location | Selecione **lesteus**.|
    | Sub-rede – Nome | Insira *mySubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.0.0.0/24*. |
    |||
1. Deixe o restante como padrão e, em seguida, selecione **criar**.

## <a name="permissons"></a>Permissões

Se você não criou a sub-rede que deseja delegar a um serviço do Azure, precisará da seguinte permissão: `Microsoft.Network/virtualNetworks/subnets/write`.

A função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) interna também contém as permissões necessárias.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Delegar uma sub-rede a um serviço do Azure

Nesta seção, você delega a sub-rede que criou na seção anterior para um serviço do Azure.

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **sub-redes**, em **configurações**e, em seguida, selecione **mysubnet**.
4. Na página *mysubnet* , para a lista **delegação de sub-rede** , selecione os serviços listados em **delegar sub-rede para um serviço** (por exemplo, **Microsoft. DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Remover a delegação de sub-rede de um serviço do Azure

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **sub-redes**, em **configurações**e, em seguida, selecione **mysubnet**.
4. Na página *mysubnet* , para a lista **delegação de sub-rede** , selecione **nenhum** dos serviços listados em **delegar sub-rede a um serviço**. 

## <a name="next-steps"></a>Próximas etapas
- Saiba como [gerenciar sub-redes no Azure](virtual-network-manage-subnet.md).
