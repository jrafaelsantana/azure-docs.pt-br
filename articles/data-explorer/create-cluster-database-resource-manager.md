---
title: Criar um cluster de Data Explorer do Azure e um banco de dados usando um modelo de Azure Resource Manager
description: Saiba como criar um cluster de Data Explorer do Azure e um banco de dados usando um modelo de Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: oflipman
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: e2e051db00c9b8de5268e64be70ab99752bf7a55
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001422"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Criar um cluster de Data Explorer do Azure e um banco de dados usando um modelo de Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo de ARM](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. 

Neste artigo, você cria um cluster de Data Explorer do Azure e um banco de dados usando um [modelo de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). O artigo mostra como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades. Para obter informações sobre como criar modelos, consulte Criando [modelos de Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates). Para obter a sintaxe JSON e as propriedades a serem usadas em um modelo, consulte [tipos de recursos Microsoft. Kusto](/azure/templates/microsoft.kusto/allversions).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Modelo de Azure Resource Manager para criação de banco de dados e cluster

Neste artigo, você usa um [modelo de início rápido existente](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-05-15",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-05-15",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Para encontrar mais exemplos de modelo, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Implantar o modelo e verificar a implantação do modelo

Você pode implantar o modelo de Azure Resource Manager [usando o portal do Azure](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) ou [usando o PowerShell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Usar o portal do Azure para implantar o modelo e verificar a implantação do modelo

1. Para criar um cluster e um banco de dados, use o botão a seguir para iniciar a implantação. Clique com o botão direito do mouse e selecione **Abrir em nova janela** para que você possa acompanhar o restante das etapas neste artigo.

    [![Implantar no Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-kusto-cluster-database/azuredeploy.json)

    O botão **Implantar no Azure** leva você ao portal do Azure para preencher um formulário de implantação.

    ![Implantar no Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Você pode [Editar e implantar o modelo no portal do Azure](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) usando o formulário.

1. Conclua as seções **básicas** e **configurações** . Selecione nomes de banco de dados e cluster exclusivos.
Leva alguns minutos para criar um cluster de Data Explorer do Azure e um banco de dados.

1. Para verificar a implantação, abra o grupo de recursos no [portal do Azure](https://portal.azure.com) para localizar o novo cluster e o banco de dados. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Usar o PowerShell para implantar o modelo e verificar a implantação do modelo

#### <a name="deploy-the-template-using-powershell"></a>Implantar o modelo usando o PowerShell

1. Selecione **Experimente** no seguinte bloco de código e siga as instruções para entrar no Azure cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add(“clusters_kustocluster_name”, $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Selecione **Copiar** para copiar o script do PowerShell.
1. Clique com o botão direito do mouse no console do Shell e selecione **colar**.
Leva alguns minutos para criar um cluster de Data Explorer do Azure e um banco de dados.

#### <a name="verify-the-deployment-using-powershell"></a>Verificar a implantação usando o PowerShell

Para verificar a implantação, use o seguinte script de Azure PowerShell.  Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira linha (Read-Host). Para obter mais informações sobre como gerenciar recursos de Data Explorer do Azure no PowerShell, leia [AZ. Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Limpar recursos usando o portal do Azure

Exclua os recursos no portal do Azure seguindo as etapas em [limpar recursos](create-cluster-database-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Limpar recursos usando o PowerShell

Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

[Ingerir dados no cluster de Data Explorer do Azure e no banco de dados](ingest-data-overview.md)
