---
title: 'Criar um gateway de VPN do Azure baseado em rota: CLI | Microsoft Docs'
description: Saiba como criar rapidamente um Gateway de VPN usando CLI
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: f5f62a6bfa1baa205e0496dd901f1f1eef660079
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391099"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Criar um Gateway de VPN baseado em rota usando CLI

Este artigo ajuda-o a criar rapidamente um Gateway de VPN do Azure baseado em rota usando a CLI do Azure. Um gateway de VPN é usado ao criar uma conexão VPN à rede local. Você também pode usar um gateway de VPN para se conectar a VNets.

As etapas neste artigo criarão uma rede virtual, uma sub-rede, uma sub-rede do gateway e um gateway VPN com base em rota (gateway de rede virtual). Um gateway de rede virtual pode levar 45 minutos ou mais para ser criado. Concluída a criação de gateway, você pode criar conexões. Estas etapas exigem uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão instalada, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Criar uma rede virtual

Crie uma rede virtual usando o comando [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual padrão chamada **VNet1** no local **EastUS**:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Adicione uma sub-rede de gateway

A sub-rede de gateway contém os endereços IP reservados que usam os serviços de gateway de rede virtual. Use os exemplos a seguir para adicionar uma sub-rede de gateway:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Solicite um endereço IP público

Um gateway de VPN deve ter um endereço IP público alocado dinamicamente. O endereço IP público será alocado ao gateway de VPN que criado para rede virtual. Use o exemplo a seguir para solicitar um endereço IP público chamado:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Criar o gateway de VPN

Criar o gateway de VPN usando o comando [az network vnet-gateway create](/cli/azure/group).

Se você executar esse comando usando o parâmetro `--no-wait`, não receberá nenhum feedback ou saída. O parâmetro `--no-wait` permite que o gateway seja criado em segundo plano. Isso não significa que a criação do gateway de VPN ocorrerá imediatamente.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Pode demorar 45 minutos ou mais para que o gateway VPN seja criado.

## <a name="viewgw"></a>Veja o Gateway de VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

A resposta é semelhante a esta:

```
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Exibir o endereço IP público

Para exibir o endereço IP público atribuído ao gateway, use o exemplo a seguir:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

O valor associado ao campo **ipAddress** é o endereço IP público do gateway de VPN.

Resposta de exemplo:

```
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```
## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos criados não forem mais necessários, use [az group delete](/cli/azure/group) para excluir o grupo de recursos. Isso excluirá o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Próximas etapas

Uma vez que o gateway tenha sido criado, você pode criar uma conexão entre sua rede virtual e outra rede virtual. Ou criar uma conexão entre sua rede virtual e redes locais.

> [!div class="nextstepaction"]
> [Criar uma conexão site a site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Criar uma conexão ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Criar uma conexão com outra rede virtual](vpn-gateway-vnet-vnet-rm-ps.md)
