---
title: Desabilitar políticas de rede para pontos de extremidade privados no Azure
description: Saiba como desabilitar as políticas de rede para pontos de extremidade privados.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: b510b697fbf8b53e9e55e96f60b27967f90893f1
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104616"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Desabilitar políticas de rede para pontos de extremidade privados

Não há suporte para as políticas de rede, como NSG (grupos de segurança de rede) para pontos de extremidade privados. Para implantar um ponto de extremidade privado em uma determinada sub-rede, uma configuração de desabilitação explícita é necessária nessa sub-rede. Essa configuração é aplicável somente para o ponto de extremidade privado. Para outros recursos na sub-rede, o acesso é controlado com base na definição de regras de segurança de NSG (grupos de segurança de rede). 
 
Ao usar o portal para criar um ponto de extremidade privado, essa configuração é automaticamente desabilitada como parte do processo de criação. A implantação usando outros clientes requer uma etapa adicional para alterar essa configuração. Você pode desabilitar a configuração usando o Cloud shell do portal do Azure, ou instalações locais do Azure PowerShell, CLI do Azure ou usar modelos de Azure Resource Manager.  
 
Os exemplos a seguir descrevem como desabilitar `PrivateEndpointNetworkPolicies` o para uma rede virtual chamada *myVirtualNetwork* com uma sub-rede *padrão* hospedada em um grupo de recursos chamado *MyResource*Group.

## <a name="using-azure-powershell"></a>Usando o PowerShell do Azure
Esta seção descreve como desabilitar políticas de ponto de extremidade privadas de sub-rede usando Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Usando a CLI do Azure
Esta seção descreve como desabilitar políticas de ponto de extremidade privadas de sub-rede usando CLI do Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>Criação de um modelo
Esta seção descreve como desabilitar políticas de ponto de extremidade privadas de sub-rede usando Azure Resource Manager modelo.
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [ponto de extremidade privado do Azure](private-endpoint-overview.md)
 
