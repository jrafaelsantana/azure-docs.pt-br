---
title: Usar um modelo de Azure Resource Manager para criar um espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como usar um modelo de Azure Resource Manager para criar um novo espaço de trabalho Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7e0897f92dd5ead939cbae9d6bf269bd22152419
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034776"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Use um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning

Neste artigo, você aprende várias maneiras de criar um espaço de trabalho do Azure Machine Learning usando modelos de Azure Resource Manager. Um modelo do Resource Manager facilita a criação de recursos como uma operação única e coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Além disso, pode especificar os parâmetros de implantação. Os parâmetros são usados para fornecer valores de entrada ao usar o modelo.

Para saber mais, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Se você não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar um modelo a partir de uma CLI, você precisará do [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O seguinte modelo do Resource Manager pode ser usado para criar um espaço de trabalho Azure Machine Learning e recursos do Azure associados:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Esse modelo cria os seguintes serviços do Azure:

* Grupo de Recursos do Azure
* Conta de Armazenamento do Azure
* Azure Key Vault
* Azure Application Insights
* Registro de Contêiner do Azure
* Workspace do Azure Machine Learning

O grupo de recursos é o contêiner que retém os serviços. Os diversos serviços são exigidos pelo workspace do Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* A **localização** onde o grupo de recursos e serviços serão criados.

    O modelo usará a localização selecionada para a maioria dos recursos. A exceção é o serviço do Application Insights que não está disponível em todos os locais de disponibilidade dos outros serviços. Se você selecionar uma localização onde não esteja disponível, o serviço será criado na localização Centro-Sul dos EUA.

* O **nome do workspace** é o nome amigável do workspace do Azure Machine Learning.

    Os nomes dos outros serviços são gerados aleatoriamente.

> [!TIP]
> Embora o modelo associado a este documento crie um novo registro de contêiner do Azure, você também pode criar um novo espaço de trabalho sem criar um registro de contêiner. Se no registro de contêiner estiver presente no espaço de trabalho, um será criado quando você executar uma operação que requer um registro de contêiner. Por exemplo, treinar ou implantar um modelo.
>
> Você também pode fazer referência a um registro de contêiner ou conta de armazenamento existente no modelo Azure Resource Manager, em vez de criar um novo.

Para obter mais informações sobre modelos, consulte os artigos a seguir:

* [Criar modelos do Gerenciador de Recursos do Azure](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Implantar um aplicativo com o modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Tipos de recursos do Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Siga as etapas em [Implantar recursos do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Ao acessar a tela __Editar modelo__, cole o modelo deste documento.
1. Selecione __Salvar__ para usar o modelo. Forneça as informações a seguir e concorde com os termos e condições listados:

   * Assinatura: Selecione a assinatura do Azure a ser usada para esses recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Nome do workspace: O nome a ser usado para o workspace do Azure Machine Learning que será criado. O nome do workspace deverá ter entre 3 e 33 caracteres. E o nome poderá conter apenas caracteres alfanuméricos e '-'.
   * Localização: Selecione a localização onde os recursos serão criados.

Para obter mais informações, consulte [Implantar recursos de modelo personalizado](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Este exemplo assume que você salvou o modelo em um arquivo nomeado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e do Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [implantar modelo do Resource Manager privado com o token SAS e o Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Usar a CLI do Azure

Este exemplo assume que você salvou o modelo em um arquivo nomeado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e da CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) e [implantar modelo do Resource Manager privado com o token SAS e a CLI do Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso de Azure Key Vault e modelos de Azure Resource Manager

Quando você usa um modelo de Azure Resource Manager para criar o espaço de trabalho e os recursos associados (incluindo Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínua.

A maioria das operações de criação de recursos por meio de modelos é idempotente, mas Key Vault limpa as políticas de acesso toda vez que o modelo é usado. Limpar as políticas de acesso interrompe o acesso ao Key Vault para qualquer espaço de trabalho existente que o esteja usando. Por exemplo, parar/criar funcionalidades de Azure Notebooks VM pode falhar.  

Para evitar esse problema, recomendamos uma das seguintes abordagens:

*  Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.
  
* Examine as políticas de acesso do Key Vault e use essas políticas para definir a propriedade accessPolicies do modelo.
* Verifique se o recurso de Key Vault já existe. Se tiver, não a recrie por meio do modelo. Por exemplo, adicione um parâmetro que permita desabilitar a criação do recurso de Key Vault se ele já existir.

## <a name="next-steps"></a>Próximas etapas

* [Implantar recursos com modelos do Resource Manager e API REST do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Criar e implantar grupos de recursos do Azure por meio do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
